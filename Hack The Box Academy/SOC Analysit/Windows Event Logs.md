
 コースURL : https://academy.hackthebox.com/module/216/section/2300

#  Windows イベント ログの基本
 - Windows イベントログは Windows オペレーティングシステムに組み込まれた機能
 - システム本体、実行中のアプリケーション、ETW プロバイダー、サービスなど、システムのさまざまなコンポーネントからのログを保存する
 - ログは「Application」「System」「Security」などのイベントログに分類され、そのソースや目的に基づいて整理されている
 - アクセス
	 - **Event Viewer** アプリを使ってアクセスできる
		 - 管理者ログインで、さまざまなログを見ることができる
		 -  ![Windows で「イベント ビューアー」を検索すると、開く、管理者として実行、ファイルの場所を開く、スタートにピン留め、タスク バーにピン留めなどのオプションが表示されます。](https://academy.hackthebox.com/storage/modules/216/image42.png)
	 - Windows Event Log API などの API 

イベントログの詳細な分類
- 大きく5つに分けられる
	- Application
	- Security
	- Setup
	- System
	- Forwarded Events
		- 転送されたイベント

- また、Windows イベントビューアは、**保存済みの .evtx ファイルを開いて表示する機能**を持っている
- これらは「Saved Logs（保存されたログ）」セクションに表示される

保存したログを開く様子

![Event Viewer displaying DLLHijack logs with details of Sysmon events, including registry value changes and process information.](https://academy.hackthebox.com/storage/modules/216/image89.png)

## イベントログの構成
Windows イベントログの各エントリ（＝「イベント」）の主要な構成要素
- Log Name 
	- イベントログの名前（例：Application、System、Security など）
- Source
	- イベントを記録したソフトウェア
- Event ID
	- イベントを一意に識別する番号
	- Microsoft の公式サイトでさらに調査することで追加情報を得ることができる
- Task Category
	- イベントの目的や用途を理解する助けとなる値や名前
- Level
	- イベントの深刻度（情報、警告、エラー、重大、詳細など）
- Keywords
	- 他の分類方法を超えてイベントをカテゴライズするためのフラグ
	- 例：セキュリティログでは「Audit Success」「Audit Failure」など
- User
	- イベント発生時にログオンしていたユーザーアカウント
- OpCode
	- イベントが報告する特定の操作を示すフィールド
- Logged
	- イベントが記録された日時
- Computer
	- イベントが発生したコンピューター名
- XML Data
	- 上記すべての情報に加え、追加のイベントデータが XML 形式でも含まれる
- Execution
	- イベントログからはエラーが発生した PID(プロセスID)も抽出でき、より正確な分析ができる


特に Keywords フィールド は、特定の種類のイベントでログをフィルタリングする際に非常に使える
- 興味のあるイベントを指定できるため、検索クエリの精度を高め、ログ管理をより効率的・効果的にすることができる


## セキュリティログ

例のセキュリティログ
- ARASHPARSA...$ユーザーが、正常にログインできたことを示すログ
	- Event ID : 4624は、正常にログインできたことを示す

![イベント 4624 (Microsoft Windows セキュリティ監査) のイベント ビューアー ログ。セキュリティ ID SYSTEM、アカウント名 ARASHPARSA2BB9$、ログオン タイプ 5 でアカウント ログオンが成功したことを示しています。](https://academy.hackthebox.com/storage/modules/216/image6.png)

このログの中の重要な情報
- Logon ID
	- 同じ Logon ID を持つ他のイベントと関連付けることで、セッション全体を追跡できる
- Logon Type
	- ログオンの種類を示すフィールド
	- この例では サービスログオンタイプ が指定されており、“SYSTEM” が新しいサービスを起動したことを示唆している

## カスタムXMLクエリの活用
- 分析を効率化するために、「ログオンID」を起点として関連イベントを特定するカスタムXMLクエリを作成できる
- Filter Current Log → XML → Edit Query Manually に進むと、カスタム XML クエリ言語を使ったより詳細なログ検索ができる
- クエリ作成に不慣れな場合は、**自動フィルター機能**を有効にして XML 表現への影響を確認することもできる

- クエリによって、特定のアカウント（サービスを起動した主体）に焦点を絞って、調査するコオができる

例 : セキュリティログに対し、SubjectLogonId フィールドが 0x3E7 のイベントを抽出するクエリを記述
- この値の選択は、特定の「Logon ID」と関連するイベントを相関させ、その詳細を理解するため

 ![セキュリティ ログの XML クエリを使用して、SubjectLogonId 0x3E7 でフィルタリングするイベント ビューアー フィルターを設定します。](https://academy.hackthebox.com/storage/modules/216/image7.png)




## 実際のクエリ
### 問題1
- 2022年8月3日10時23分25秒に発生したID 4624のイベントを分析してください。このセクションで概説されているのと同様の調査を実施し、監査設定の変更に関与した実行ファイルの名前を回答として提供してください。回答形式：`T_W_____.exe`

解き方
1.	左側ツリーからWindows Logs → Security をクリックする
	- セキュリティ監査イベントはここに記録される
2.	右側の「Actions」ペインでFilter Current Log… を選択する
3.	フィルタの設定画面で
	- Event ID に 4624 と入力
	- Logged を「カスタム期間」にして 2022-08-03 10:23:25 前後の数分間に絞るとよい
	- ここでは、AMかPMか分からなかったため、8/3のイベントでフィルタリングすることにした
4.	OK を押すと、指定条件のイベントが一覧される
5.	対象の 4624 (Logon event) をダブルクリックして詳細を開き、Logon ID を控える

![](https://i.imgur.com/T59pPSn.png)

GUIで、右のFindから、メモしたLogon IDを検索することで、絞り込むこともできるが、これは面倒だし、いちいちやるのめんどい
　
![](https://i.imgur.com/Y3xiFU6.png)

なので、XMLのカスタムクエリを使う
- すでに日時を絞り込んだカスタムクエリに付け足す形で**LogonID**と**監査設定に関与するevent ID(4719,4907,4902,4904)**で、絞り込む
```sh
<QueryList>
  <Query Id="0" Path="Security">
    <Select Path="Security">
      *[
        System[
          (EventID=4719 or EventID=4907 or EventID=4902 or EventID=4904)
          and TimeCreated[@SystemTime&gt;='2022-08-02T14:45:51.000Z' and @SystemTime&lt;='2022-08-04T14:44:49.999Z']
        ]
        and
        EventData[Data[@Name='SubjectLogonId']='0x3e7']
      ]
    </Select>
  </Query>
</QueryList>
```

すると、絞り込まれた中から、実行ファイルが出てくる
- 以下の写真で、もう少し下にスクロールすると、書いてある

![](https://i.imgur.com/EQ5IHyW.png)

### 問題2
前述の実行ファイルがC:\Windows\Microsoft.NET\Framework64\v4.0.30319\WPF\wpfgfx_v0400.dllの監査設定を変更したかどうかを判断するためのXMLクエリを作成します。クエリには、特定されたイベントの時刻をHH:MM:SS形式で入力します。

上のXMLクエリでフィルターした後に、Find機能で`C:\Windows\Microsoft.NET\Framework64\v4.0.30319\WPF\wpfgfx_v0400.dll`を検索して、出てきた結果を入れれば良い

![](https://i.imgur.com/WDme18b.png)


# Sysmonの基礎
- Sysmonは、システムの再起動後も常駐するWindowsシステムサービスおよびデバイスドライバー
- システムアクティビティを監視し、Windowsイベントログに記録する
	- **通常はセキュリティ イベント ログに表示されない情報をログに記録できる**
- Sysmon の主なコンポーネント
	- システム アクティビティを監視するための Windows サービス
	- システム アクティビティ データのキャプチャを支援するデバイス ドライバー
	- キャプチャされたアクティビティ データを表示するイベント ログ

Sysmonの全てのイベントタイプ
- https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon

![](https://i.imgur.com/Jvq49Td.png)

- Sysmonは、ログに記録するイベントをより細かく制御するために、XMLベースの設定ファイルを使用する
	- プロセス名やIPアドレスなどの様々な属性に基づいて、特定の種類のイベントをログに記録または除外できる
	- 便利な設定ファイルの例
		- 包括的な構成 → [https://github.com/SwiftOnSecurity/sysmon-config](https://github.com/SwiftOnSecurity/sysmon-config)
		- モジュール方式の構成 → [https://github.com/olafhartong/sysmon-modular](https://github.com/olafhartong/sysmon-modular)

## Sysmonのインストール
Windows版
- ここからダウンロードできる
	- https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon
- ダウンロードしたら、管理者権限のコマンドプロンプトを開き、以下のコマンドを実行して Sysmon をインストールできる
```cmd
C:\Tools\Sysmon> sysmon.exe -i -accepteula -h md5,sha256,imphash -l -n
```

- カスタム Sysmon 構成を利用するには、Sysmon をインストールした後に以下を実行する
```cmd
C:\Tools\Sysmon> sysmon.exe -c filename.xml
```

Linux版
- ここからダウンロードできる
	- https://github.com/microsoft/SysmonForLinux

## 検出例1: DLLハイジャックの検出
ここでは、DLLハイジャックの検出を目指す
- DLL ハイジャックを検知するためには、**イベントタイプ 7（モジュール読み込みイベント）**に注目する必要がある
![](https://i.imgur.com/9jUJ3L0.png)

- DLL ハイジャックの理解を深めるため、まずリサーチが重要
	- この記事に詳しく書いてある
	- https://www.wietzebeukema.nl/blog/hijacking-dlls-in-windows
- ここでは、脆弱な実行ファイルcalc.exeを対象とする具体的なハイジャックと、ハイジャックされる可能性のあるDLLのリストに焦点を当てる

![calc.exe と関連 DLL (CRYPTBASE.DLL、edputil.dll、MLANG.dll、PROPSYS.dll、Secur32.dll、SSPICLI.DLL、WININET.dll) およびそれらの機能を示す表。](https://academy.hackthebox.com/storage/modules/216/image19.png)

### 設定

- 検出するためには、[https://github.com/SwiftOnSecurity/sysmon-config](https://github.com/SwiftOnSecurity/sysmon-config) からダウンロードした sysmonconfig-export.xml の Sysmon 構成ファイルを修正する
- sysmonconfig-export.xml内で、RuleGroup に ImageLoad が include に設定されており、「ルールが無い場合は何も記録されない」と注記されている
 ![ImageLoad が 'include' に設定された RuleGroup と、ルールがない場合は何もログに記録されないという注意を示す XML スニペット。](https://academy.hackthebox.com/storage/modules/216/image14.png)
- これを、DLL ハイジャックを検出する場合、何も除外されないように「include」を「exclude」に変更し、必要なデータを取得できるようにする

![RuleGroup、ImageLoad が 'exclude' に設定され、ルールなしで 'include' を使用することについての注意が記された XML スニペット。](https://academy.hackthebox.com/storage/modules/216/image15.png)

上のように更新して、以下を実行する
```cmd-session
C:\Tools\Sysmon> sysmon.exe -c sysmonconfig-export.xml
```

- Sysmonの設定を修正したら、イメージ読み込みイベントの監視を開始できる
- これらのイベントを確認するには、イベントビューアーを開き、
	- 「アプリケーションとサービス」→「Microsoft」→「Windows」→「Sysmon」にアクセスする
- 簡単な確認で、対象のイベントIDが存在することがわかる

![Sysmon イベント ログに、イベント ID 7、イメージがロードされたという複数の情報エントリが表示されています。](https://academy.hackthebox.com/storage/modules/216/image17.png)

Sysmon イベント ID 7 がどのようになるかを見てみる

![Sysmon ログ エントリ: イメージがロードされました、プロセス ID 8060、イメージ mmc.exe、ImageLoaded psapi.dll、署名 true、ユーザー DESKTOP-N33HELB\Waldo。](https://academy.hackthebox.com/storage/modules/216/image18.png)

イベントログに含まれる情報
- DLL の署名状態（今回の例では Microsoft による署名あり）
- DLL を読み込んだプロセス（イメージ）
- 実際に読み込まれた DLL 名
例として、上では「MMC.exe」が「psapi.dll」を読み込んでおり、どちらも Microsoft 署名済みで System32 ディレクトリにあることが確認できる



### DLLハイジャックの実行
例として「calc.exe」と「WININET.dll」を使ってハイジャックを試してみる
- プロセスを簡素化するために、Stephen Fewer氏の「hello world」[リフレクションDLLを](https://github.com/stephenfewer/ReflectiveDLLInjection/tree/master/bin)利用できる
	- 必須ではない

実験のDLLハイジャックのための手順
- reflective_dll.x64.dll を WININET.dll にリネームする
- C:\Windows\System32 にある calc.exe と WININET.dll を書き込み可能なディレクトリ（例：デスクトップ）へ移動する
- calc.exe を実行する

すると、通常なら電卓アプリが起動するところで、代わりに **MessageBox** が表示され、ハイジャックが成功したことがわかる

![Command prompt running calc.exe, desktop showing WININET.dll and calc icons, with a popup message 'Hello from DllMain!' indicating Reflective DLL Injection.](https://academy.hackthebox.com/storage/modules/216/image20.png)

### 検知
次に、ハイジャックの影響を分析する
まず「現在のログをフィルター…」をクリックし、**モジュール読み込みイベントを表す Event ID 7** に絞ってイベントログを確認
![Filter Current Log window with options for event level, event logs set to Microsoft-Windows-Sysmon/Operational, and Event ID 7.](https://academy.hackthebox.com/storage/modules/216/image21.png)

次に、「検索...」をクリックして「calc.exe」のインスタンスを検索し、ハイジャックに関連付けられた DLL ロードを識別する
![Sysmonログエントリ: イメージがロードされました。プロセスID 6212、イメージ calc.exe、イメージロード元 WININET.dll、署名 false、ユーザー DESKTOP-N33HELB\Waldo。「calc.exe」のダイアログが開いています。](https://academy.hackthebox.com/storage/modules/216/image43.png)

これらのSysmonの出力から、IOCを観察して、効果的な検出ルールを作成できる

### IOCの観察
- calc.exe は本来 System32 に存在するもので、書き込み可能なディレクトリにあるべきではない
	- そのため、書き込み可能なディレクトリにある calc.exe のコピーは IOC となる
	- calc.exe は常に System32、もしくは Syswow64 にあるはず
- WININET.dll は本来 System32 に存在し、calc.exe が System32 以外から読み込むことはない
	- もし calc.exe が親プロセスとなって System32 以外から WININET.dll を読み込む場合、それは calc.exe 内で DLL ハイジャックが発生していることを示す
	- 他のアプリケーションでは安定性のために独自の DLL バージョンを同梱するケースもあるため、すべての「System32 外からの WININET.dll ロード」にアラートを出すのは注意が必要
	- しかし calc.exe の場合、DLL 名を攻撃者が変更して回避することはできないため、ハイジャックと断定できる
- 正規の WININET.dll は Microsoft によって署名されていますが、注入された DLL は署名されていない

- これら 3 つの IOC により、calc.exe を利用した DLL ハイジャックを効果的に検知することができる
- Sysmon やイベントログはハンティングやアラートルール作成に役立ちますが、それだけが情報源ではない

## 検出例2 :  非管理型 PowerShell / C# インジェクションの検知
