![](https://i.imgur.com/MFFHLmO.png)


- URL : https://app.hackthebox.com/machines/474
-  #medium 
- OS :  #Windows
- Machine Author(s):   Created by [JDgodd &](https://app.hackthebox.com/users/481778) [nikk37](https://app.hackthebox.com/users/247264)
- Hack Date: 2025-08-21,15:28

# Enumeration
Principle
1. **目に見えるものだけがすべてではない。** あらゆる視点を考慮しろ
2. 見えていることと、見えていないことを区別しろ
3. **常に情報を得る手段は存在する。** 対象をしっかり理解しろ


## nmap
ドメイン
- watch.streamIO.htb
- streamIO.htb

| **ポート**   | **サービス**     | **バージョン**                               | **その他**                                                                                                                 |
| --------- | ------------ | --------------------------------------- | ----------------------------------------------------------------------------------------------------------------------- |
| 53/tcp    | DNS          | Simple DNS Plus                         |                                                                                                                         |
| 80/tcp    | **http**     | Microsoft IIS httpd 10.0                | http-title: IIS Windows Serverhttp-server-header: Microsoft-IIS/10.0TRACE メソッド有                                         |
| 88/tcp    | **kerberos** | Microsoft Windows Kerberos              | server time: 2025-08-21 13:46:06Z                                                                                       |
| 135/tcp   | msrpc        | Microsoft Windows RPC                   |                                                                                                                         |
| 139/tcp   | netbios-ssn  | Microsoft Windows netbios-ssn           |                                                                                                                         |
| 443/tcp   | **HTTPS**    | Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP) | SSL証明書: CN=streamIO, DNS:streamIO.htb, watch.streamIO.htbhttp-title: Not Foundhttp-server-header: Microsoft-HTTPAPI/2.0 |
| 445/tcp   | **SMB**      | ?                                       |                                                                                                                         |
| 464/tcp   | kpasswd5     | ?                                       |                                                                                                                         |
| 593/tcp   | ncacn_http   | Microsoft Windows RPC over HTTP 1.0     |                                                                                                                         |
| 636/tcp   | tcpwrapped   | -                                       |                                                                                                                         |
| 3268/tcp  | **LDAP**     | Microsoft Windows Active Directory LDAP | Domain: streamIO.htb0Site: Default-First-Site-Name                                                                      |
| 3269/tcp  | tcpwrapped   | -                                       |                                                                                                                         |
| 5985/tcp  | **WINRM      | Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP) | http-title: Not Foundhttp-server-header: Microsoft-HTTPAPI/2.0                                                          |
| 9389/tcp  | mc-nmf       | .NET Message Framing                    |                                                                                                                         |
| 49667/tcp | msrpc        | Microsoft Windows RPC                   |                                                                                                                         |
| 49677/tcp | ncacn_http   | Microsoft Windows RPC over HTTP 1.0     |                                                                                                                         |
| 49678/tcp | msrpc        | Microsoft Windows RPC                   |                                                                                                                         |
| 49707/tcp | msrpc        | Microsoft Windows RPC                   |                                                                                                                         |
| 49734/tcp | msrpc        | Microsoft Windows RPC                   |                                                                                                                         |


