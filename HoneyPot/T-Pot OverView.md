多種多様なハニーポットを統合したオールインワンのプラットフォーム
 
[ 🍯 T-Pot - The All In One Multi Honeypot Platform 🐝](https://github.com/telekom-security/tpotce/tree/8465b4e608906b4a8f9f2a22b983b7b900800bd1)
 
↑のReadme.mdの中の「Required Ports」にT-Potの中で使用するポートと説明がされている
T-Potで使用されているハニーポットの目的と概要をまとめる

# ハニーポット一覧
1. ADBHoney
    - 模倣するサービス: Android Debug Bridge（ADB）
    - 特徴: Androidデバイスのデバッグインターフェースをエミュレートし、モバイルデバイスを標的とした攻撃を検知する。

2. Beelzebub (LLM required)
    - 模倣するサービス: SSH
    - 特徴: SSHサービスを模倣し、特に大規模言語モデル（LLM）を活用した攻撃を検知する。

3. CiscoASA
    - 模倣するサービス: Cisco Adaptive Security Appliance（ASA）
    - 特徴: Cisco ASAファイアウォールのVPNサービスをエミュレートし、関連する攻撃を検知する。

4. CitrixHoneypot
    - 模倣するサービス: Citrix
    - 特徴: CitrixのWebインターフェースを模倣し、関連する攻撃を検知する。

5. Conpot
    - 模倣するサービス: 産業制御システム（ICS）/SCADA
    - 特徴: 産業制御システムのプロトコルやサービスをエミュレートし、SCADA関連の攻撃を検知する。

6. Cowrie
    - 模倣するサービス: SSHおよびTelnet
    - 特徴: SSHおよびTelnetサービスを模倣し、ブルートフォース攻撃やシェル操作を記録する。
    - 分析の時のポイント : Cowrieは、ttylog というものを吐き出す
	    - Kibana上で、入力されたコマンドをまとめるには、「input.keyword」で新しくテーブルを作る
	    - ttylog :  攻撃者の **TTYセッション（端末操作ログ）を記録したファイルのパス
		    - `log/tty/` ディレクトリの下にあるログファイルに、攻撃者の シェル操作（キーストローク、コマンド入力など）が保存されている。
	    - このログを確認すると、攻撃者が何をしようとしたのかがわかる。
	    - 分析の時は、sshで接続して、catかcowrie-logviewer(CowrieにはTtylogを再生するツール)を使う
		    - ```cat /data/cowrie/log/tty/...```
		    - ```cowrie-logviewer /data/cowrie/log/tty/...```

7. Ddospot
    - 模倣するサービス: NTP、DNS、SSDPなどのUDPサービス
    - 特徴: DDoS増幅攻撃を検知するため、さまざまなUDPサービスをエミュレートする。

8. Dicompot
    - 模倣するサービス: DICOM（医療用画像通信プロトコル）
    - 特徴: 医療用画像通信プロトコルをエミュレートし、医療システムを標的とした攻撃を検知する。

9. Dionaea
    - 模倣するサービス: SMB、HTTP、FTP、MySQLなど
    - 特徴: 複数のサービスをエミュレートし、マルウェアの捕獲と分析を行う。
    - 分析の時のポイント: Dionaeaは `/opt/dionaea/var/dionaea/binaries/` にダウンロードされたマルウェアを保存する。
	    - sshで接続して、アクセスして確認する
	    - バイナリのハッシュを計算して、VirusTotalでチェックする
	    - ```find /opt/dionaea/var/dionaea/binaries/ -type f -exec sha256sum {} \; > malware_hashes.txt```


10. Elasticpot
    - 模倣するサービス: Elasticsearch
    - 特徴: Elasticsearchサービスを模倣し、関連する攻撃を検知する。

11. Endlessh
    - 模倣するサービス: SSH
    - 特徴: SSHターピットとして機能し、攻撃者の接続を長時間保持することでリソースを消耗させる。

12. Galah (LLM required)
    - 模倣するサービス: Webサービス
    - 特徴: 大規模言語モデル（LLM）を活用したハニーポットで、Webベースの攻撃を検知する。

13. Go-pot
    - 模倣するサービス: 特定のWebサービス
    - 特徴: Go言語で実装されたハニーポットで、特定のWebサービスをエミュレートする。

14. H0neytr4p
    - 模倣するサービス: 複数のサービス
    - 特徴: 多目的ハニーポットで、さまざまなサービスへの攻撃を検知する。

15. Heralding
    - 模倣するサービス: FTP、SSH、Telnetなどの認証サービス
    - 特徴: 認証サービスをエミュレートし、攻撃者が試行する資格情報をキャプチャする。

16. Honeyaml
    - 模倣するサービス: 複数のサービス
    - 特徴: YAMLベースの設定で、さまざまなサービスをエミュレートする。

17. IPPHoney
    - 模倣するサービス: Internet Printing Protocol（IPP）
    - 特徴: IPPサービスをエミュレートし、プリンター関連の攻撃を検知する。

18. Log4Pot
    - 模倣するサービス: Log4j
    - 特徴: Log4jの脆弱性をエミュレートし、関連する攻撃を検知する。

19. Mailoney
    - 模倣するサービス: SMTP
    - 特徴: SMTPサービスをエミュレートし、メールサーバーへの攻撃を検知する。

20. Medpot
    - 模倣するサービス: HL7（Health Level 7）
    - 特徴: 医療情報交換プロトコルであるHL7をエミュレートし、医療システムを標的とした攻撃や不正アクセスの試行を検知する。

21. Miniprint
    - 模倣するサービス: JetDirect（プリンターポート）
    - 特徴: ネットワークプリンターのJetDirectポートをエミュレートし、プリンターを標的とした攻撃や不正な印刷ジョブの送信を検知する。

22. qHoneypots
    - 模倣するサービス: 複数の一般的なサービス
    - 特徴: FTP、SSH、Telnet、HTTP、MySQL、RDPなど、さまざまな一般的なサービスをエミュレートし、幅広い攻撃手法の検知を目的とする。

23. Redishoneypot
    - 模倣するサービス: Redis
    - 特徴: オープンソースのインメモリデータベースであるRedisをエミュレートし、未認証のアクセスや不正な操作を試みる攻撃を検知する。

24. SentryPeer
    - 模倣するサービス: SIP（Session Initiation Protocol）
    - 特徴: VoIP通信で使用されるSIPをエミュレートし、不正な通話の試行やサービス拒否（DoS）攻撃など、VoIP関連の脅威を検知する。

25. Snare (Tanner)
    - 模倣するサービス: HTTPおよびWebアプリケーション
    - 特徴: HTTPサービスや一般的なWebアプリケーションの脆弱性をエミュレートし、SQLインジェクションやディレクトリトラバーサルなどのWeb攻撃を検知する。

26. Wordpot
    - 模倣するサービス: WordPress
    - 特徴: 人気のあるCMSであるWordPressをエミュレートし、特有の脆弱性を狙った攻撃やプラグインの脆弱性を悪用する試行を検知する。




