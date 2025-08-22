
![HTB Banner](https://github.com/hackthebox/writeup-templates/blob/master/machine/assets/images/banner.png?raw=true)


- URL : 
- #easy 
- OS : #Linux 
- Machine Author(s): [nirza](https://app.hackthebox.com/users/800960)
- Hack Date: 2024-12-29,16:36


# Enumeration
ポートは20番、80番、3000番が空いてる
3000番は、well knownポートではないが、httpで何らかのサイト？
```bash
PORT     STATE SERVICE REASON         VERSION
22/tcp   open  ssh     syn-ack ttl 63 OpenSSH 8.9p1 Ubuntu 3ubuntu0.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 57:d6:92:8a:72:44:84:17:29:eb:5c:c9:63:6a:fe:fd (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBOp+cK9ugCW282Gw6Rqe+Yz+5fOGcZzYi8cmlGmFdFAjI1347tnkKumDGK1qJnJ1hj68bmzOONz/x1CMeZjnKMw=
|   256 40:ea:17:b1:b6:c5:3f:42:56:67:4a:3c:ee:75:23:2f (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIEZQbCc8u6r2CVboxEesTZTMmZnMuEidK9zNjkD2RGEv
80/tcp   open  http    syn-ack ttl 63 nginx 1.18.0 (Ubuntu)
|_http-server-header: nginx/1.18.0 (Ubuntu)
| http-title: Welcome to GreenHorn ! - GreenHorn
|_Requested resource was http://greenhorn.htb/?file=welcome-to-greenhorn
|_http-generator: pluck 4.7.18
| http-methods: 
|_  Supported Methods: GET HEAD POST
3000/tcp open  ppp?    syn-ack ttl 63
| fingerprint-strings: 
|   GenericLines, Help, RTSPRequest: 
|     HTTP/1.1 400 Bad Request
|     Content-Type: text/plain; charset=utf-8
|     Connection: close
|     Request
|   GetRequest: 
|     HTTP/1.0 200 OK
|     Cache-Control: max-age=0, private, must-revalidate, no-transform
|     Content-Type: text/html; charset=utf-8
|     Set-Cookie: i_like_gitea=0be44410dbbb0381; Path=/; HttpOnly; SameSite=Lax
|     Set-Cookie: _csrf=wBVspdjFwYH1yysu652HFrNqycQ6MTczNTc4NjcwODUzMzU0NzE4NQ; Path=/; Max-Age=86400; HttpOnly; SameSite=Lax
|     X-Frame-Options: SAMEORIGIN
|     Date: Thu, 02 Jan 2025 02:58:28 GMT
|     <!DOCTYPE html>
|     <html lang="en-US" class="theme-auto">
|     <head>
|     <meta name="viewport" content="width=device-width, initial-scale=1">
|     <title>GreenHorn</title>
|     <link rel="manifest" href="data:application/json;base64,eyJuYW1lIjoiR3JlZW5Ib3JuIiwic2hvcnRfbmFtZSI6IkdyZWVuSG9ybiIsInN0YXJ0X3VybCI6Imh0dHA6Ly9ncmVlbmhvcm4uaHRiOjMwMDAvIiwiaWNvbnMiOlt7InNyYyI6Imh0dHA6Ly9ncmVlbmhvcm4uaHRiOjMwMDAvYXNzZXRzL2ltZy9sb2dvLnBuZyIsInR5cGUiOiJpbWFnZS9wbmciLCJzaXplcyI6IjUxMng1MTIifSx7InNyYyI6Imh0dHA6Ly9ncmVlbmhvcm4uaHRiOjMwMDAvYX
|   HTTPOptions: 
|     HTTP/1.0 405 Method Not Allowed
|     Allow: HEAD
|     Allow: GET
|     Cache-Control: max-age=0, private, must-revalidate, no-transform
|     Set-Cookie: i_like_gitea=c19aa1d7521f9074; Path=/; HttpOnly; SameSite=Lax
|     Set-Cookie: _csrf=qNYx5z7BM6KIUxAGzKyjjTj5vAk6MTczNTc4NjcxMzU5NzA2MjczOQ; Path=/; Max-Age=86400; HttpOnly; SameSite=Lax
|     X-Frame-Options: SAMEORIGIN
|     Date: Thu, 02 Jan 2025 02:58:33 GMT
|_    Content-Length: 0

```

whatweb
- `http://greenhorn.htb/?file=welcome-to-greenhorn`
	- ディレクトリトラバーサルとかできないかな
```bash
└──╼ [★]$ whatweb http://greenhorn.htb
http://greenhorn.htb [302 Found] Cookies[PHPSESSID], Country[RESERVED][ZZ], HTTPServer[Ubuntu Linux][nginx/1.18.0 (Ubuntu)], IP[10.129.207.200], RedirectLocation[http://greenhorn.htb/?file=welcome-to-greenhorn], nginx[1.18.0]
http://greenhorn.htb/?file=welcome-to-greenhorn [200 OK] Cookies[PHPSESSID], Country[RESERVED][ZZ], HTTPServer[Ubuntu Linux][nginx/1.18.0 (Ubuntu)], IP[10.129.207.200], MetaGenerator[pluck 4.7.18], Pluck-CMS[4.7.18], Title[Welcome to GreenHorn ! - GreenHorn], nginx[1.18.0]

```

feroxbuster
- docsディレクトリの中のいくつか
```bash
└──╼ [★]$ feroxbuster -u http://greenhorn.htb
...
200      GET       41l      266w     1811c http://greenhorn.htb/docs/README
200      GET      676l     5644w    35068c http://greenhorn.htb/docs/COPYING
200      GET      182l     1236w     8535c http://greenhorn.htb/docs/CHANGES

```

dirbuster
robots.txtにアクセス出来る。
/data/・/docs/にはアクセスして欲しくなさそう
![](https://raw.githubusercontent.com/crum7/Obsidian/main/HackTheBox_Writeups/Retired/Easy/GreenHorn/images/Pasted%20image%2020241229171606.png)
```bash
└──╼ [★]$ sudo dirsearch --url=http://greenhorn.htb

  _|. _ _  _  _  _ _|_    v0.4.3
 (_||| _) (/_(_|| (_| )

Extensions: php, aspx, jsp, html, js | HTTP method: GET | Threads: 25 | Wordlist size: 11460

Output File: /home/snowyowl644/HTBScript/reports/http_greenhorn.htb/_24-12-29_01-55-28.txt

Target: http://greenhorn.htb/

[01:55:28] Starting: 
[01:55:31] 200 -   93B  - /+CSCOT+/oem-customization?app=AnyConnect&type=oem&platform=..&resource-type=..&name=%2bCSCOE%2b/portal_inc.lua
[01:55:31] 200 -   93B  - /+CSCOT+/translation-table?type=mst&textdomain=/%2bCSCOE%2b/portal_inc.lua&default-language&lang=../
[01:55:32] 404 -  564B  - /.css
[01:55:32] 404 -  564B  - /.gif
[01:55:32] 404 -  564B  - /.ico
[01:55:33] 404 -  564B  - /.jpg
[01:55:33] 404 -  564B  - /.jpeg
[01:55:33] 404 -  564B  - /.png
[01:55:36] 404 -  564B  - /adm/style/admin.css
[01:55:36] 200 -    4KB - /admin.php
[01:55:37] 404 -  564B  - /admin_my_avatar.png
[01:55:41] 404 -  564B  - /bundles/kibana.style.css
[01:55:43] 301 -  178B  - /data  ->  http://greenhorn.htb/data/
[01:55:43] 200 -   48B  - /data/
[01:55:44] 200 -   93B  - /docpicker/common_proxy/http/www.redbooks.ibm.com/Redbooks.nsf/RedbookAbstracts/sg247798.html?Logout&RedirectTo=http://example.com
[01:55:44] 301 -  178B  - /docs  ->  http://greenhorn.htb/docs/
[01:55:44] 403 -  564B  - /docs/
[01:55:45] 200 -   93B  - /faces/javax.faces.resource/web.xml?ln=../WEB-INF
[01:55:45] 200 -   93B  - /faces/javax.faces.resource/web.xml?ln=..\\WEB-INF
[01:55:45] 404 -  564B  - /favicon.ico
[01:55:45] 301 -  178B  - /files  ->  http://greenhorn.htb/files/
[01:55:45] 403 -  564B  - /files/
[01:55:47] 404 -  564B  - /IdentityGuardSelfService/images/favicon.ico
[01:55:47] 301 -  178B  - /images  ->  http://greenhorn.htb/images/
[01:55:47] 403 -  564B  - /images/
[01:55:47] 200 -    4KB - /install.php
[01:55:48] 200 -    4KB - /install.php?profile=default
[01:55:48] 200 -   93B  - /jmx-console/HtmlAdaptor?action=inspectMBean&name=jboss.system:type=ServerInfo
[01:55:49] 200 -    1KB - /login.php
[01:55:49] 404 -  564B  - /logo.gif
[01:55:50] 200 -   93B  - /manager/jmxproxy/?get=java.lang:type=Memory&att=HeapMemoryUsage
[01:55:50] 200 -   93B  - /manager/jmxproxy/?invoke=Catalina%3Atype%3DService&op=findConnectors&ps=
[01:55:54] 200 -   93B  - /plugins/servlet/gadgets/makeRequest?url=https://google.com
[01:55:55] 200 -   93B  - /proxy.stream?origin=https://google.com
[01:55:55] 200 -    2KB - /README.md
[01:55:55] 200 -   93B  - /remote/fgt_lang?lang=/../../../..//////////dev/cmdb/sslvpn_websession
[01:55:55] 200 -   93B  - /remote/fgt_lang?lang=/../../../../////////////////////////bin/sslvpnd
[01:55:55] 404 -  564B  - /resources/.arch-internal-preview.css
[01:55:56] 200 -   47B  - /robots.txt
[01:55:57] 404 -  564B  - /skin1_admin.css
[01:55:58] 404 -  997B  - /solr/admin/file/?file=solrconfig.xml
[01:56:03] 200 -   93B  - /wps/myproxy/http/www.redbooks.ibm.com/Redbooks.nsf/RedbookAbstracts/sg247798.html?Logout&RedirectTo=http://example.com
[01:56:03] 200 -   93B  - /wps/contenthandler/!ut/p/digest!8skKFbWr_TwcZcvoc9Dn3g/?uri=http://www.redbooks.ibm.com/Redbooks.nsf/RedbookAbstracts/sg247798.html?Logout&RedirectTo=http://example.com
[01:56:03] 200 -   93B  - /wps/common_proxy/http/www.redbooks.ibm.com/Redbooks.nsf/RedbookAbstracts/sg247798.html?Logout&RedirectTo=http://example.com
[01:56:03] 200 -   93B  - /wps/cmis_proxy/http/www.redbooks.ibm.com/Redbooks.nsf/RedbookAbstracts/sg247798.html?Logout&RedirectTo=http://example.com
[01:56:03] 200 -   93B  - /wps/proxy/http/www.redbooks.ibm.com/Redbooks.nsf/RedbookAbstracts/sg247798.html?Logout&RedirectTo=http://example.com

Task Completed

```

いくつか、ディレクトラバーサルなどのwebリンクが見つかったので、アクセスするも、こんな文章が表示される
![](https://raw.githubusercontent.com/crum7/Obsidian/main/HackTheBox_Writeups/Retired/Easy/GreenHorn/images/Pasted%20image%2020241229171035.png)


```bash

└──╼ [★]$ sudo dirsearch -u http://greenhorn.htb -t 50 -i 200

Target: http://greenhorn.htb/

[02:04:25] Starting: 
[02:04:25] 200 -   93B  - /+CSCOT+/translation-table?type=mst&textdomain=/%2bCSCOE%2b/portal_inc.lua&default-language&lang=../
[02:04:25] 200 -   93B  - /+CSCOT+/oem-customization?app=AnyConnect&type=oem&platform=..&resource-type=..&name=%2bCSCOE%2b/portal_inc.lua
[02:04:32] 200 -    4KB - /admin.php
[02:04:39] 200 -   48B  - /data/
[02:04:40] 200 -   93B  - /docpicker/common_proxy/http/www.redbooks.ibm.com/Redbooks.nsf/RedbookAbstracts/sg247798.html?Logout&RedirectTo=http://example.com
[02:04:41] 200 -   93B  - /faces/javax.faces.resource/web.xml?ln=../WEB-INF
[02:04:41] 200 -   93B  - /faces/javax.faces.resource/web.xml?ln=..\\WEB-INF
[02:04:44] 200 -    4KB - /install.php
[02:04:44] 200 -    4KB - /install.php?profile=default
[02:04:44] 200 -   93B  - /jmx-console/HtmlAdaptor?action=inspectMBean&name=jboss.system:type=ServerInfo
[02:04:45] 200 -    1KB - /login.php
[02:04:46] 200 -   93B  - /manager/jmxproxy/?invoke=Catalina%3Atype%3DService&op=findConnectors&ps=
[02:04:46] 200 -   93B  - /manager/jmxproxy/?get=java.lang:type=Memory&att=HeapMemoryUsage
[02:04:50] 200 -   93B  - /plugins/servlet/gadgets/makeRequest?url=https://google.com
[02:04:51] 200 -   93B  - /proxy.stream?origin=https://google.com
[02:04:51] 200 -    2KB - /README.md
[02:04:51] 200 -   93B  - /remote/fgt_lang?lang=/../../../../////////////////////////bin/sslvpnd
[02:04:51] 200 -   93B  - /remote/fgt_lang?lang=/../../../..//////////dev/cmdb/sslvpn_websession
[02:04:52] 200 -   47B  - /robots.txt
[02:04:59] 200 -   93B  - /wps/cmis_proxy/http/www.redbooks.ibm.com/Redbooks.nsf/RedbookAbstracts/sg247798.html?Logout&RedirectTo=http://example.com
[02:04:59] 200 -   93B  - /wps/contenthandler/!ut/p/digest!8skKFbWr_TwcZcvoc9Dn3g/?uri=http://www.redbooks.ibm.com/Redbooks.nsf/RedbookAbstracts/sg247798.html?Logout&RedirectTo=http://example.com
[02:04:59] 200 -   93B  - /wps/myproxy/http/www.redbooks.ibm.com/Redbooks.nsf/RedbookAbstracts/sg247798.html?Logout&RedirectTo=http://example.com
[02:04:59] 200 -   93B  - /wps/proxy/http/www.redbooks.ibm.com/Redbooks.nsf/RedbookAbstracts/sg247798.html?Logout&RedirectTo=http://example.com
[02:04:59] 200 -   93B  - /wps/common_proxy/http/www.redbooks.ibm.com/Redbooks.nsf/RedbookAbstracts/sg247798.html?Logout&RedirectTo=http://example.com

```

ffufでサブドメインを探したが、見つからなかった
```bash
ffuf -w /usr/share/wordlists/seclists/Discovery/DNS/bitquark-subdomains-top100000.txt -u http://greenhorn.htb -H "Host: FUZZ.greenhorn.htb" -mc 200
```

/login.phpで、このサイトが、pluck cms 4.7.18を利用していることがわかる。
- https://github.com/pluck-cms/pluck
![](https://raw.githubusercontent.com/crum7/Obsidian/main/HackTheBox_Writeups/Retired/Easy/GreenHorn/images/Pasted%20image%2020241229173129.png)

- 脆弱性があるのかなと思って調べたが、Pluck CMS 4.7.18の脆弱性はなさそう？
```bash
└──╼ [★]$ searchsploit pluck
------------------------------------------------------------------------------------------------------------------------------------------------ ---------------------
 Exploit Title                                                                                                                                  |  Path
------------------------------------------------------------------------------------------------------------------------------------------------ ---------------------
Pluck CMS 4.5.1 (Windows) - 'blogpost' Local File Inclusion                                                                                     | php/webapps/6074.txt
Pluck CMS 4.5.2 - Multiple Cross-Site Scripting Vulnerabilities                                                                                 | php/webapps/32168.txt
Pluck CMS 4.5.2 - Multiple Local File Inclusions                                                                                                | php/webapps/6300.txt
Pluck CMS 4.5.3 - 'g_pcltar_lib_dir' Local File Inclusion                                                                                       | php/webapps/7153.txt
Pluck CMS 4.5.3 - 'update.php' Remote File Corruption                                                                                           | php/webapps/6492.php
Pluck CMS 4.6.1 - 'module_pages_site.php' Local File Inclusion                                                                                  | php/webapps/8271.php
Pluck CMS 4.6.2 - 'langpref' Local File Inclusion                                                                                               | php/webapps/8715.txt
Pluck CMS 4.6.3 - 'cont1' HTML Injection                                                                                                        | php/webapps/34790.txt
Pluck CMS 4.7 - Directory Traversal                                                                                                             | php/webapps/36986.txt
Pluck CMS 4.7 - HTML Code Injection                                                                                                             | php/webapps/27398.txt
Pluck CMS 4.7 - Multiple Local File Inclusion / File Disclosure Vulnerabilities                                                                 | php/webapps/36129.txt
Pluck CMS 4.7.13 - File Upload Remote Code Execution (Authenticated)                                                                            | php/webapps/49909.py
Pluck CMS 4.7.16 - Remote Code Execution (RCE) (Authenticated)                                                                                  | php/webapps/50826.py
Pluck CMS 4.7.3 - Cross-Site Request Forgery (Add Page)                                                                                         | php/webapps/40566.py
Pluck CMS 4.7.3 - Multiple Vulnerabilities                                                                                                      | php/webapps/38002.txt
Pluck v4.7.18 - Remote Code Execution (RCE)                                                                                                     | php/webapps/51592.py
pluck v4.7.18 - Stored Cross-Site Scripting (XSS)                                                                                               | php/webapps/51420.txt
------------------------------------------------------------------------------------------------------------------------------------------------ ---------------------
Shellcodes: No Results

```


- いや、Pluckは、Pluck CMSを指してるらしい
	- Pluck v4.7.18 - Remote Code Execution (RCE)  を試してみる
 ![](https://raw.githubusercontent.com/crum7/Obsidian/main/HackTheBox_Writeups/Retired/Easy/GreenHorn/images/Pasted%20image%2020241229180356.png)

PoCをミラーダウンロードする
```bash
searchsploit -m php/webapps/51592.py
```

コードの調整が必要な部分を調整する
Upload_URLを指定する必要があるが、アップロードするサイトの部分がないから出来なさそう
他の手法を試す

調べてると、Readme.mdが見つかったので、見ていると、
[Plunk CMSのWiki](https://github.com/pluck-cms/pluck/wiki/Frequently-Asked-Questions)のリンクを見つけた。
Wiki内で
	**Changing the Password** There is way to reset a lost password. But you can modify it via ftp!
	To do so, edit the value for ww in data\settings\pass.php
	
	```html
	<?php
	$ww = 'ba3253876aed6bc22d4a6ff53d8406c6ad864195ed144ab5c87621b6c233b548baeae6956df346ec8c17f5ea10f35ee3cbc514797ed7ddd3145464e2a0bab413';
	```
	
	This lines change the password to "123456".
	
	Then login, click 'options' and change the password
	
	[](https://github.com/pluck-cms/pluck/wiki/_Footer/_edit "Edit footer")
	
	bx0
という記述がある。この記述から、data\settings\pass.phpでパスワードを変更することができるとわかる。
しかし、`curl -o http://greenhorn.htb/data/settings/pass.php`と直接ダウンロードしようとしても、ダウンロードできない。
以下のようなディレクトリトラバーサル攻撃もうまく効かない
`http://greenhorn.htb/?file=welcome-to-greenhorn....//....//....//....//data/settings/pass.php`

このサーバーは、3000番からアクセスできる。
![](https://raw.githubusercontent.com/crum7/Obsidian/main/HackTheBox_Writeups/Retired/Easy/GreenHorn/images/Pasted%20image%2020250102124547.png)
johnで解析する。
ハッシュのパスワードが「iloveyou1」であることがわかった。
```sh
└──╼ [★]$ echo "d5443aef1b64544f3685bf112f6c405218c573c7279a831b1fe9612e3a4d770486743c5580556c0d838b51749de15530f87fb793afdcc689b6b39024d7790163" > hashes.txt

└──╼ [★]$ john --format=raw-sha512 hashes.txt
Created directory: /home/snowyowl644/.john
Using default input encoding: UTF-8
Loaded 1 password hash (Raw-SHA512 [SHA512 256/256 AVX2 4x])
Warning: poor OpenMP scalability for this hash type, consider --fork=4
Will run 4 OpenMP threads
Proceeding with single, rules:Single
Press 'q' or Ctrl-C to abort, almost any other key for status
Almost done: Processing the remaining buffered candidate passwords, if any.
Proceeding with wordlist:/usr/share/john/password.lst
iloveyou1        (?)     
1g 0:00:00:00 DONE 2/3 (2025-01-01 21:58) 33.33g/s 136533p/s 136533c/s 136533C/s 123456..Peter
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 
```

sshでadmin/iloveyou1でログインしたがログインできない。
まず、pluck CMSにadmin/iloveyou1でログイン。
ユーザーを確認する。



# Foothold

記事を書いているMr.Greenというアカウントがあるのかと思ったけど、無さそう。
リバースシェルを[]moduleにアップロード、インストールして、リバースシェルを確立する。
正しく動いているときは、特にリバースシェルに明示的にアクセスしないで、勝手にリバースシェルが確立される。

リバースシェルは、[https://www.revshells.com/]を使用して、PHP PentestMonkeyを使う。
注意点として、.zipと.phpの前の名前は同じにしないと正しくインストールされないぽい。
```bash
nano shell.php
zip shell.zip shell.php
```

他のタブでリバースを待ち受ける。
```bash
sudo nc -lvnp <PORT>
```

リバースシェルが確立されて、whoamiをすると、Apache サーバーの Web 管理者であるwww-dataであることがわかる。
```bash
$ whoami
www-data
```


# Lateral Movement
ユーザーフラグは、juniorというwww-dataとは違うアカウントのhomeフォルダ以下にある。
先ほどのadminのパスワードと同じパスワードでユーザー切り替えができる。
```bash
www-data@greenhorn:/home$ su - junior
su - junior
Password: iloveyou1

```



# Privilege Escalation

juiorの/homeの中にuser.txtの他にもpdfファイルがあったのでみてみる。
![](https://raw.githubusercontent.com/crum7/Obsidian/main/HackTheBox_Writeups/Retired/Easy/GreenHorn/images/Pasted%20image%2020250102181441.png)

文字のところを複合する

```bash
git clone https://github.com/spipm/Depix.git
cd Depix
```

複合すると、sidefromsidetheothersidesidefromsidetheothersideであることがわかる。
利用して、rootに権限昇格
```bash
www-data@greenhorn:/$ su root                   
su root
Password: sidefromsidetheothersidesidefromsidetheotherside

root@greenhorn:/# 

```


## Flags

- **User**: 08f08b7cf6c897d19260db2eae90e403
- **Root**: d782ab796e204fff28c2aa2872c21a75

### ポイント


- 詰まったら、一回俯瞰する(大事)
	- 80番でdata\settings\pass.phpでアクセスできないことがわかる→俯瞰する。→3000番もhttpで探索していないことに気づく。
	- 画像の複合化はできる。
		- https://github.com/spipm/Depix.git