```bash
PORT      STATE SERVICE       REASON          VERSION
53/tcp    open  domain        syn-ack ttl 127 Simple DNS Plus
80/tcp    open  http          syn-ack ttl 127 Microsoft IIS httpd 10.0
|_http-title: IIS Windows Server
|_http-server-header: Microsoft-IIS/10.0
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
88/tcp    open  kerberos-sec  syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2025-08-21 13:46:06Z)
135/tcp   open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 127 Microsoft Windows netbios-ssn
443/tcp   open  ssl/http      syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_ssl-date: 2025-08-21T13:47:40+00:00; +7h00m00s from scanner time.
| tls-alpn: 
|_  http/1.1
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
| ssl-cert: Subject: commonName=streamIO/countryName=EU
| Subject Alternative Name: DNS:streamIO.htb, DNS:watch.streamIO.htb
| Issuer: commonName=streamIO/countryName=EU
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2022-02-22T07:03:28
| Not valid after:  2022-03-24T07:03:28
| MD5:   b99a:2c8d:a0b8:b10a:eefa:be20:4abd:ecaf
| SHA-1: 6c6a:3f5c:7536:61d5:2da6:0e66:75c0:56ce:56e4:656d
| -----BEGIN CERTIFICATE-----
| MIIDYjCCAkqgAwIBAgIUbdDRZxR55nbfMxJzBHWVXcH83kQwDQYJKoZIhvcNAQEL
| BQAwIDELMAkGA1UEBhMCRVUxETAPBgNVBAMMCHN0cmVhbUlPMB4XDTIyMDIyMjA3
| MDMyOFoXDTIyMDMyNDA3MDMyOFowIDELMAkGA1UEBhMCRVUxETAPBgNVBAMMCHN0
| cmVhbUlPMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEA2QSO8noWDU+A
| MYuhSMrB2mA+V7W2gwMdTHxYK0ausnBHdfQ4yGgAs7SdyYKXf8fA502x4LvYwgmd
| 67QtQdYtsTSv63SlnEW3zjJyu/dRW0cwMfBCqyiLgAScrxb/6HOhpnOAzk0DdBWE
| 2vobsSSAh+cDHVSuSbEBLqJ0GEL4hcggHhQq6HLRmmrb0wGjL1WIwjQ8cCWcFzzw
| 5Xe3gEe+aHK245qZKrZtHuXelFe72/nbF8VFiukkaBMgoh6VfpM66nMzy+KeLfhP
| FkxBt6osGUHwSnocJknc7t+ySRVTACAMPjbbPGEl4hvNEcZpepep6jD6qgi4k7bL
| 82Nu2AeSIQIDAQABo4GTMIGQMB0GA1UdDgQWBBRf0ALWCgvVfRgijR2I0KY0uRjY
| djAfBgNVHSMEGDAWgBRf0ALWCgvVfRgijR2I0KY0uRjYdjAPBgNVHRMBAf8EBTAD
| AQH/MCsGA1UdEQQkMCKCDHN0cmVhbUlPLmh0YoISd2F0Y2guc3RyZWFtSU8uaHRi
| MBAGA1UdIAQJMAcwBQYDKgMEMA0GCSqGSIb3DQEBCwUAA4IBAQCCAFvDk/XXswL4
| cP6nH8MEkdEU7yvMOIPp+6kpgujJsb/Pj66v37w4f3us53dcoixgunFfRO/qAjtY
| PNWjebXttLHER+fet53Mu/U8bVQO5QD6ErSYUrzW/l3PNUFHIewpNg09gmkY4gXt
| oZzGN7kvjuKHm+lG0MunVzcJzJ3WcLHQUcwEWAdSGeAyKTfGNy882YTUiAC3p7HT
| 61PwCI+lO/OU52VlgnItRHH+yexBTLRB+Oa2UhB7GnntQOR1S5g497Cs3yAciST2
| JaKhcCnBY1cWqUSAm56QK3mz55BNPcOUHLhrFLjIaWRVx8Ro8QOCWcxkTfVcKcR+
| DSJTOJH8
|_-----END CERTIFICATE-----
445/tcp   open  microsoft-ds? syn-ack ttl 127
464/tcp   open  kpasswd5?     syn-ack ttl 127
593/tcp   open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped    syn-ack ttl 127
3268/tcp  open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: streamIO.htb0., Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped    syn-ack ttl 127
5985/tcp  open  http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
9389/tcp  open  mc-nmf        syn-ack ttl 127 .NET Message Framing
49667/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49677/tcp open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
49678/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49707/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49734/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running (JUST GUESSING): Microsoft Windows 2019|10 (97%)
OS CPE: cpe:/o:microsoft:windows_server_2019 cpe:/o:microsoft:windows_10
OS fingerprint not ideal because: Missing a closed TCP port so results incomplete
Aggressive OS guesses: Windows Server 2019 (97%), Microsoft Windows 10 1903 - 21H1 (91%)
No exact OS matches for host (test conditions non-ideal).
TCP/IP fingerprint:
SCAN(V=7.95%E=4%D=8/20%OT=53%CT=%CU=%PV=Y%DS=2%DC=T%G=N%TM=68A6C10C%P=aarch64-unknown-linux-gnu)
SEQ(SP=104%GCD=1%ISR=106%TI=I%II=I%SS=S%TS=U)
SEQ(SP=107%GCD=1%ISR=10C%TI=I%II=I%SS=S%TS=U)
OPS(O1=M552NW8NNS%O2=M552NW8NNS%O3=M552NW8%O4=M552NW8NNS%O5=M552NW8NNS%O6=M552NNS)
WIN(W1=FFFF%W2=FFFF%W3=FFFF%W4=FFFF%W5=FFFF%W6=FF70)
ECN(R=Y%DF=Y%TG=80%W=FFFF%O=M552NW8NNS%CC=Y%Q=)
T1(R=Y%DF=Y%TG=80%S=O%A=S+%F=AS%RD=0%Q=)
T2(R=N)
T3(R=N)
T4(R=N)
U1(R=N)
IE(R=Y%DFI=N%TG=80%CD=Z)

Network Distance: 2 hops
TCP Sequence Prediction: Difficulty=260 (Good luck!)
IP ID Sequence Generation: Incremental
Service Info: Host: DC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 53151/tcp): CLEAN (Timeout)
|   Check 2 (port 12254/tcp): CLEAN (Timeout)
|   Check 3 (port 4278/udp): CLEAN (Timeout)
|   Check 4 (port 4810/udp): CLEAN (Timeout)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked
| smb2-time: 
|   date: 2025-08-21T13:47:03
|_  start_date: N/A
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
|_clock-skew: mean: 6h59m59s, deviation: 0s, median: 6h59m59s

TRACEROUTE (using port 443/tcp)
HOP RTT      ADDRESS
1   97.85 ms 10.10.14.1
2   98.01 ms 10.129.186.103

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 23:47
Completed NSE at 23:47, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 23:47
Completed NSE at 23:47, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 23:47
Completed NSE at 23:47, 0.00s elapsed
Read data files from: /usr/share/nmap
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 102.83 seconds
           Raw packets sent: 103 (8.240KB) | Rcvd: 51 (2.828KB)

```

