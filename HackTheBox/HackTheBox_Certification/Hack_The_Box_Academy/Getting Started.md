# InfoSecの概要
- infosecは、不正なアクセス、変更、違法な使用、中断などからデータを保護する
- 私たちの情報セキュリティのキャリアで何度も出てくる一般的なフレーズは、「データの機密性、完全性、および可用性」、または`CIA triad`を保護すること

## 特権のエスカレーション

### PrivEscチェックリスト
HackTrics
- https://book.hacktricks.xyz/
- Linux
	- https://book.hacktricks.wiki/en/linux-hardening/linux-privilege-escalation-checklist.html
- Windows
	- https://book.hacktricks.wiki/en/windows-hardening/checklist-windows-privilege-escalation.html


PayloadsAllThings
- https://github.com/swisskyrepo/PayloadsAllTheThings
- Linux
	- https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Linux%20-%20Privilege%20Escalation.md
- Windows
	- https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Windows%20-%20Privilege%20Escalation.md

## 列挙スクリプト
- Linux
	- [LinEnum](https://github.com/rebootuser/LinEnum.git) と [linuxprivchecker](https://github.com/sleventyeleven/linuxprivchecker) と[Privilege Escalation Awesome Scripts SUITE（PEASS）](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite)
- Windows
	- [Seatbelt](https://github.com/GhostPack/Seatbelt) と [JAWS ](https://github.com/411Hall/JAWS)

## カーネルエクスプロイト
- 古いオペレーティングシステムを実行しているサーバーに遭遇するたびに、存在する可能性のある潜在的なカーネルの脆弱性を探す
- 同じ概念が Windows にも当てはまります。Windows の未適用/古いバージョンには、特権の昇格に使用できるさまざまな脆弱性がある多くの脆弱性

## 脆弱なソフトウェア
- 私たちが探すべきもう一つのものは、インストールされたソフトウェア
- たとえば、Linux で `dpkg -l` コマンドを使用する
- Windows の `C:\Program Files` を参照して、システムにインストールされているソフトウェアを確認できる
- インストールされているソフトウェアの公開エクスプロイトを探す必要があります。特に、パッチが適用されていない脆弱性を含む古いバージョンが使用されている場合はなおさら



## ユーザー権限
1. Sudo -l
2. SUID
3. Windows Token Privileges


全てをSudoでできるから、こう言う時は、`sudo su -`でいける
```shell-session
User user1 may run the following commands on ExampleServer:
    (ALL : ALL) ALL
```

sudo -l で特定のアプリケーション、またはすべてのアプリケーションをパスワードなしで実行できる場合がある
```shell-session
snowyowl644@htb[/htb]$ sudo -l

    (user : user) NOPASSWD: /bin/echo
```


- Sudoで実行できる特定のアプリケーションを見つけたら、悪用してルートユーザーとしてシェルを取得する方法
- GTFOBinsには、コマンドのリストと、sudoを介してそれらを悪用する方法が含まれている
- Sudo 権限を持つアプリケーションを検索でき、存在する場合、sudo 権限を使用してルート アクセスを取得するために実行すべき正確なコマンド
- [LOLBAS](https://lolbas-project.github.io/#)には、特権ユーザーのコンテキストでファイルのダウンロードやコマンドの実行など、特定の機能を実行するために活用できる可能性のあるWindowsアプリケーションのリスト

## スケジュールされたタスク
- Linux と Windows の両方で、タスクを実行するためにスクリプトを特定の間隔で実行する方法
- いくつかの例は、1時間ごとに実行されるウイルス対策スキャンや、30分ごとに実行されるバックアップスクリプト
1. 新しいスケジュールされたタスク/cronジョブを追加する
2. 悪意のあるソフトウェアを実行するように彼らをだます

実行できるフォルダ
`/etc/crontab`
 `/etc/cron.d`
 `/var/spool/cron/crontabs/root`
 cronジョブによって呼び出されたディレクトリに書き込むことができれば、実行時にリバースシェルを送信するリバースシェルコマンドでbashスクリプト

## 公開された資格情報
読み取りできるファイルを探して、公開されている資格情報が含まれているかどうかを確認できる
`configuration`ファイル、`log`ファイル、およびユーザー履歴ファイル（Linuxの`bash_history`およびWindowsの`PSReadLine`）で非常に一般的

## SSHキー
- SSHキーについて話し合う
- 特定のユーザーの .ssh ディレクトリを介して読み取りアクセス権がある場合、/home/user/.ssh/id_rsa または /root/.ssh/id_rsa にあるプライベート ssh キーを読み、それを使用してサーバーにログインできる
- /Root/.ssh/ディレクトリを読み、id_rsaファイルを読み取ることができれば、それをマシンにコピーし、-iフラグを使用してログインできる