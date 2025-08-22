
# SIEM の定義と基礎
- SIEM（Security Information and Event Management／セキュリティ情報・イベント管理）
	- セキュリティデータの管理とセキュリティイベントの監視を統合するソフトウェアやソリューションを指す
	- ネットワーク機器やアプリケーションによって生成されるセキュリティ関連のアラートをリアルタイムに評価できるようにする

SIEMツールの主な機能
- ログイベントの収集と管理
- 複数のソースからのログや付随データの分析
- インシデント対応、可視化ダッシュボード、レポート作成などの運用機能

「SIEM」という言葉は2005年、ガートナーのアナリストによる論文 「Enhance IT Security through Vulnerability Management」 で提唱されました。これは、
- **SIM（Security Information Management）** 
	- ログ管理をベースに長期保存・分析・レポートを可能にした技術
- **SEM（Security Event Management）** 
	- 複数のセキュリティ機器（アンチウイルス、FW、IDS など）からのイベントを相関・統合して通知する技術
の二つを統合する発想

- SIEMは、PC・ネットワーク機器・サーバーなど多様なソースからデータを収集し、標準化・統合する
- SOCの分析者がこれを精査し、潜在的な脅威を検出
	- アラートは、特定の攻撃や異常を示す通知としてSOCチームに届く
		- 配信方法はメール、コンソール通知、SMS、電話など多様
	- 監視対象が多いと膨大なアラートが発生するため、重大イベントに絞るチューニングが不可欠
- SIEMは単体でIPSやIDSを置き換えるものではなく、それらのログを統合・相関してより大きな脅威を浮かび上がらせることが強み

ビジネス要件とユースケース
- ログ集約と正規化
	- ファイアウォールやDB、重要アプリからのログを一元的に収集・整理することで、脅威の可視性を飛躍的に向上。SOCが相関分析できる。
- 脅威アラート
	- 膨大なイベントから潜在的な脅威を抽出し、リアルタイム通知。迅速な調査と対応を支援。
- コンテキスト化と対応
	- 単にアラートを出すだけでなく、脅威の関連情報（誰が、どのシステムで、いつ）を付与し、真のリスクを見極めて対応効率を高める。
- コンプライアンス
	- PCI DSS、HIPAA、GDPRなど各種規制の要件（リアルタイム監視、ログ保全、レポート生成など）に対応。自動化されたレポーティングと監査証跡を提供。

SIEM内のデータフロー
	1.	データ収集（ingestion） – 各種ソースからログを取得
	2.	正規化・集約（normalization & aggregation） – 多様な形式を統一し、相関分析が可能な形にする
	3.	分析と可視化 – SOCが検知ルール・ダッシュボード・アラートを構築し、脅威を迅速に特定


# Elastic Stack入門
- Elastic社が開発した **Elastic Stack** 
	- 主に3つのアプリケーション（**Elasticsearch, Logstash, Kibana**）から成るオープンソースのソリューション群
	- ログソースのリアルタイム分析や可視化を包括的に提供

