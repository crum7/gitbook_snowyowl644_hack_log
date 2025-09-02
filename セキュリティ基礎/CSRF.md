# 概要
クロスサイト・リクエスト・フォージェリ
- 認証・ログイン済みのユーザーを騙して、意図しないリクエストを送らせる攻撃
	- CSRF攻撃は 「ブラウザがクッキーを自動送信する」性質を悪用して発生する。
- 攻撃者は 被害者のブラウザを利用して、別のサイトに不正なリクエストを送信し、被害者のアカウントで意図しない操作を実行させる。
	- 被害者がログインしてるからこそできる攻撃


# 攻撃の流れ
1. ユーザーが認証済みの状態（ログイン中）でいる
	- 例えば、銀行やECサイト、SNSにログインしている。
	- **ログインした状態のCoookieを持っている。**
1. 攻撃者がユーザーを騙して悪意のあるページへ誘導
	- メール・SNS・広告などでリンクをクリックさせる
	- 例：「クリックすると無料プレゼント！」のような偽サイト
2. 悪意のあるリクエストが自動的に送信される
	- ユーザーが偽サイトを開いた瞬間に、下のようなコードが実行される
		- `<img src="https://bank.com/transfer?to=attacker&amount=1000" />`
	- ユーザーのブラウザが 自動的に銀行のサーバーにリクエストを送信
	- **ログインした状態のクッキーが自動で送信されるため、サーバーは正規のユーザーのリクエストと誤認して、このリクエストが通ってしまう**
3. 攻撃者の意図したアクションが実行される
	- 例：勝手に攻撃者の口座に送金される、アカウントのメールアドレスが変更されるなど


# GETリクエストを悪用したCSRF攻撃
攻撃者は、ユーザーがクリックするだけで不正なリクエストを送信させることができる。

 例：銀行アプリの送金URL
 `GET https://xymbank.com/online/transfer?amount=1000&account=654585`

悪用の例
攻撃者は以下のような手法で、ユーザーにこのURLを踏ませる
```
<a href="https://xymbank.com/online/transfer?amount=1000&account=654585">
  クリックするとAmazonギフトカードがもらえます！
</a>
```
→ ログイン中のユーザーがこのリンクを踏むと、銀行のサーバーはリクエストを正規のものと判断し、送金が実行される。

# POSTリクエストを悪用したCSRF攻撃
GETリクエストではなく、POSTリクエストを必要とする場合も、攻撃者は自動送信されるフォームを埋め込むことで攻撃が可能

例 銀行の送金フォーム
```
<form action="https://xymbank.com/online/transfer" method="POST">
  <input type="hidden" name="amount" value="500">
  <input type="hidden" name="account" value="654585">
</form>
<script>
  document.forms[0].submit();
</script>
```
→ユーザーがこのページを開くだけで、自動的に送金処理が実行される。


 # CSRF攻撃を防ぐ対策
##  CSRFトークンの導入
- サーバー側でリクエストごとに一意のトークンを発行し、それが正しいか検証する。
```
<form action="/transfer" method="POST">
  <input type="hidden" name="csrf_token" value="abc123xyz">
  <input type="text" name="amount" value="1000">
  <button type="submit">送金</button>
</form>
```
トークンがない・不正な値の場合、サーバー側でリクエストを拒否する。

##  `SameSite` Cookie属性を設定

クッキーがクロスサイトリクエストで送信されないように設定する。
`Set-Cookie: session=abc123xyz; Secure; HttpOnly; SameSite=Strict`
- `Strict`: クロスサイトリクエストではクッキーを送らない（推奨）
- `Lax`: GETリクエストなら送るが、POSTでは送らない
- `None`: すべてのリクエストでクッキーを送る（危険！）

## Referer ヘッダーを検証
リクエストの送信元を確認し、異なるサイトからのリクエストを拒否する。
```
def validate_referer(request):
    if not request.headers.get("Referer").startswith("https://your-site.com"):
        return "Unauthorized request"

```
ただし、Refererはブラウザ設定で削除できるため、補助的な対策として使う。

CSRF攻撃は 「ブラウザがクッキーを自動送信する」性質を悪用して発生する。
SameSite=Strict でクッキーを保護するのが簡単で効果的。
CSRFトークンを使うことで、正規のリクエストと悪意のあるリクエストを区別できる。