## SMB
- 匿名ログインできるのか
	- できない
```bash
┌──(kali㉿kali)-[~/Desktop/HTB/machine/StreamIO]
└─$ smbclient -N -L //$Target_IP                                    
session setup failed: NT_STATUS_ACCESS_DENIED

```

## WebSite

- http://streamio.htb/
![](https://i.imgur.com/JgtmQLR.png)

- https://streamio.htb/
![](https://i.imgur.com/BaYfqhZ.png)

- `https://streamio.htb/about.php`
![](https://i.imgur.com/RAxkGH4.png)

- `https://streamio.htb/contact.php`
![](https://i.imgur.com/bNuRiZm.png)

- `https://streamio.htb/login.php`
	- admin : adminではログインできない
![](https://i.imgur.com/RmsnGj7.png)

- `https://streamio.htb/register.php`
![](https://i.imgur.com/VqgQzpG.png)

- `https://streamio.htb/register.php`
- admin : adminで作って、
![](https://i.imgur.com/oqk4aZh.png)

Account Createdって出るんだけど、ログインページに戻って、入力した認証情報を入れてもログインできない
![](https://i.imgur.com/FZDgorF.png)


- `https://watch.streamio.htb/`
![](https://i.imgur.com/IAIu7ns.png)

![](https://i.imgur.com/QApvBdS.png)

メールリストのところに、ipを入れて、SSRFが起きないかを確認する
![](https://i.imgur.com/cAe89yr.png)

respoinderを動かすも、画面の表示が変わるだけで、何も起きない
![](https://i.imgur.com/6GugI1j.png)



## Feroxbuster
```bash
┌──(kali㉿kali)-[~/Desktop/HTB/machine/StreamIO]
└─$ feroxbuster -u http://10.129.186.103/ 
                                                                                                                                                                               
 ___  ___  __   __     __      __         __   ___
|__  |__  |__) |__) | /  `    /  \ \_/ | |  \ |__
|    |___ |  \ |  \ | \__,    \__/ / \ | |__/ |___
by Ben "epi" Risher 🤓                 ver: 2.11.0
───────────────────────────┬──────────────────────
 🎯  Target Url            │ http://10.129.186.103/
 🚀  Threads               │ 50
 📖  Wordlist              │ /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt
 👌  Status Codes          │ All Status Codes!
 💥  Timeout (secs)        │ 7
 🦡  User-Agent            │ feroxbuster/2.11.0
 💉  Config File           │ /etc/feroxbuster/ferox-config.toml
 🔎  Extract Links         │ true
 🏁  HTTP methods          │ [GET]
 🔃  Recursion Depth       │ 4
───────────────────────────┴──────────────────────
 🏁  Press [ENTER] to use the Scan Management Menu™
──────────────────────────────────────────────────
404      GET       29l       95w     1245c Auto-filtering found 404-like response and created new filter; toggle off with --dont-filter
301      GET        2l       10w      159c http://10.129.186.103/aspnet_client => http://10.129.186.103/aspnet_client/
200      GET      334l     2089w   180418c http://10.129.186.103/iisstart.png


404      GET       40l      156w     1913c http://10.129.186.103/ASPNET_CLIENT/system_web/prn
[####################] - 3m    270027/270027  0s      found:46      errors:0      
[####################] - 76s    30000/30000   393/s   http://10.129.186.103/ 
[####################] - 77s    30000/30000   389/s   http://10.129.186.103/aspnet_client/ 
[####################] - 80s    30000/30000   375/s   http://10.129.186.103/Aspnet_client/ 
[####################] - 83s    30000/30000   360/s   http://10.129.186.103/aspnet_Client/ 
[####################] - 88s    30000/30000   340/s   http://10.129.186.103/aspnet_client/system_web/ 
[####################] - 88s    30000/30000   343/s   http://10.129.186.103/ASPNET_CLIENT/ 
[####################] - 2m     30000/30000   331/s   http://10.129.186.103/Aspnet_client/system_web/ 
[####################] - 86s    30000/30000   350/s   http://10.129.186.103/aspnet_Client/system_web/ 
[####################] - 80s    30000/30000   377/s   http://10.129.186.103/ASPNET_CLIENT/system_web/                                                                                                                                                                                         
```


```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/StreamIO]
└─$ feroxbuster -u https://streamIO.htb/ -k
                                                                                                                                                                               
 ___  ___  __   __     __      __         __   ___
|__  |__  |__) |__) | /  `    /  \ \_/ | |  \ |__
|    |___ |  \ |  \ | \__,    \__/ / \ | |__/ |___
by Ben "epi" Risher 🤓                 ver: 2.11.0
───────────────────────────┬──────────────────────
 🎯  Target Url            │ https://streamIO.htb/
 🚀  Threads               │ 50
 📖  Wordlist              │ /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt
 👌  Status Codes          │ All Status Codes!
 💥  Timeout (secs)        │ 7
 🦡  User-Agent            │ feroxbuster/2.11.0
 💉  Config File           │ /etc/feroxbuster/ferox-config.toml
 🔎  Extract Links         │ true
 🏁  HTTP methods          │ [GET]
 🔓  Insecure              │ true
 🔃  Recursion Depth       │ 4
───────────────────────────┴──────────────────────
 🏁  Press [ENTER] to use the Scan Management Menu™
──────────────────────────────────────────────────
404      GET       29l       95w     1245c Auto-filtering found 404-like response and created new filter; toggle off with --dont-filter
301      GET        2l       10w      150c https://streamio.htb/admin => https://streamio.htb/admin/
301      GET        2l       10w      151c https://streamio.htb/images => https://streamio.htb/images/
301      GET        2l       10w      147c https://streamio.htb/js => https://streamio.htb/js/
301      GET        2l       10w      148c https://streamio.htb/css => https://streamio.htb/css/
200      GET      206l      430w     6434c https://streamio.htb/contact.php
200      GET      192l     1006w    82931c https://streamio.htb/images/icon.png
200      GET        5l      374w    21257c https://streamio.htb/js/popper.min.js
200      GET      101l      173w     1663c https://streamio.htb/css/responsive.css
200      GET      395l      915w    13497c https://streamio.htb/index.php
200      GET      913l     5479w   420833c https://streamio.htb/images/about-img.png
200      GET        2l     1276w    88145c https://streamio.htb/js/jquery-3.4.1.min.js
200      GET      231l      571w     7825c https://streamio.htb/about.php
200      GET       51l      213w    19329c https://streamio.htb/images/client.jpg
200      GET      863l     1698w    16966c https://streamio.htb/css/style.css
200      GET      367l     1995w   166220c https://streamio.htb/images/contact-img.png
200      GET      111l      269w     4145c https://streamio.htb/login.php
200      GET      395l      915w    13497c https://streamio.htb/
301      GET        2l       10w      150c https://streamio.htb/Admin => https://streamio.htb/Admin/
301      GET        2l       10w      151c https://streamio.htb/Images => https://streamio.htb/Images/
301      GET        2l       10w      154c https://streamio.htb/admin/css => https://streamio.htb/admin/css/
301      GET        2l       10w      157c https://streamio.htb/admin/images => https://streamio.htb/admin/images/
301      GET        2l       10w      153c https://streamio.htb/admin/js => https://streamio.htb/admin/js/
301      GET        2l       10w      150c https://streamio.htb/fonts => https://streamio.htb/fonts/
301      GET        2l       10w      157c https://streamio.htb/Admin/images => https://streamio.htb/Admin/images/
301      GET        2l       10w      154c https://streamio.htb/Admin/css => https://streamio.htb/Admin/css/
301      GET        2l       10w      153c https://streamio.htb/Admin/js => https://streamio.htb/Admin/js/
301      GET        2l       10w      148c https://streamio.htb/CSS => https://streamio.htb/CSS/
301      GET        2l       10w      157c https://streamio.htb/admin/Images => https://streamio.htb/admin/Images/
301      GET        2l       10w      157c https://streamio.htb/Admin/Images => https://streamio.htb/Admin/Images/
301      GET        2l       10w      156c https://streamio.htb/admin/fonts => https://streamio.htb/admin/fonts/
301      GET        2l       10w      156c https://streamio.htb/Admin/fonts => https://streamio.htb/Admin/fonts/
301      GET        2l       10w      154c https://streamio.htb/admin/CSS => https://streamio.htb/admin/CSS/
301      GET        2l       10w      150c https://streamio.htb/ADMIN => https://streamio.htb/ADMIN/
301      GET        2l       10w      154c https://streamio.htb/Admin/CSS => https://streamio.htb/Admin/CSS/
301      GET        2l       10w      147c https://streamio.htb/JS => https://streamio.htb/JS/
301      GET        2l       10w      153c https://streamio.htb/ADMIN/js => https://streamio.htb/ADMIN/js/
301      GET        2l       10w      157c https://streamio.htb/ADMIN/images => https://streamio.htb/ADMIN/images/
301      GET        2l       10w      154c https://streamio.htb/ADMIN/css => https://streamio.htb/ADMIN/css/
301      GET        2l       10w      153c https://streamio.htb/admin/JS => https://streamio.htb/admin/JS/
301      GET        2l       10w      147c https://streamio.htb/Js => https://streamio.htb/Js/
301      GET        2l       10w      148c https://streamio.htb/Css => https://streamio.htb/Css/
301      GET        2l       10w      157c https://streamio.htb/ADMIN/Images => https://streamio.htb/ADMIN/Images/
301      GET        2l       10w      153c https://streamio.htb/Admin/JS => https://streamio.htb/Admin/JS/
301      GET        2l       10w      156c https://streamio.htb/ADMIN/fonts => https://streamio.htb/ADMIN/fonts/
301      GET        2l       10w      153c https://streamio.htb/admin/Js => https://streamio.htb/admin/Js/
301      GET        2l       10w      154c https://streamio.htb/ADMIN/CSS => https://streamio.htb/ADMIN/CSS/
301      GET        2l       10w      154c https://streamio.htb/admin/Css => https://streamio.htb/admin/Css/
301      GET        2l       10w      153c https://streamio.htb/Admin/Js => https://streamio.htb/Admin/Js/
301      GET        2l       10w      154c https://streamio.htb/Admin/Css => https://streamio.htb/Admin/Css/
301      GET        2l       10w      153c https://streamio.htb/ADMIN/JS => https://streamio.htb/ADMIN/JS/
301      GET        2l       10w      151c https://streamio.htb/IMAGES => https://streamio.htb/IMAGES/
301      GET        2l       10w      153c https://streamio.htb/ADMIN/Js => https://streamio.htb/ADMIN/Js/
301      GET        2l       10w      154c https://streamio.htb/ADMIN/Css => https://streamio.htb/ADMIN/Css/
301      GET        2l       10w      157c https://streamio.htb/admin/IMAGES => https://streamio.htb/admin/IMAGES/
301      GET        2l       10w      157c https://streamio.htb/Admin/IMAGES => https://streamio.htb/Admin/IMAGES/
301      GET        2l       10w      157c https://streamio.htb/ADMIN/IMAGES => https://streamio.htb/ADMIN/IMAGES/
404      GET        0l        0w     1245c https://streamio.htb/admin/CSS/pass

```


```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/StreamIO]
└─$ feroxbuster -u https://watch.streamio.htb/ -k
                                                                                                                                                                               
 ___  ___  __   __     __      __         __   ___
|__  |__  |__) |__) | /  `    /  \ \_/ | |  \ |__
|    |___ |  \ |  \ | \__,    \__/ / \ | |__/ |___
by Ben "epi" Risher 🤓                 ver: 2.11.0
───────────────────────────┬──────────────────────
 🎯  Target Url            │ https://watch.streamio.htb/
 🚀  Threads               │ 50
 📖  Wordlist              │ /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt
 👌  Status Codes          │ All Status Codes!
 💥  Timeout (secs)        │ 7
 🦡  User-Agent            │ feroxbuster/2.11.0
 💉  Config File           │ /etc/feroxbuster/ferox-config.toml
 🔎  Extract Links         │ true
 🏁  HTTP methods          │ [GET]
 🔓  Insecure              │ true
 🔃  Recursion Depth       │ 4
───────────────────────────┴──────────────────────
 🏁  Press [ENTER] to use the Scan Management Menu™
──────────────────────────────────────────────────
404      GET       29l       95w     1245c Auto-filtering found 404-like response and created new filter; toggle off with --dont-filter
200      GET      192l     1006w    82931c https://watch.streamio.htb/static/icon.png
200      GET       72l      112w      875c https://watch.streamio.htb/static/css/index.css
200      GET      136l      295w    22042c https://watch.streamio.htb/static/logo.png
200      GET       78l      245w     2829c https://watch.streamio.htb/
301      GET        2l       10w      157c https://watch.streamio.htb/static => https://watch.streamio.htb/static/
403      GET       29l       92w     1233c https://watch.streamio.htb/static/css/
403      GET       29l       92w     1233c https://watch.streamio.htb/static/
301      GET        2l       10w      160c https://watch.streamio.htb/static/js => https://watch.streamio.htb/static/js/
301      GET        2l       10w      161c https://watch.streamio.htb/static/css => https://watch.streamio.htb/static/css/
301      GET        2l       10w      161c https://watch.streamio.htb/static/CSS => https://watch.streamio.htb/static/CSS/
301      GET        2l       10w      160c https://watch.streamio.htb/static/JS => https://watch.streamio.htb/static/JS/
301      GET        2l       10w      160c https://watch.streamio.htb/static/Js => https://watch.streamio.htb/static/Js/
301      GET        2l       10w      161c https://watch.streamio.htb/static/Css => https://watch.streamio.htb/static/Css/
301      GET        2l       10w      157c https://watch.streamio.htb/Static => https://watch.streamio.htb/Static/
301      GET        2l       10w      161c https://watch.streamio.htb/Static/css => https://watch.streamio.htb/Static/css/
301      GET        2l       10w      160c https://watch.streamio.htb/Static/js => https://watch.streamio.htb/Static/js/
301      GET        2l       10w      161c https://watch.streamio.htb/Static/CSS => https://watch.streamio.htb/Static/CSS/
301      GET        2l       10w      160c https://watch.streamio.htb/Static/JS => https://watch.streamio.htb/Static/JS/
301      GET        2l       10w      160c https://watch.streamio.htb/Static/Js => https://watch.streamio.htb/Static/Js/
301      GET        2l       10w      161c https://watch.streamio.htb/Static/Css => https://watch.streamio.htb/Static/Css/
404      GET       40l      156w     1888c https://watch.streamio.htb/con

```

- `https://streamio.htb/about.php`
`oliver@Streamio.htb`というのが分かった
- この命名規則に従うと以下のようなメールアドレス、ユーザー名が推測できる
	- `barry@Streamio.htb`
	- `samantha@Streamio.htb`
![](https://i.imgur.com/NvhbdEe.png)

![](https://i.imgur.com/lhsP0h8.png)


## AS-REP Roasting
- 得られたユーザー名で、AS-REPを持っているユーザーがいないかを確認する
- ダメそう
```bash
┌──(kali㉿kali)-[~/Desktop/HTB/machine/StreamIO]
└─$ impacket-GetNPUsers streamIO.htb/ -dc-ip 10.129.186.103 -no-pass -usersfile users.txt -format hashcat -outputfile asrep_hashes.txt
Impacket v0.13.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

[-] Kerberos SessionError: KDC_ERR_C_PRINCIPAL_UNKNOWN(Client not found in Kerberos database)
[-] Kerberos SessionError: KDC_ERR_C_PRINCIPAL_UNKNOWN(Client not found in Kerberos database)
[-] Kerberos SessionError: KDC_ERR_C_PRINCIPAL_UNKNOWN(Client not found in Kerberos database)
[-] Kerberos SessionError: KDC_ERR_C_PRINCIPAL_UNKNOWN(Client not found in Kerberos database)
[-] Kerberos SessionError: KDC_ERR_C_PRINCIPAL_UNKNOWN(Client not found in Kerberos database)
[-] Kerberos SessionError: KDC_ERR_C_PRINCIPAL_UNKNOWN(Client not found in Kerberos database)

```


![](https://i.imgur.com/BVQDjdX.png)

## Dirsearch
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/StreamIO]
└─$ dirsearch -u streamIO.htb -t 50 -i 200 
/usr/lib/python3/dist-packages/dirsearch/dirsearch.py:23: DeprecationWarning: pkg_resources is deprecated as an API. See https://setuptools.pypa.io/en/latest/pkg_resources.html
  from pkg_resources import DistributionNotFound, VersionConflict

  _|. _ _  _  _  _ _|_    v0.4.3                                                                                                                                               
 (_||| _) (/_(_|| (_| )                                                                                                                                                        
                                                                                                                                                                               
Extensions: php, aspx, jsp, html, js | HTTP method: GET | Threads: 50 | Wordlist size: 11460

Output File: /home/kali/Desktop/HTB/machine/StreamIO/reports/_streamIO.htb/_25-08-21_01-04-42.txt

Target: https://streamIO.htb/

[01:04:43] Starting:                                                                                                                                                           
[01:04:48] 200 -    8KB - /about.php                                        
[01:04:55] 200 -    6KB - /contact.php                                      
[01:04:58] 200 -    1KB - /favicon.ico                                      
[01:05:02] 200 -    4KB - /login.php                                        
[01:05:07] 200 -    4KB - /register.php                                     
                                                                             
Task Completed                    
```


```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/StreamIO]
└─$ dirsearch -u https://watch.streamIO.htb/ -t 50 -i 200           
/usr/lib/python3/dist-packages/dirsearch/dirsearch.py:23: DeprecationWarning: pkg_resources is deprecated as an API. See https://setuptools.pypa.io/en/latest/pkg_resources.html
  from pkg_resources import DistributionNotFound, VersionConflict

  _|. _ _  _  _  _ _|_    v0.4.3                                                                                                                                               
 (_||| _) (/_(_|| (_| )                                                                                                                                                        
                                                                                                                                                                               
Extensions: php, aspx, jsp, html, js | HTTP method: GET | Threads: 50 | Wordlist size: 11460

Output File: /home/kali/Desktop/HTB/machine/StreamIO/reports/https_watch.streamIO.htb/__25-08-21_01-06-34.txt

Target: https://watch.streamIO.htb/

[01:06:34] Starting:                                                                                                                                                           
[01:06:49] 200 -    1KB - /favicon.ico                                      
[01:07:01] 200 -  248KB - /search.php                                       
                                                                             
Task Completed   
```


- `https://watch.streamIO.htb/search.php`
	- SQLインジェクションができるのか確認する
![](https://i.imgur.com/EkCPXHg.png)

## LDAP
- 」脆弱性があるかを確認する
```bash
┌──(kali㉿kali)-[~/Desktop/HTB/machine/StreamIO]
└─$ sqlmap -u "https://watch.streamio.htb/search.php" \
  --data="q=hi" \
  -p q --level 5 --risk 3
        ___
       __H__                                                                                                                                                                   
 ___ ___[']_____ ___ ___  {1.9.6#stable}                                                                                                                                       
|_ -| . [.]     | .'| . |                                                                                                                                                      
|___|_  [,]_|_|_|__,|  _|                                                                                                                                                      
      |_|V...       |_|   https://sqlmap.org                                                                                                                                   

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 01:38:46 /2025-08-21/

[01:38:46] [INFO] testing connection to the target URL
[01:38:47] [INFO] testing if the target URL content is stable
[01:38:48] [INFO] target URL content is stable
[01:38:48] [WARNING] heuristic (basic) test shows that POST parameter 'q' might not be injectable
[01:38:49] [INFO] testing for SQL injection on POST parameter 'q'
[01:38:49] [INFO] testing 'AND boolean-based blind - WHERE or HAVING clause'
got a 302 redirect to 'https://watch.streamio.htb/blocked.php'. Do you want to follow? [Y/n] n
[01:39:04] [INFO] POST parameter 'q' appears to be 'AND boolean-based blind - WHERE or HAVING clause' injectable (with --string="Is")
[01:39:16] [INFO] heuristic (extended) test shows that the back-end DBMS could be 'Sybase' 
it looks like the back-end DBMS is 'Sybase'. Do you want to skip test payloads specific for other DBMSes? [Y/n] Y
[01:40:55] [INFO] testing 'Microsoft SQL Server/Sybase AND error-based - WHERE or HAVING clause (IN)'
[01:40:55] [INFO] testing 'Microsoft SQL Server/Sybase OR error-based - WHERE or HAVING clause (IN)'
[01:40:56] [INFO] testing 'Microsoft SQL Server/Sybase AND error-based - WHERE or HAVING clause (CONVERT)'
[01:40:57] [INFO] testing 'Microsoft SQL Server/Sybase OR error-based - WHERE or HAVING clause (CONVERT)'
[01:40:57] [INFO] testing 'Microsoft SQL Server/Sybase AND error-based - WHERE or HAVING clause (CONCAT)'
[01:40:58] [INFO] testing 'Microsoft SQL Server/Sybase OR error-based - WHERE or HAVING clause (CONCAT)'
[01:40:58] [INFO] testing 'Microsoft SQL Server/Sybase error-based - Parameter replace'
[01:40:58] [INFO] testing 'Microsoft SQL Server/Sybase error-based - Parameter replace (integer column)'
[01:40:58] [INFO] testing 'Microsoft SQL Server/Sybase error-based - Stacking (EXEC)'
[01:40:58] [INFO] testing 'Generic inline queries'
[01:40:59] [INFO] testing 'Microsoft SQL Server/Sybase inline queries'
[01:40:59] [INFO] testing 'Microsoft SQL Server/Sybase stacked queries (comment)'
[01:40:59] [CRITICAL] considerable lagging has been detected in connection response(s). Please use as high value for option '--time-sec' as possible (e.g. 10 or more)
[01:41:00] [INFO] testing 'Microsoft SQL Server/Sybase stacked queries (DECLARE - comment)'
[01:41:00] [INFO] testing 'Microsoft SQL Server/Sybase stacked queries'
[01:41:01] [INFO] testing 'Microsoft SQL Server/Sybase stacked queries (DECLARE)'
[01:41:01] [INFO] testing 'Microsoft SQL Server/Sybase time-based blind (IF)'
[01:41:01] [INFO] testing 'Microsoft SQL Server/Sybase time-based blind (IF - comment)'
[01:41:02] [INFO] testing 'Microsoft SQL Server/Sybase AND time-based blind (heavy query)'
[01:41:03] [INFO] testing 'Microsoft SQL Server/Sybase OR time-based blind (heavy query)'
[01:41:03] [INFO] testing 'Microsoft SQL Server/Sybase AND time-based blind (heavy query - comment)'
[01:41:04] [INFO] testing 'Microsoft SQL Server/Sybase OR time-based blind (heavy query - comment)'
[01:41:05] [INFO] testing 'Microsoft SQL Server/Sybase time-based blind - Parameter replace (heavy queries)'
[01:41:05] [INFO] testing 'Generic UNION query (NULL) - 1 to 20 columns'
[01:41:05] [INFO] automatically extending ranges for UNION query injection technique tests as there is at least one other (potential) technique found
[01:41:15] [INFO] testing 'Generic UNION query (random number) - 1 to 20 columns'
[01:41:27] [INFO] testing 'Generic UNION query (NULL) - 21 to 40 columns'
[01:41:36] [INFO] testing 'Generic UNION query (random number) - 21 to 40 columns'
[01:41:45] [INFO] testing 'Generic UNION query (NULL) - 41 to 60 columns'
[01:41:57] [INFO] testing 'Generic UNION query (random number) - 41 to 60 columns'
[01:42:06] [INFO] testing 'Generic UNION query (NULL) - 61 to 80 columns'
[01:42:14] [INFO] testing 'Generic UNION query (random number) - 61 to 80 columns'
[01:42:24] [INFO] testing 'Generic UNION query (NULL) - 81 to 100 columns'
[01:42:32] [INFO] testing 'Generic UNION query (random number) - 81 to 100 columns'
[01:42:41] [INFO] checking if the injection point on POST parameter 'q' is a false positive
POST parameter 'q' is vulnerable. Do you want to keep testing the others (if any)? [y/N] y
sqlmap identified the following injection point(s) with a total of 299 HTTP(s) requests:
Parameter: q (POST)
    Type: boolean-based blind
    Title: AND boolean-based blind - WHERE or HAVING clause
    Payload: q=hi%' AND 7972=7972 AND 'odKe%'='odKe
[01:43:06] [INFO] testing Sybase
[01:43:07] [WARNING] the back-end DBMS is not Sybase
[01:43:07] [INFO] testing MySQL
[01:43:07] [WARNING] the back-end DBMS is not MySQL
[01:43:07] [INFO] testing Oracle
[01:43:07] [WARNING] the back-end DBMS is not Oracle
[01:43:07] [INFO] testing PostgreSQL
[01:43:08] [WARNING] the back-end DBMS is not PostgreSQL
[01:43:08] [INFO] testing Microsoft SQL Server
[01:43:08] [WARNING] the back-end DBMS is not Microsoft SQL Server
[01:43:08] [INFO] testing SQLite
[01:43:09] [WARNING] the back-end DBMS is not SQLite
[01:43:09] [INFO] testing Microsoft Access
[01:43:09] [WARNING] the back-end DBMS is not Microsoft Access
[01:43:09] [INFO] testing Firebird
[01:43:10] [WARNING] the back-end DBMS is not Firebird
[01:43:10] [INFO] testing SAP MaxDB
[01:43:10] [WARNING] the back-end DBMS is not SAP MaxDB
[01:43:10] [INFO] testing IBM DB2
[01:43:11] [WARNING] the back-end DBMS is not IBM DB2
[01:43:11] [INFO] testing HSQLDB
[01:43:11] [WARNING] the back-end DBMS is not HSQLDB
[01:43:11] [INFO] testing H2
[01:43:11] [WARNING] the back-end DBMS is not H2
[01:43:11] [INFO] testing Informix
[01:43:12] [WARNING] the back-end DBMS is not Informix
[01:43:12] [INFO] testing MonetDB
[01:43:12] [WARNING] the back-end DBMS is not MonetDB
[01:43:12] [INFO] testing Apache Derby
[01:43:13] [WARNING] the back-end DBMS is not Apache Derby
[01:43:13] [INFO] testing Vertica
[01:43:13] [WARNING] the back-end DBMS is not Vertica
[01:43:13] [INFO] testing Mckoi
[01:43:14] [WARNING] the back-end DBMS is not Mckoi
[01:43:14] [INFO] testing Presto
[01:43:14] [WARNING] the back-end DBMS is not Presto
[01:43:14] [INFO] testing Altibase
[01:43:15] [WARNING] the back-end DBMS is not Altibase
[01:43:15] [INFO] testing MimerSQL
[01:43:15] [WARNING] the back-end DBMS is not MimerSQL
[01:43:15] [INFO] testing ClickHouse
[01:43:15] [WARNING] the back-end DBMS is not ClickHouse
[01:43:15] [INFO] testing CrateDB
[01:43:16] [WARNING] the back-end DBMS is not CrateDB
[01:43:16] [INFO] testing Cubrid
[01:43:19] [WARNING] the back-end DBMS is not Cubrid
[01:43:19] [INFO] testing InterSystems Cache
[01:43:20] [WARNING] the back-end DBMS is not InterSystems Cache
[01:43:20] [INFO] testing eXtremeDB
[01:43:20] [WARNING] the back-end DBMS is not eXtremeDB
[01:43:20] [INFO] testing FrontBase
[01:43:21] [WARNING] the back-end DBMS is not FrontBase
[01:43:21] [INFO] testing Raima Database Manager
[01:43:21] [WARNING] the back-end DBMS is not Raima Database Manager
[01:43:21] [INFO] testing Virtuoso
[01:43:22] [WARNING] the back-end DBMS is not Virtuoso
[01:43:22] [CRITICAL] sqlmap was not able to fingerprint the back-end database management system

[*] ending @ 01:43:22 /2025-08-21/

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

