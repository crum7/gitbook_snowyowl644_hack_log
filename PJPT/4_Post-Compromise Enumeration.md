- アカウントが侵害された場合に何が起こるか
- アカウントを見つけるか、侵害する必要がある

# LDAPドメインダンプ

IPv6を使ったDNSなりすまし攻撃の時と同様にLDAPドメインダンプを行える
同じように、ドメインユーザーや管理者を取得できる。jsonやhtmlで結果が返される
```sh
sudo ldapdomaindump ldaps://[DCのIP] -u 'ドメイン名\ユーザー名'　-p 'パスワード'
```

例
```sh
sudo ldapdomaindump ldaps://192.168.56.101 -u 'MARVEL\fcastle' -p Password1
```

結果として得られる
- HTMLのDescriptionにパスワードが書いてあるかもしれないから、注意深く見る
	- また、ldapdomaindumpのdescriptionには書いてなくても、ldapsearchでも見つかるinfoっていうステータスにパスワードが書いてあることもある
	- なので、ldapdomaindumpで、Remote Management Usersを確認して、詳細は、ldapsearchで確認すると良いかも
	- 実際例 : [[Support#Lateral Movement]]
- ドメインコンピュータだと、ネットワーク内にあるコンピュータ一覧が見られる
	- 古いOSやアーキテクチャを狙える
![](https://i.imgur.com/uCNqBrS.png)

# BloodHound
[[CheatSheet/Active Directory#BloodHound / SharpHound]]

# PlumHound
- BloodHoundのプラグインみたいな感じ
- BloodHoundで、視覚的にごちゃっとなっているのをPlumHoundで明確にレポートにすることができる
- PlumHoundを利用する時は、BloodHoundを立ち上げる必要がある

インストール
```sh
git clone https://github.com/PlumHound/PlumHound.git
cd PlumHound
pip install -r requirements.txt
```

簡単なスキャン
```sh
python PlumHound.py --easy -p [neo4jのパスワード]
```

デフォルトのルールでレポートの作成
```sh
python PlumHound.py -x tasks/default.tasks -p [neo4jのパスワード]
```

作成したレポートの表示
```sh
cd reports
firefox index.html
```

こんな感じでレポートができる
![](https://i.imgur.com/5dIAHxW.png)


# PingCastle
- Active Directory（AD）環境のセキュリティ診断に特化した無料の自動監査ツール
	- 点数化して、実際に問題があるところを洗ってくれる　
	- 侵入したユーザーがローカル管理者だったら、実行する
		- 実行には管理者権限が必要
- HTML形式の詳細なレポート（グラフ・リスク・推奨対策付き）を出力する
	- パスワードポリシー、過剰権限、SIDHistory、古いオブジェクトなどが出力される
	- 
