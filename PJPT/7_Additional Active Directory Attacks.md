脆弱性を利用した攻撃
- ペンテストでは、最後の一手として残しておくべき
- また、実際に実行するために、脆弱性をチェックするgitのリポジトリを使って確認する
- 実際に実行するときは、顧客に自身が脆弱であることを知っているか、また実行していいのか確認するべき
- ZeroLogonなどでは、ADを破壊する可能性があるので、ZeroLogonはペンテストでは行わない

## PrintNitghtMare
- [[Pentest_Technique/Active Directory#PrintNightmare]]

## NoPac
- [[Pentest_Technique/Active Directory#NoPac (Sam_The_Admin 脆弱性)]]

## ZeroLogon
- 2020年に発見された脆弱性
- この攻撃を行うときの問題
	- パスワードを復元しないとドメインコントローラーが破損してしまう
	- 本当の環境では、DCを破壊すると、とても大変になるのでやらないのが自分のため
- https://github.com/dirkjanm/CVE-2020-1472
