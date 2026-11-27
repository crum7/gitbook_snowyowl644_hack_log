![](https://i.imgur.com/MFFHLmO.png)


- URL : https://app.hackthebox.com/machines/Bruno
- #medium 
- OS :  #Windows
- Machine Author(s): [xct](https://app.hackthebox.com/users/13569)
- Hack Date: 2025-11-19,21:09

# Enumeration
Principle
1. **目に見えるものだけがすべてではない。** あらゆる視点を考慮しろ
2. 見えていることと、見えていないことを区別しろ
3. **常に情報を得る手段は存在する。** 対象をしっかり理解しろ

## Nmap

| **ポート**   | **サービス**         | **バージョン**                     | **その他**                                                                             |
| --------- | ---------------- | ----------------------------- | ----------------------------------------------------------------------------------- |
| 21/tcp    | **ftp**          | Microsoft ftpd                | Anonymous FTP login allowed／app, benign, malicious, queue ディレクトリあり／SYST: Windows_NT |
| 53/tcp    | **domain**       | Simple DNS Plus               |                                                                                     |
| 80/tcp    | **http**         | Microsoft IIS httpd 10.0      | HTTP Title: IIS Windows Server／TRACE 可                                              |
| 88/tcp    | **kerberos-sec** | Microsoft Windows Kerberos    | Server time: 2025-11-19                                                             |
| 135/tcp   | msrpc            | Microsoft Windows RPC         |                                                                                     |
| 139/tcp   | netbios-ssn      | Microsoft Windows netbios-ssn |                                                                                     |
| 389/tcp   | **ldap**         | AD LDAP                       | 独自 CA 証明書／SAN: brunodc.bruno.vl 等                                                   |
| 443/tcp   | **https**        | Microsoft IIS httpd 10.0      | HTTPS／TRACE 可／TLS cert: 独自 CA                                                       |
| 445/tcp   | **SMB**          | —                             | SMB                                                                                 |
| 464/tcp   | kpasswd5         | —                             |                                                                                     |
| 593/tcp   | ncacn_http       | Windows RPC over HTTP 1.0     |                                                                                     |
| 636/tcp   | **ldaps**        | LDAP over SSL                 | 独自 CA 証明書                                                                           |
| 3268/tcp  | ldap             | Global Catalog LDAP           | AD GC／独自 CA                                                                         |
| 3269/tcp  | ssl/ldap         | Global Catalog LDAP over SSL  | AD GC／独自 CA                                                                         |
| 3389/tcp  | **RDP**          | Microsoft Terminal Services   | RDP (証明書あり)／NTLM 情報取得可                                                              |
| 9389/tcp  | ADWS             | .NET Message Framing          |                                                                                     |
| 49664/tcp | msrpc            | Microsoft Windows RPC         |                                                                                     |
| 49668/tcp | msrpc            | Microsoft Windows RPC         |                                                                                     |
| 52886/tcp | ncacn_http       | Windows RPC over HTTP 1.0     |                                                                                     |
| 52887/tcp | msrpc            | Microsoft Windows RPC         |                                                                                     |
| 52957/tcp | msrpc            | Microsoft Windows RPC         |                                                                                     |
| 52970/tcp | msrpc            | Microsoft Windows RPC         |                                                                                     |

```bash
PORT      STATE SERVICE       REASON          VERSION
21/tcp    open  ftp           syn-ack ttl 127 Microsoft ftpd
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| 06-29-22  04:55PM       <DIR>          app
| 06-29-22  04:33PM       <DIR>          benign
| 06-29-22  01:41PM       <DIR>          malicious
|_06-29-22  04:33PM       <DIR>          queue
| ftp-syst: 
|_  SYST: Windows_NT
53/tcp    open  domain        syn-ack ttl 127 Simple DNS Plus
80/tcp    open  http          syn-ack ttl 127 Microsoft IIS httpd 10.0
|_http-title: IIS Windows Server
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
88/tcp    open  kerberos-sec  syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2025-11-19 12:13:02Z)
135/tcp   open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 127 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: bruno.vl0., Site: Default-First-Site-Name)
| ssl-cert: Subject: 
| Subject Alternative Name: DNS:brunodc.bruno.vl, DNS:bruno.vl, DNS:BRUNO
| Issuer: commonName=bruno-BRUNODC-CA/domainComponent=bruno
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-10-09T09:54:08
| Not valid after:  2105-10-09T09:54:08
| MD5:   e92b:7496:6c9a:3a81:62eb:4ea4:58e0:20d3
| SHA-1: 855d:c331:c896:ab01:fa20:6c8a:5fd1:dfe8:402b:1a93
| -----BEGIN CERTIFICATE-----
|_-----END CERTIFICATE-----
|_ssl-date: 2025-11-19T12:14:41+00:00; 0s from scanner time.
443/tcp   open  ssl/http      syn-ack ttl 127 Microsoft IIS httpd 10.0
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=bruno-BRUNODC-CA/domainComponent=bruno
| Issuer: commonName=bruno-BRUNODC-CA/domainComponent=bruno
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2022-06-29T13:23:01
| Not valid after:  2121-06-29T13:33:00
| MD5:   659b:3c90:00eb:1e0a:5170:1be9:0456:840c
| SHA-1: a093:f4c2:3c8e:0532:86f2:1e99:cad7:82f8:e40e:3d72
| -----BEGIN CERTIFICATE-----
|_-----END CERTIFICATE-----
|_http-title: IIS Windows Server
| tls-alpn: 
|_  http/1.1
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
445/tcp   open  microsoft-ds? syn-ack ttl 127
464/tcp   open  kpasswd5?     syn-ack ttl 127
593/tcp   open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  ssl/ldap      syn-ack ttl 127
| ssl-cert: Subject: 
| Subject Alternative Name: DNS:brunodc.bruno.vl, DNS:bruno.vl, DNS:BRUNO
| Issuer: commonName=bruno-BRUNODC-CA/domainComponent=bruno
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-10-09T09:54:08
| Not valid after:  2105-10-09T09:54:08
| MD5:   e92b:7496:6c9a:3a81:62eb:4ea4:58e0:20d3
| SHA-1: 855d:c331:c896:ab01:fa20:6c8a:5fd1:dfe8:402b:1a93
| -----BEGIN CERTIFICATE-----
|_-----END CERTIFICATE-----
|_ssl-date: 2025-11-19T12:14:41+00:00; 0s from scanner time.
3268/tcp  open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: bruno.vl0., Site: Default-First-Site-Name)
| ssl-cert: Subject: 
| Subject Alternative Name: DNS:brunodc.bruno.vl, DNS:bruno.vl, DNS:BRUNO
| Issuer: commonName=bruno-BRUNODC-CA/domainComponent=bruno
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-10-09T09:54:08
| Not valid after:  2105-10-09T09:54:08
| MD5:   e92b:7496:6c9a:3a81:62eb:4ea4:58e0:20d3
| SHA-1: 855d:c331:c896:ab01:fa20:6c8a:5fd1:dfe8:402b:1a93
| -----BEGIN CERTIFICATE-----
|_-----END CERTIFICATE-----
|_ssl-date: 2025-11-19T12:14:41+00:00; 0s from scanner time.
3269/tcp  open  ssl/ldap      syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: bruno.vl0., Site: Default-First-Site-Name)
| ssl-cert: Subject: 
| Subject Alternative Name: DNS:brunodc.bruno.vl, DNS:bruno.vl, DNS:BRUNO
| Issuer: commonName=bruno-BRUNODC-CA/domainComponent=bruno
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-10-09T09:54:08
| Not valid after:  2105-10-09T09:54:08
| MD5:   e92b:7496:6c9a:3a81:62eb:4ea4:58e0:20d3
| SHA-1: 855d:c331:c896:ab01:fa20:6c8a:5fd1:dfe8:402b:1a93
| -----BEGIN CERTIFICATE-----
|_-----END CERTIFICATE-----
|_ssl-date: 2025-11-19T12:14:41+00:00; 0s from scanner time.
3389/tcp  open  ms-wbt-server syn-ack ttl 127 Microsoft Terminal Services
| ssl-cert: Subject: commonName=brunodc.bruno.vl
| Issuer: commonName=brunodc.bruno.vl
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-10-08T09:36:40
| Not valid after:  2026-04-09T09:36:40
| MD5:   8821:8264:b724:2189:d3ba:0ce6:c157:3984
| SHA-1: 66b0:d87c:3afc:5d2a:6a1e:8240:21fb:cef2:b90a:e5a5
| -----BEGIN CERTIFICATE-----
|_-----END CERTIFICATE-----
|_ssl-date: 2025-11-19T12:14:41+00:00; 0s from scanner time.
| rdp-ntlm-info: 
|   Target_Name: BRUNO
|   NetBIOS_Domain_Name: BRUNO
|   NetBIOS_Computer_Name: BRUNODC
|   DNS_Domain_Name: bruno.vl
|   DNS_Computer_Name: brunodc.bruno.vl
|   DNS_Tree_Name: bruno.vl
|   Product_Version: 10.0.20348
|_  System_Time: 2025-11-19T12:14:02+00:00
9389/tcp  open  mc-nmf        syn-ack ttl 127 .NET Message Framing
49664/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49668/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
52886/tcp open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
52887/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
52957/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
52970/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running (JUST GUESSING): Microsoft Windows 2022|2012|2016 (89%)
OS CPE: cpe:/o:microsoft:windows_server_2022 cpe:/o:microsoft:windows_server_2012:r2 cpe:/o:microsoft:windows_server_2016
OS fingerprint not ideal because: Missing a closed TCP port so results incomplete
Aggressive OS guesses: Microsoft Windows Server 2022 (89%), Microsoft Windows Server 2012 R2 (85%), Microsoft Windows Server 2016 (85%)
No exact OS matches for host (test conditions non-ideal).
TCP/IP fingerprint:
SCAN(V=7.95%E=4%D=11/19%OT=21%CT=%CU=%PV=Y%DS=2%DC=T%G=N%TM=691DB4B6%P=aarch64-unknown-linux-gnu)
SEQ(SP=103%GCD=1%ISR=10C%TI=I%II=I%TS=A)
SEQ(SP=105%GCD=1%ISR=10D%TI=I%II=I%SS=S%TS=A)
OPS(O1=M542NW8ST11%O2=M542NW8ST11%O3=M542NW8NNT11%O4=M542NW8ST11%O5=M542NW8ST11%O6=M542ST11)
WIN(W1=FFFF%W2=FFFF%W3=FFFF%W4=FFFF%W5=FFFF%W6=FFDC)
ECN(R=Y%DF=Y%TG=80%W=FFFF%O=M542NW8NNS%CC=Y%Q=)
T1(R=Y%DF=Y%TG=80%S=O%A=S+%F=AS%RD=0%Q=)
T2(R=N)
T3(R=N)
T4(R=N)
U1(R=N)
IE(R=Y%DFI=N%TG=80%CD=Z)

Uptime guess: 0.005 days (since Wed Nov 19 12:06:57 2025)
Network Distance: 2 hops
TCP Sequence Prediction: Difficulty=259 (Good luck!)
IP ID Sequence Generation: Incremental
Service Info: Host: BRUNODC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 42769/tcp): CLEAN (Timeout)
|   Check 2 (port 54436/tcp): CLEAN (Timeout)
|   Check 3 (port 64073/udp): CLEAN (Timeout)
|   Check 4 (port 46616/udp): CLEAN (Timeout)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked
|_clock-skew: mean: 0s, deviation: 0s, median: 0s
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
| smb2-time: 
|   date: 2025-11-19T12:14:04
|_  start_date: N/A

TRACEROUTE (using port 21/tcp)
HOP RTT       ADDRESS
1   171.77 ms 10.10.16.1
2   259.80 ms 10.129.25.101

```
SMB・FTPに匿名ログインできるのか試す
## enum4linux-ng
```bash
─$ enum4linux-ng -A  $Target_IP
ENUM4LINUX - next generation (v1.3.5)

 ==========================
|    Target Information    |
 ==========================
[*] Target ........... 10.129.25.101
[*] Username ......... ''
[*] Random Username .. 'yojjyqni'
[*] Password ......... ''
[*] Timeout .......... 5 second(s)

 ======================================
|    Listener Scan on 10.129.25.101    |
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
|    Domain Information via LDAP for 10.129.25.101    |
 =====================================================
[*] Trying LDAP
[+] Appears to be root/parent DC
[+] Long domain name is: bruno.vl

 ============================================================
|    NetBIOS Names and Workgroup/Domain for 10.129.25.101    |
 ============================================================
[-] Could not get NetBIOS names information via 'nmblookup': timed out

 ==========================================
|    SMB Dialect Check on 10.129.25.101    |
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
|    Domain Information via SMB session for 10.129.25.101    |
 ============================================================
[*] Enumerating via unauthenticated SMB session on 445/tcp
[+] Found domain information via SMB
NetBIOS computer name: BRUNODC
NetBIOS domain name: BRUNO
DNS domain: bruno.vl
FQDN: brunodc.bruno.vl
Derived membership: domain member                                                                                                                                                                                                          
Derived domain: BRUNO                                                                                                                                                                                                                      

 ==========================================
|    RPC Session Check on 10.129.25.101    |
 ==========================================
[*] Check for anonymous access (null session)
[+] Server allows authentication via username '' and password ''
[*] Check for guest access
[-] Could not establish guest session: STATUS_LOGON_FAILURE

 ====================================================
|    Domain Information via RPC for 10.129.25.101    |
 ====================================================
[+] Domain: BRUNO
[+] Domain SID: S-1-5-21-1536375944-4286418366-3447278137
[+] Membership: domain member

 ================================================
|    OS Information via RPC for 10.129.25.101    |
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
|    Users via RPC on 10.129.25.101    |
 ======================================
[*] Enumerating users via 'querydispinfo'
[-] Could not find users via 'querydispinfo': STATUS_ACCESS_DENIED
[*] Enumerating users via 'enumdomusers'
[-] Could not find users via 'enumdomusers': STATUS_ACCESS_DENIED

 =======================================
|    Groups via RPC on 10.129.25.101    |
 =======================================
[*] Enumerating local groups
[-] Could not get groups via 'enumalsgroups domain': STATUS_ACCESS_DENIED
[*] Enumerating builtin groups
[-] Could not get groups via 'enumalsgroups builtin': STATUS_ACCESS_DENIED
[*] Enumerating domain groups
[-] Could not get groups via 'enumdomgroups': STATUS_ACCESS_DENIED

 =======================================
|    Shares via RPC on 10.129.25.101    |
 =======================================
[*] Enumerating shares
[+] Found 0 share(s) for user '' with password '', try a different user

 ==========================================
|    Policies via RPC for 10.129.25.101    |
 ==========================================
[*] Trying port 445/tcp
[-] SMB connection error on port 445/tcp: STATUS_ACCESS_DENIED
[*] Trying port 139/tcp
[-] SMB connection error on port 139/tcp: session failed

 ==========================================
|    Printers via RPC for 10.129.25.101    |
 ==========================================
[-] Could not get printer info via 'enumprinters': STATUS_ACCESS_DENIED

Completed after 35.60 seconds

```
DNS domain: bruno.vl
FQDN: brunodc.bruno.vl
SMBは匿名ログインできなそう

一応nxcでもSMBの確認
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/Bruno]
└─$ nxc smb 10.129.25.101 -u 'guest' -p '' --shares
SMB         10.129.25.101   445    BRUNODC          [*] Windows Server 2022 Build 20348 x64 (name:BRUNODC) (domain:bruno.vl) (signing:True) (SMBv1:False) 
SMB         10.129.25.101   445    BRUNODC          [-] bruno.vl\guest: STATUS_ACCOUNT_DISABLED 
```

## FTP
認証情報わからず
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/Bruno]
└─$ ftp -p $Target_IP
Connected to 10.129.25.101.
220 Microsoft FTP Service
Name (10.129.25.101:kali): 
421 Service not available, remote server has closed connection.
ftp: Login failed

```
## Feroxbuster
```bash
feroxbuster -u https://10.129.238.9/ -C 404 -C 500
```

## Dirsearch
```bash

```

## FFUF
```bash

```

## WebSite
- 
- 
- 
## WebSite Screenshot


# Foothold

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






# Privilege Escalation

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







## Notes



<このWriteupで特に重要な点や学んだことを追加で記載するセクション>

