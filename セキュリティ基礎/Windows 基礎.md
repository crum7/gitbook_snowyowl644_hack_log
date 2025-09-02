# 認証プロセス

Linuxよりも複雑
- Windowsが複数のサブシステムやサービスを組み合わせ、さらにドメイン環境などの大規模ネットワークでの集中管理を想定した構造をとっているため

## Local Security Authority (LSA)
- 役割 
	- ユーザー認証やローカルコンピューターへのログオン処理など、Windowsのセキュリティに関する中核的な機能を担う保護されたサブシステム。  
	- コンピューターのローカルセキュリティ情報（ユーザー情報やパスワード、グループ、特権など）を保持し、他のコンポーネントに提供する。
- 機能  
	- 名前（アカウント名）とSID(Security Identifier)の相互変換。  
	- ユーザーの権限チェックや、監査ログ（イベントログ）の生成。  
	- Windows全体のログオン手続きを統括する。

## セキュリティサブシステム（Security Subsystem）
- 役割  
	- セキュリティポリシーやアカウント管理など、LSAを含むWindows認証基盤全体を統制する部分。  
	- ローカルのマシンやドメインコントローラーと連携し、セキュリティ情報やポリシーを適用する。
- 機能  
	- ユーザーアカウントやグループなど、アカウント管理機能。  
	- オブジェクトアクセスのチェック（ファイルやレジストリなどへのアクセス可否の判定）。  
	- ユーザー権限の確認と監査メッセージ（ログ）の生成。

## ドメインコントローラー (Domain Controller)
- 役割  
	- Active Directoryをホストし、ネットワーク全体のアカウントやグループポリシーを統合管理するサーバー。  
	- ユーザーやコンピューターがドメインに参加するための中心的な認証基盤となる。
- 機能  
	- ドメイン内の認証要求の処理（KerberosやNTLMなど）。  
		- Kerberos
			- 役割
				- Windows 2000以降のドメイン環境で標準的に使われる認証プロトコル。  
				- ユーザーがドメインにログオンするとき、チケットと呼ばれるデータを用いて、シングルサインオンを実現する。  
				- ネットワーク上でパスワードをやり取りしないため、比較的高い安全性を確保できる。
			- 機能  
				- Key Distribution Center (KDC)というドメインコントローラー上のサービスを中心に、チケットの発行・管理が行われる。  
				- ユーザーはまずKDCから自分専用の「TGT（Ticket Granting Ticket）」を取得し、以降はTGTを使って別のサービス用チケットをもらうことで、追加の認証なしでサービスにアクセスできる。  
				- チケットにより、ユーザーがすでに認証済みであることをサービスに示せるため、一度ログオンすると複数のサービスにログイン可能（シングルサインオン）。
			- ポイント  
				- ドメインコントローラー（KDC）が正しく動作していないと認証ができないため、ドメイン環境の信頼性や時刻の同期が重要になる。  
				- Windows環境だけでなく、Unix系システムでもKerberosを利用可能であり、相互運用が比較的しやすい。
		- NTLM
			- 役割 
				- Windows NT系のOSで使用されてきた古い認証プロトコル。  
				- 現在は主に後方互換性のために残されており、Kerberosを使えない状況やワークグループ環境などで使われることが多い。
			- 機能  
				- チャレンジ・レスポンス方式を採用している
				- ユーザーのパスワード自体を送信せずに、サーバーから送られるランダム値に対してクライアントが応答を返すことで、ユーザーが正しいパスワードを持っているかを確認する。  
				- Active Directoryドメインでは、Kerberosがデフォルトの認証方式として使われるが、Kerberosに問題がある場合やドメインに参加していないマシン間ではNTLMが使われる場合がある。
			- ポイント  
				- Kerberosに比べるとセキュリティが弱いとされており、リプレイ攻撃への対策や、パスワードハッシュが漏れた場合に悪用されるリスクなどが指摘されている。  
				- 企業や組織では、可能な限りKerberosベースの認証に移行し、NTLMは最小限の使用にとどめる方向で運用されることが多い。
	- ドメインポリシー（グループポリシー）やアカウント情報の一括適用。  
	- ユーザーやコンピューターのアカウント情報をActive Directory上に保持し、集中管理を可能にする。

## Active Directory
- 役割  
	- Windowsドメインネットワークの中核。
	- アカウント情報（ユーザーやコンピューター）、セキュリティポリシー、グループポリシーなどを一元管理するディレクトリサービス。  
	- 大規模環境でのユーザー管理やセキュリティ管理を効率化するために設計されている。
- 機能  
	- ドメイン参加しているマシンやユーザーへの一貫したポリシー適用。  
	- LDAP(Lightweight Directory Access Protocol)などのプロトコルを通じたディレクトリサービスの提供。  
	- マルチドメイン環境やフォレストといった階層構造による大規模運用のサポート。