![Elastic Stack diagram showing components: Solutions (Application Search, Site Search, Enterprise Search, Logging, Future), Visualize (Kibana), Store Search & Analyze (Elasticsearch), Ingest (Beats, Logstash), Deployment (SaaS: Elastic Cloud, Self Managed: Elastic Cloud Enterprise, Standalone).](https://academy.hackthebox.com/storage/modules/211/elastic.png)

- Beats → Logstash/Queue → Elasticsearch（Master/Ingest/Dataノード）→ Kibana の流れでデータを可視化する

それぞれの機能
Elasticsearch
- RESTful APIに基づいた分散型JSON検索エンジン
- インデックス作成・保存・検索を担当。高度なクエリや分析が可能

Logstash
- ログデータの収集・変換・転送を担当。入力・変換・出力の3つの機能領域
	- 入力：ファイル/TCP/シスログなどから取り込み
	- 変換：フィルタープラグインでログを加工・正規化
	- 出力：Elasticsearchへ転送

Kibana
- Elasticsearchに格納されたデータの可視化ツール
- テーブル・チャート・ダッシュボードで結果を直感的に理解できる

Beats
- FilebeatやMetricbeatなど、軽量で単機能のデータシッパー
- リモート機器に導入し、LogstashまたはElasticsearchへログ・メトリクスを送信


## Elastic StackをSIEMとして使う
- Elastic Stackは SIEM（Security Information and Event Management） として利用可能

流れ
- LogstashでFW/IDS/IPS/エンドポイントからのセキュリティデータを取り込み
- Elasticsearchで保存・インデックス化
- Kibanaでカスタムダッシュボードを作成し、セキュリティイベントを可視化


## KQL
- Kibana Query Language
- Kibana でのデータ検索と分析に特化して設計された、強力でユーザーフレンドリーなクエリ言語
- インデックスされた Elasticsearch データからインサイトを抽出するプロセスを簡素化
	- Elasticsearch のクエリ DSL よりも直感的なアプローチを提供

基本構文
フィールド:値 の形式で記述

例
- Windowsのイベントコード4625（ログイン失敗）を抽出
	- ブルートフォースやパスワード推測の検知に有効
```kql
event.code:4625
```

他のクエリ方法
- 自由テキスト検索
	- "svc-sql1" で任意フィールドに含まれる文字列を検索
	- 例 : `"svc-sql1"`
- 論理演算子
	- AND, OR, NOT を使用して複雑な条件を組み立て可能
	- 例 : `event.code:4625 AND winlog.event_data.SubStatus:0xC0000072`
- 比較演算子
	- `:>`, `:>=`, `:<`, `:<=`,`:!` など
	- 例 : `event.code:4625 AND winlog.event_data.SubStatus:0xC0000072 AND @timestamp >= "2023-03-03T00:00:00.000Z" AND @timestamp <= "2023-03-06T23:59:59.999Z"`
- ワイルドカード/正規表現
	- user.name: admin* で「admin」「administrator」「admin123」などを抽出
	- `event.code:4625 AND user.name: admin*`

### 利用可能なフィールドとクエリの特定
#### Discover機能を使う
- 検出機能を使う
	- https://www.elastic.co/docs/explore-analyze/discover

基本的なDiscover画面内の呼び方

![Elastic interface showing a side navigation toggle, search bar, histogram, and document table with network event logs. Includes time picker and index pattern selection.](https://academy.hackthebox.com/storage/modules/211/discover.png)


ここでは、最終的に以下のクエリまで辿り着く方法の紹介
```kql
event.code:4625 AND winlog.event_data.SubStatus:0xC0000072 AND @timestamp >= "2023-03-03T00:00:00.000Z" AND @timestamp <= "2023-03-06T23:59:59.999Z"
```

1. Windowsイベントログ（ログイン失敗に関連するもの）を検索する
	- 以下のような記事が出てきて、Log Event IDが4625であることがわかる
	- https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=4625
2. KQLで "4625" を自由検索すると、返却されたレコードに以下が含まれるのに気づく
	- `event.code:4625` → Elastic Common Schema (ECS) に関連
	- `winlog.event_id:4625` → Winlogbeat に関連
	- `@timestamp` → 元イベントから抽出された時刻（event.created とは異なる）
	- ![ヒストグラム、ドキュメント テーブル、フィルター オプションを使用して「4625」の検索結果を表示する Elastic インターフェース。](https://academy.hackthebox.com/storage/modules/211/discover1.png)
	- 無効化されたアカウントについては、上記リソースに「Windowsイベントログ4625のSubStatusが 0xC0000072 なら、そのアカウントは無効化されている」と記載されている
	- 再びKQLで "0xC0000072" を検索すると、返却されたレコードに winlog.event_data.SubStatus が含まれていることがわかる
		- Winlogbeatに関連するフィールド

#### Elasticのドキュメントを読む
- Discoverを使う前に、Elasticの包括的なドキュメントに慣れておくのも有効
- 以下のリソースが役にたちそうとのこと
- [エラスティック共通スキーマ（ECS）](https://www.elastic.co/guide/en/ecs/current/ecs-reference.html)
- [Elastic Common Schema (ECS) イベントフィールド](https://www.elastic.co/guide/en/ecs/current/ecs-event.html)
- [Winlogbeatフィールド](https://www.elastic.co/guide/en/beats/winlogbeat/current/exported-fields-winlog.html)
- [Winlogbeat ECSフィールド](https://www.elastic.co/guide/en/beats/winlogbeat/current/exported-fields-ecs.html)
- [Winlogbeat セキュリティモジュールフィールド](https://www.elastic.co/guide/en/beats/winlogbeat/current/exported-fields-security.html)
- [ファイルビートフィールド](https://www.elastic.co/guide/en/beats/filebeat/current/exported-fields.html)
- [Filebeat ECSフィールド](https://www.elastic.co/guide/en/beats/filebeat/current/exported-fields-ecs.html)

## ECS
- Elastic Common Schema 
- ECSはElastic Stack全体でイベントやログを統一的に表現する拡張可能な語彙
- データソースごとに異なるフィールド形式を標準化する

KQLでECSフィールドを使うメリット
- 統一ビュー
	- Windowsログ、ネットワークトラフィック、エンドポイントイベント、クラウドデータなどを同じフィールド名で検索・相関できる。
- 検索効率の向上
	- データタイプごとに異なるフィールド名を覚える必要がなく、効率的にクエリを記述できる。
- 相関の強化
	- 異なるソース間のイベント相関が容易になり、セキュリティ調査に有用。例：IPアドレスをFWログ、ネットワークログ、エンドポイントデータと突き合わせる。
- 可視化の向上
	- 一貫したフィールド命名により、Kibanaでのダッシュボードやグラフ作成が直感的になり、傾向把握や異常検出が容易に。
- Elasticソリューションとの互換性
	- Elastic Security / Observability / Machine Learning など高度な機能と完全互換。脅威ハンティング、異常検知、パフォーマンス監視を強化。
- 将来性
	- ECSはElastic Stackの基盤スキーマであり、ECSに準拠すれば今後の拡張や新機能にも対応できる。


# SOCとは
- 組織のセキュリティ状況を継続的に監視・評価する情報セキュリティ専門チームを擁する重要な施設
- 主な目的は、技術的なソリューションと包括的な手順の組み合わせを用いて、サイバーセキュリティインシデントを特定・調査・対処すること
- SOCチームは通常、熟練したセキュリティアナリスト、エンジニア、そしてセキュリティ運用を監督するマネージャーで構成される
- SIEM（セキュリティ情報イベント管理）システム、IDS/IPS（侵入検知・防御システム）、EDR（エンドポイント検知・対応）などの技術ソリューションを駆使して脅威を監視・特定する

主な機能
- 企業情報セキュリティの継続的な運用面を管理すること
- 主にセキュリティアナリストで構成され、協力してサイバーセキュリティインシデントを検出・評価・対応・報告・防止する
- SOCチームによっては、フォレンジック分析やマルウェア分析といった高度な能力を備えている場合もある
- これによりインシデントを詳細に調査し、その根本原因を解明して将来の攻撃を防ぐことが可能になる

SOC内の主な役割
- SOCディレクター：SOC全体の管理と戦略的計画を担当（予算、人員、組織のセキュリティ目標との整合性など）
- SOCマネージャー：日々の運用を監督し、チーム管理やインシデント対応の調整、他部門との連携を確保
- Tier 1アナリスト：セキュリティアラートやイベントを監視し、潜在的インシデントをトリアージして上位層にエスカレーション
- Tier 2アナリスト：エスカレーションされたインシデントを詳細に分析し、パターンや傾向を特定、脅威に対する緩和策を策定
- Tier 3アナリスト：高度な専門知識で複雑なセキュリティインシデントを処理し、スレットハンティングを実施、組織のセキュリティ態勢改善に貢献
- 検知エンジニア：SIEM, IDS/IPS, EDRなどの検知ルールやシグネチャの開発・実装・維持を担当。検知の隙間を特定し改善。
- インシデントレスポンダー：実際のインシデント対応を担当し、デジタルフォレンジック、封じ込め、修復を実施。システム復旧と再発防止を担う。
- 脅威インテリジェンスアナリスト：脅威情報を収集・分析・共有し、SOCメンバーが脅威環境を理解して能動的に防御できるよう支援。
- セキュリティエンジニア：セキュリティツールや技術基盤の開発・展開・維持を行い、SOCチームに技術的専門性を提供。
- コンプライアンス／ガバナンス担当：組織のセキュリティ実務が業界標準や規制・ベストプラクティスに準拠することを確認し、監査やレポートに対応。
- セキュリティ教育・トレーニングコーディネーター：セキュリティ研修や啓発プログラムを企画・実施し、従業員の意識向上とセキュリティ文化の醸成を推進。

SOCの進化段階

SOCは、もともとネットワーク中心のNOC（ネットワークオペレーションセンター）から進化してきた。
- SOC 1.0
	- セキュリティインテリジェンス基盤やID管理システムなどを導入したが、統合不足によりアラートが相関されず、複数のプラットフォームで作業が分散
	- ネットワークや境界防御重視だが、脅威は別ベクトルを突き始めていた。今もこの古い手法に依存する組織も存在。
- SOC 2.0
	- マルチベクトル・持続型・非同期型の高度な脅威（モバイルマルウェア、ボットネットなど）への対応が課題
	- 脅威インテリジェンスやネットワークフロー分析、レイヤー7解析を統合し、異常検知と完全な状況認識を重視
	- 事前の脆弱性管理やリスク管理、事後のフォレンジックと教訓化も含む。
- 認知型SOC（次世代SOC）
	- SOC 2.0の限界（経験不足、ビジネスとセキュリティの連携不足、標準化されたインシデント対応手順の欠如）を解決するため、学習システムを導入して意思決定を補強。成功率は常に完璧ではないが、時間とともに改善が期待される。


# MITRE ATT&CK
- **MITRE ATT&CK（Adversarial Tactics, Techniques, and Common Knowledge）** フレームワーク
	- サイバー脅威アクターが用いる戦術・技術・手順（TTPs）を体系的にまとめ、定期的に更新される包括的なリソース

セキュリティオペレーションにおけるMITRE ATT&CKのユースケース
- 検知と対応
	- 攻撃者の既知のTTPに基づき、検知・対応計画を策定することで、SOCが潜在的な脅威を特定し、能動的な対策を開発するのに役立つ
- セキュリティ評価とギャップ分析
	- 組織はATT&CKを利用して、自社のセキュリティ態勢の強みと弱みを特定し、優先的に投資すべきセキュリティコントロールを明確化できる
- SOC成熟度評価
	- SOCがさまざまなTTPを検知・対応・軽減できる能力を測定することで、SOCの成熟度を評価できる
	- この評価により改善点を特定し、リソースの優先順位付けを行い、全体的なセキュリティ態勢を強化できる
- 脅威インテリジェンス
	- 攻撃者の行動を記述するための統一言語と形式を提供し、脅威インテリジェンスの強化や内部チーム・外部関係者との協力を促進する
- サイバー脅威インテリジェンスの充実
	- 攻撃者のTTPや潜在的ターゲット、IoC（侵害指標）に関するコンテキストを提供することで、脅威インテリジェンスを補強する
	- これにより、より適切な意思決定と効果的な脅威緩和戦略が可能になる
- 行動分析の開発
	- ATT&CKのTTPをユーザーやシステムの具体的な行動にマッピングすることで、異常な活動を特定する行動分析モデルを構築できる
	- これにより検知能力が向上し、リスクを能動的に軽減できる
- レッドチーミングとペネトレーションテスト
	- 攻撃者の技術を体系的に模倣する方法を提供し、レッドチーム演習やペネトレーションテストで組織の防御能力を評価できる
- トレーニングと教育
	- ATT&CKは網羅的かつ整理されたフレームワークであり、最新の攻撃戦術や手法についてセキュリティ専門家を教育するための優れた教材となる

# SIEMユースケース開発

SIEMユースケース
- ユースケースは潜在的なセキュリティインシデントを効果的に識別・検知できるようにする
- 製品やサービスが適用できる特定の状況を示すよう設計されている
- 単純な「ログイン失敗」のような一般的シナリオから、ランサムウェア感染検知のような複雑なケースまで幅広く存在している
	- 例: ユーザー「Rob」が10回連続で認証失敗した場合、それは「パスワードを忘れた本人」による可能性も「ブルートフォース攻撃者」による可能性もある
	- これら10件のイベントはSIEMに送信され、SIEMは相関して「ブルートフォース攻撃」としてSOCチームにアラートを通知する。
	- SOCはこのログデータに基づいて適切な対応を取る

SIEMユースケース開発ライフサイクル

![Diagram of Usecase Lifecycle with stages: Requirements, Data Points, Log Validation, Design, Implementation, Documentation, Onboarding, Testing, Fine Tuning.](https://academy.hackthebox.com/storage/modules/211/usecase2.png)

1.	要件定義（Requirements）
	- ユースケースの目的を理解し、どんな状況でアラートが必要かを明確化。
	- 例：4分間で10回ログイン失敗したらブルートフォース攻撃アラートを発火。

2.	データポイントの特定（Data Points）
	- ログイン可能な全てのデータソース（Windows, Linux, サーバー, アプリなど）を確認。ログにユーザー、時刻、ソース、宛先など必要な情報が含まれることを確保。

3.	ログ検証（Log Validation）
	- 収集されるログが完全であるか検証。ユーザー認証イベント（ローカル、Web、アプリ、VPN、OWAなど）すべてでログを受信できるか確認。

4.	設計と実装（Design & Implementation）
	- どの条件でアラートを発火させるか定義。主要パラメータは「条件」「集約」「優先度」。例：4分以内に10回失敗→ブルートフォースアラート。

5.	文書化（Documentation）
	- SOP（標準手順書）に条件・優先度・エスカレーション先などを明記。

6.	オンボーディング（Onboarding）
	- いきなり本番投入せず、検証環境で誤検知を減らしてから本番へ。

7.	定期更新／チューニング（Fine-tuning）
	- アナリストのフィードバックをもとにルールを調整し、ホワイトリストを更新。


SIEMユースケース構築の手順
- ニーズやリスクを理解し、監視対象システムのアラートを設定
- 優先度・影響度を決め、キルチェーンやMITREフレームワークにマッピング
- 検知時間（TTD）、対応時間（TTR）を定義
- アラート管理用のSOPを作成
- アラートの改善プロセスを定義
- インシデント対応計画（IRP）を策定
- SLA/OLAをチーム間で設定
- 監査プロセスを実装し、アナリストの対応をレビュー
- ログ取得状況やアラート条件を文書化
- ケース管理ツールに知識ベースを整備

SIEMのフィルターで使う
widowsのevent code一覧はここから見れる
- https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/default.aspx?i=j

### 例1
OfficeアプリからMSBuildが起動された場合

![Dashboard showing MSBuild started by Office apps, with high severity and risk score 73. Includes MITRE ATT&CK tactics and custom query details.](https://academy.hackthebox.com/storage/modules/211/us1.png)

Elastic StackをSIEMとして使う実例。
- 背景: MSBuild（Microsoft Build Engine）は通常Visual Studioなどが使うが、攻撃者は悪意あるコードを組み込むために悪用する。
- 検知: OfficeやブラウザからMSBuildが実行されたら要注意。
- ユースケース設計: SIEMに検知ルールを作成。ExcelやWordからMSBuild起動があれば高リスクアラート。
- 優先度: MITREで「Defense Evasion（TA0005）」のT1127（Trusted Developer Utilities Proxy Execution）に該当。重大リスクなので「高」Severity。
- 対応: process.name, parent.name, event.action, ユーザー・端末情報を記録し、ユーザーとマシンを調査。
- チューニング: 開発者が使う正当なMSBuild利用を除外して誤検知を減らす。

SIEMユースケースは、ログイン失敗検知からMSBuildの悪用まで、具体的な脅威シナリオに基づいて設計・実装・チューニングできる
これによりSOCチームは精度高く、効率的にインシデントを検出・対応できるようになる


# SIEM
- SIEM ソリューションのダッシュボードは、複数の視覚化のためのコンテナーとして機能し、データを意味のある方法で整理して表示できるようにする
## 可視化1
elasticを使って可視化する
可視化1では、 全ユーザーを対象に、失敗したログオン試行を可視化する


この状態から、1からダッシュボードを作っていく
早速、Create new dashboardを押して、進めていく

![](https://i.imgur.com/6qGuQiu.png)

作成するときに注目するべき4つの項目

![フィルターの追加、インデックス パターン「windows*」の選択、フィールド名の検索、「縦積み上げバー」視覚化の選択などのオプションを備えた、柔軟なダッシュボード作成インターフェイス。](https://academy.hackthebox.com/storage/modules/211/visualization1.png)

1. グラフを作成する前にデータをフィルタリングできるフィルター
	- 例えば、失敗したログオン試行を表示したい場合は、一致するイベントIDのみを表示するフィルターを使用できる
		- 4625 – Failed logon attempt on a Windows system
実際の設定例

![「フィルターの追加」オプションが開いた Elastic ダッシュボード インターフェースで、演算子「is」を使用して「event.code」のフィルターを「4625」に設定しています。](https://academy.hackthebox.com/storage/modules/211/visualization2.png)

2. 使用するデータセット（インデックス）を示す
	- 様々なインフラストラクチャソースからのデータは、ネットワーク、Windows、Linuxなど、異なるインデックスに分割されるのが一般的
	- `windows*`「インデックスパターン」で指定する

![](https://i.imgur.com/R0fzkyP.png)

3. インデックスパターンの検索バー
	- データセット内に特定のフィールドが存在するかどうかを二重に確認する機能を提供する
	- 正しいデータを参照していることを確認するためのもう一つの手段
	- ここで、対象のキーワードが出てこなければ、インデックスパターンが違う可能性がある

![](https://i.imgur.com/IgeDkKY.png)


4. 可視化
- どんなふうに可視化するのかを決められる

![](https://i.imgur.com/5uuRUk8.png)

最終的にこうなったら良い

![](https://i.imgur.com/pjRTiRy.png)

## 可視化1の改良

改良案
- 視覚化ではより明確な列名を指定する必要があります
- ログオンタイプを視覚化に含める必要があります
- 視覚化の結果は並べ替えられるべきである
- DESKTOP-DPOESND、WIN-OK​​9BH1BCKSD、およびWIN-RMMGJA7T9TCのユーザー名は監視対象ではありません。
	- [コンピュータアカウントは](https://learn.microsoft.com/en-us/azure/active-directory/fundamentals/service-accounts-computer)監視すべきではない（良い習慣ではない）

列名の変更
こんな感じで、Display Nameの部分を変更できる

![](https://i.imgur.com/CNgtEIa.png)

監視対象でないユーザーを表から排除する
![](https://i.imgur.com/jglN50c.png)

これで、だいぶ見やすくなる

![](https://i.imgur.com/O8VRoZy.png)


右上の保存を押すことを忘れない

![](https://i.imgur.com/VnLKOr0.png)


## 可視化2
ここでは、無効なユーザーに対する失敗したログイン試行を監視する視覚化を作成する
-　windowsでは、Windows ログには失敗の理由を示すサブステータス値 `0xC0000072` が追加で記録される

こんな感じでフィルターを作成する
- これらのフィルターだと、user.name.keywordの中で、winlog.event_data.SubStatusが、`0xC0000072` のものだけを抽出する

![](https://i.imgur.com/fqDvBJR.png)

## 可視化3
- この可視化3では、サービスアカウントに特に関連するRDPログオン成功を監視するための可視化を作成する
- 企業内/実世界の環境では、サービスアカウントの資格情報はRDPログオンに決して使用されない
- IT運用部門から、環境内のすべてのサービスアカウントが 「svc-」から始まると聞いている

windowsのアカウントが正常にログインされているかは、event.code 4624でわかる
- https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=4624

変更したフィルター
- `winlog.logon.type`  is  `RemoteInteractive`
	- RDPログイン

![](https://i.imgur.com/jRMNTQG.png)

- `event_code` is `4624`
	- 正常ログイン

 ![](https://i.imgur.com/UkpTjkg.png)

最終的にこうなったらおっけ

![](https://i.imgur.com/MAALPYk.png)


## 可視化4
- ここでの可視化は、2023 年 3 月 5 日から現在までのローカルの「管理者」グループへのユーザーの追加または削除を監視する視覚化を作成する
- 私たちの視覚化は、次の Windows イベント ログに基づいている

- セキュリティが有効なローカルグループにメンバーが追加されたかは、event.code 4732でわかる
	- https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=4732
- セキュリティが有効なローカルグループにメンバーが削除されたかは、event.code 4733でわかる
	- https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=4733

追加または削除が起きたときでいいので、Operatorは`is one of`にする

![](https://i.imgur.com/i1xWvPW.png)

検索する対象は、管理者グループなので、このようにすれば良い

![](https://i.imgur.com/dEp7jEy.png)

時間範囲はこんな感じで、絶対値にして、設定すれば良い

![](https://i.imgur.com/YSde1PU.png)


そして、テーブルにするときにもう少し情報を追加して「誰が、どこで、何をしたか」をより分かりやすくする

- どのユーザーがグループに追加／削除されたのか？
	- → `winlog.event_data.MemberSid.keyword`
- どのグループに対して追加／削除が行われたのか？
	- → `group.name.keyword`（特に「Administrators」かどうかを確認）
- その操作は「追加」なのか「削除」なのか？
	- → `event.action.keyword`
- どのマシン上で操作が実行されたのか？
	- → `host.name.keyword`

最終的にこんな感じになれば良い
- これ、テーブルが更新されてないダメな例、時間範囲をいじったらUpdateを押さないとダメ。

![](https://i.imgur.com/ULhzK2y.png)


## アラートトリアージ
- アラートトリアージとは
	- SOCのアナリストが行う作業
	- さまざまな監視・検知システムによって生成されたセキュリティアラートを評価・優先順位付け
	- 組織のシステムやデータに対する脅威レベルや潜在的な影響を判断するプロセス
	- アラートを体系的にレビュー・分類することで、リソースを効果的に割り当て、セキュリティインシデントに適切に対応できるようにするもの

エスカレーション
- 上司・インシデント対応チーム・または組織内で意思決定と調整を行う権限を持つ人物に通知すること

SOCアナリストは、アラートの重大度、潜在的な影響、初期調査の所見など、詳細な情報を提供する

### 理想的なトリアージの流れ
1. 初期レビュー
	- メタデータ（IP・タイムスタンプなど）や関連ログを確認
2. 分類
	- あらかじめ決まっている重大度・影響・緊急度で分類
3. 相関
	- 関連アラートやIOCを突き合わせ、SIEMや脅威インテリジェンスで確認
4. アラートデータの拡充
	- パケットキャプチャや外部インテリジェンスで追加情報を収集
5. リスク評価
	- 重要資産や規制要件への影響、攻撃成功の可能性、潜在的な横方向の移動の可能性を評価
6. コンテキスト分析
	- 影響を受けたシステムや既存のセキュリティ制御、法規制面での意味を考慮
7. インシデント対応計画
	- 詳細を記録し、対応チームを編成。必要に応じ他部署と調整
8. IT運用との連携
	- 誤検知やシステム変更による影響がないか確認
	- 追加のコンテキストまたは不足している情報の必要性を評価
	- 影響を受けるシステム、最近の変更、進行中のメンテナンス活動に関する洞察を収集するために、ディスカッションや会議に参加
9. 対応実行
	- 誤検知なら処理終了。脅威が残る場合はIRへ
10. エスカレーション
	- 重大資産侵害や進行中攻撃などのトリガーに基づき上位へ通知
	- 必要に応じて外部機関（CERTや法執行）へ
11. 継続モニタリング
	- 進捗を追跡し、関係者と情報共有
12. ディエスカレーション
	- リスクが収束したら解除し、結果と教訓を共有
	- 関係者に通知し、実行したアクション、結果、および学んだ教訓の概要を提供

## トリアージ時の思考
- 与えられた環境ルールとログの差分を探す
	- サービスアカウントは専用用途 → それ以外の用途なら怪しい
	- PAW 必須 → 違ったら不正
	- root は禁止 → 出たら即異常

- 誤検知かの可能性を考え、Ops に聞くべきか／即エスカレーションか判断

- Tier 1 の役割＝“フィルタリング”
	- 「通常業務かも → Opsに相談」
	- 「明確に異常 → 上位にエスカレーション」