## Windows認証プロセス図
![](https://i.imgur.com/ftpLwuq.png)
1. LogonUIとCredential Provider
    - Windowsのログオン画面（LogonUI）で、ユーザーはCredential Providerを介して自分の資格情報（パスワード、PIN、スマートカードなど）を入力する
    - Credential Providerが取得した情報はWinLogon.exeに渡される

2. WinLogon.exeとLSASS.exe    
    - WinLogon.exeはユーザーの認証を行うために、LSASS.exe（Local Security Authority Subsystem Service）と通信する
    - LSASS.exeはWindowsのセキュリティを司るプロセスで、さまざまな認証パッケージ（NTLMやKerberosなど）を呼び出して認証処理を実行する
    - LSSASS内の.dll

| Authentication Packages | 説明                                                                                                        |
| ----------------------- | --------------------------------------------------------------------------------------------------------- |
| Lsasrv.dll              | LSAサーバーサービスとして、セキュリティポリシーの適用やLSAのセキュリティパッケージマネージャ機能を担う。Negotiate関数を含み、NTLMまたはKerberosのどちらを使用するかを自動的に判断する。 |
| Msv1_0.dll              | ローカルマシンのログオン（カスタム認証が不要な場合）を担当する認証パッケージ。                                                                   |
| Samsrv.dll              | Security Accounts Manager (SAM)。ローカルのセキュリティアカウントを管理し、ローカルに保存されたポリシーを適用し、関連APIをサポートする。                     |
| Kerberos.dll            | Kerberosベースの認証に対応するセキュリティパッケージで、LSAによって読み込まれてドメイン環境でのログオンをサポートする。                                         |
| Netlogon.dll            | ネットワークベースのログオンを行うサービス（Netlogonサービス）を提供するモジュール。                                                            |
| Ntdsa.dll               | Windowsレジストリ内に新しいレコードやフォルダーを作成するために使用されるライブラリ。                                                            |


3. ローカル/非ドメイン参加マシンのログオン (NTLM) 
    - 対象のマシンがドメインに参加していない場合、ローカルアカウントの認証にNTLM（Msv1_0.dll）が使用される
    - SAM（Security Account Manager）やSamsrv.dll、Registryに保存されているアカウント情報を参照し、チャレンジ・レスポンス方式で認証を行う

4. リモート/ドメイン参加マシンのログオン (KerberosあるいはNTLM)    
    - ドメインに参加している場合、基本的にはKerberos認証（Kerberos.dll）が使用される
    - Netlogonサービス（Netlogon.dll）を通じてドメインコントローラー上のActive Directory（Ntdsa.dll）と通信し、ユーザーの資格情報を検証する
    - 何らかの理由でKerberosが使えない場合は、ドメイン参加マシンでもNTLMにフォールバックする

5. 認証後のセッション作成    
    - LSASS.exeからWinLogon.exeに「認証成功」の結果が返ると、WinLogon.exeがCreateDesktop()を呼び出し、新しいセッションとデスクトップを作成する
    - Load Profileでユーザープロファイルやレジストリを読み込み、userinit.exe・explorer.exeなどを起動してユーザーのデスクトップ環境が整備される

6. Secur32.dllやLsarv.dllなどのライブラリ    
    - WinLogon.exeやLSASS.exeが内部的に呼び出すセキュリティ関連のAPIや処理を提供するライブラリ
    - 認証処理の細部やユーザー権限のチェック、ログ（監査ログ）の生成などをサポートする

上の体制が作られる前は、GINAが使われていた

- GINA
	- Windows XPおよびそれ以前のWindows NT系システムで採用されていた一昔のログオンUI実装するための仕組み
	- 現在のWindowsではCredential Providerが使われていますが、昔のWindowsではGINAというDLLを使ってログオン画面をカスタマイズしたり、独自の認証手順を組み込んだりできるようになっていた
	- GINAの流れ
		- 具体的な流れとしては、ユーザーがログオン画面で資格情報を入力する
		- Winlogon.exe内にロードされたGINAがその情報を受け取り、LSASS（Local Security Authority Subsystem）に渡す
- Windows Vista以降はGINAが廃止され、その代わりにCredential Providerが採用されている

## SAM
- Windowsでユーザーのパスワードを保存するデータベースファイル
- ローカル・リモートでの認証に使われる
- ユーザーのパスワードは、LMハッシュかNTLMハッシュで、レジストリ構造にハッシュ化されて保存されている
- ファイルの場所は、`%SystemRoot%/system32/config/SAM`で、HKLM/SAMにマウントされている
	- 参照には、SYSTEMレベルの権限が必要
- Windowsは、セットアップ時にワークグループかドメインのどちらかに参加するように設定されている
	- ワークグループに割り当てられた場合
		- SAMデータベースをローカルで処理し、すべての既存ユーザーをこのデータベースにローカルアカウントとして保存する
	- システムがドメインに参加している場合
		- ドメインコントローラー(DC)がActive Directoryデータベースから資格情報を検証する
	- Active Directoryデータベースは、`%SystemRoot%\ntds.dit`に保存されているActive Directoryデータベース(ntbs.dit)の資格情報を検証する必要がある
- マイクロソフトは、オフラインソフトウェアのクラッキングに対するSAMデータベースのセキュリティを向上させるために、Windows NT 4.0にセキュリティ機能を導入した
	- これは`SYSKEY`（`syskey.exe`）機能で、有効にすると、SAMファイルのハードディスクコピーが部分的に暗号化され、SAMに保存されているすべてのローカルアカウントのパスワードハッシュ値がキーで暗号化される


## Credential Manager

- Credential Managerは、すべてのWindowsオペレーティングシステムに組み込まれている機能
	- ユーザーはさまざまなネットワークリソースやWebサイトにアクセスするために使用する資格情報を保存できる
	- 保存された資格情報は、各ユーザーの`Credential Locker`のユーザープロファイルに基づいて保存
		- 以下に保存される
		- `PS C:\Users\[Username]\AppData\Local\Microsoft\[Vault/Credentials]\`

この図は、Windows のログオン処理の中で Credential Provider と呼ばれる仕組みがどのように動作し、Winlogon → LSASS → Local Security Authority に至る認証フローがどのようにつながっているかを示している
Credential Manager（Credential Provider などを含む資格情報管理の仕組み）が、ユーザー入力から最終的な認証までをどのように受け渡しているかを可視化したもの

![](https://i.imgur.com/aRrKeEx.png)

1. CTRL+ALT+DEL の呼び出し
    - ユーザーが CTRL+ALT+DEL を押すことで、安全なログオン画面を起動するトリガーとなる
    - Windows はセキュリティ上の理由から、この画面を信頼できるものとして扱う

2. Logon UI の表示    
    - Winlogon がログオン画面（Logon UI）を立ち上げる
    - ここでユーザーが資格情報を入力する準備が整う

3. Credential Provider の一覧表示と資格情報の取得    
    - Logon UI には複数の Credential Provider（パスワード、スマートカード、PIN などの方式を提供するモジュール）が表示される
    - ユーザーが選択した Credential Provider によって資格情報が入力され、Credential Provider はそれを受け取る

4. 資格情報の処理    
    - Credential Provider は入力された資格情報を処理し、ユーザー名・パスワードなどを整形してシステムが認識できる形にする
    - 「Credential Provider interfaces」がそのやりとりを担い、CredUI.dll が実際の画面表示や情報の収集を行う

5. Winlogon への資格情報の返却    
    - Credential Provider で処理された情報が、Winlogon.exe と Secur32.dll に渡される
    - Winlogon が次のステップとして LSASS（Local Security Authority Subsystem Service）を呼び出すための準備をする

6. LSALogonUser 関数の呼び出し    
    - Winlogon は LSALogonUser 関数を介して LSASS に資格情報を送信する
    - これにより認証パッケージ（NTLM や Kerberos など）が起動し、ユーザー情報を検証する

7. Session 0（システムレベル）での認証処理    
    - 図の右側に示される Session 0 では、Secur32.dll や Ksecdd.sys（カーネルモード）を通じて、最終的に Local Security Authority が資格情報を認証する
    - 正しい認証情報であればログオンが許可され、間違っていればログオンが拒否される

8. 認証結果の返却とログオン続行    
    - 認証に成功すると、Winlogon に「ログオン成功」が返され、ユーザーセッション（Session 1）が有効になる
    - その後、ユーザーのプロファイルが読み込まれ、デスクトップ（Explorer.exe）が起動して通常の操作が可能になる


## NTDS
- Windowsドメインに結合されているネットワーク環境で、同じActive Directoryフォレストに属するドメインコントローラーにすべてのログオン要求を送信する
- それぞれのドメインコントローラーは、読み取り専用ドメインコントローラーを除くすべてのドメインコントローラーで同期されるNTDS.ditというファイルをホストする
- NTDS.ditは、Active Directoryにデータを格納するデータベースファイル
	- 入っているもの
	- ユーザーアカウント（ユーザー名とパスワードのハッシュ）
	- グループアカウント
	- コンピュータアカウント
	- ポリシーオブジェクトのグループ

## PowerShell
PowerShellは、コマンドシェルの機能を拡張して、`cmdlets`と呼ばれるPowerShellコマンドを実行する