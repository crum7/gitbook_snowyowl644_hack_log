![](https://i.imgur.com/MFFHLmO.png)


- URL : https://app.hackthebox.com/machines/Scepter
- #hard 
- OS : #Windows
- Machine Author(s): [EmSec](https://app.hackthebox.com/users/962022)
- Hack Date: 2025-10-06,13:32

こちらのWritupを見ながら行いました
- https://4xura.com/writeups-for-ctfs/htb/htb-writeup-scepter/
# Enumeration
Principle
1. **目に見えるものだけがすべてではない。** あらゆる視点を考慮しろ
2. 見えていることと、見えていないことを区別しろ
3. **常に情報を得る手段は存在する。** 対象をしっかり理解しろ
## Nmap
空いているポートから、ADのDCであることがわかる

| ポート       | サービス         | バージョン                                   | その他                                                              |
| --------- | ------------ | --------------------------------------- | ---------------------------------------------------------------- |
| 53/tcp    | **DNS**      | Simple DNS Plus                         |                                                                  |
| 88/tcp    | **kerberos** | Microsoft Windows Kerberos              | server time: 2025-10-06 12:38:25Z                                |
| 111/tcp   | rpcbind      | 2-4 (RPC #100000)                       | nfs / mountd 情報あり                                                |
| 135/tcp   | msrpc        | Microsoft Windows RPC                   |                                                                  |
| 139/tcp   | netbios-ssn  | Microsoft Windows netbios-ssn           |                                                                  |
| 389/tcp   | **LDAP**     | Microsoft Windows Active Directory LDAP | Domain: scepter.htb0, Site: Default-First-Site-Name / SSL証明書情報あり |
| 445/tcp   | **SMB**      | 不明（?）                                   |                                                                  |
| 464/tcp   | kpasswd5     | 不明（?）                                   |                                                                  |
| 593/tcp   | ncacn_http   | Microsoft Windows RPC over HTTP 1.0     |                                                                  |
| 636/tcp   | **LDAPS**    | Microsoft Windows Active Directory LDAP | Domain: scepter.htb0, Site: Default-First-Site-Name / SSL証明書情報あり |
| 2049/tcp  | mountd       | 1-3 (RPC #100005)                       | nfs/mountd 関連                                                    |
| 3268/tcp  | ldap         | Microsoft Windows Active Directory LDAP | Domain: scepter.htb0, Site: Default-First-Site-Name / SSL証明書情報あり |
| 3269/tcp  | ssl/ldap     | Microsoft Windows Active Directory LDAP | Domain: scepter.htb0, Site: Default-First-Site-Name / SSL証明書情報あり |
| 5985/tcp  | **WinRM**    | Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP) | `_http-title: Not Found`                                         |
| 5986/tcp  | ssl/http     | Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP) | SSL証明書あり / `_http-title: Not Found`                              |
| 9389/tcp  | **ADWS**     | .NET Message Framing                    |                                                                  |
| 47001/tcp | http         | Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP) | `_http-title: Not Found`                                         |
| 49664/tcp | msrpc        | Microsoft Windows RPC                   |                                                                  |
| 49665/tcp | msrpc        | Microsoft Windows RPC                   |                                                                  |
| 49666/tcp | msrpc        | Microsoft Windows RPC                   |                                                                  |
| 49668/tcp | msrpc        | Microsoft Windows RPC                   |                                                                  |
| 49671/tcp | msrpc        | Microsoft Windows RPC                   |                                                                  |
| 49688/tcp | ncacn_http   | Microsoft Windows RPC over HTTP 1.0     |                                                                  |
| 49689/tcp | msrpc        | Microsoft Windows RPC                   |                                                                  |
| 49690/tcp | msrpc        | Microsoft Windows RPC                   |                                                                  |
| 49691/tcp | msrpc        | Microsoft Windows RPC                   |                                                                  |
| 49705/tcp | msrpc        | Microsoft Windows RPC                   |                                                                  |
| 49723/tcp | msrpc        | Microsoft Windows RPC                   |                                                                  |
| 49729/tcp | msrpc        | Microsoft Windows RPC                   |                                                                  |
| 49752/tcp | msrpc        | Microsoft Windows RPC                   |                                                                  |



```bash
PORT      STATE SERVICE       REASON          VERSION
53/tcp    open  domain        syn-ack ttl 127 Simple DNS Plus
88/tcp    open  kerberos-sec  syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2025-10-06 12:38:25Z)
111/tcp   open  rpcbind       syn-ack ttl 127 2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/tcp6  rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  2,3,4        111/udp6  rpcbind
|   100003  2,3         2049/udp   nfs
|   100003  2,3         2049/udp6  nfs
|   100003  2,3,4       2049/tcp   nfs
|   100003  2,3,4       2049/tcp6  nfs
|   100005  1,2,3       2049/tcp   mountd
|   100005  1,2,3       2049/tcp6  mountd
|   100005  1,2,3       2049/udp   mountd
|_  100005  1,2,3       2049/udp6  mountd
135/tcp   open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 127 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: scepter.htb0., Site: Default-First-Site-Name)
|_ssl-date: 2025-10-06T12:39:39+00:00; +8h00m03s from scanner time.
| ssl-cert: Subject: commonName=dc01.scepter.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:dc01.scepter.htb
| Issuer: commonName=scepter-DC01-CA/domainComponent=scepter
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2024-11-01T03:22:33
| Not valid after:  2025-11-01T03:22:33
| MD5:   2af6:88f7:a6bf:ef50:9b84:3dc6:3df5:e018
| SHA-1: cd9a:97ee:25c8:00ba:1427:c259:02ed:6e0d:9a21:7fd9
| -----BEGIN CERTIFICATE-----
<SNIP>
|_-----END CERTIFICATE-----
445/tcp   open  microsoft-ds? syn-ack ttl 127
464/tcp   open  kpasswd5?     syn-ack ttl 127
593/tcp   open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  ssl/ldap      syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: scepter.htb0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=dc01.scepter.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:dc01.scepter.htb
| Issuer: commonName=scepter-DC01-CA/domainComponent=scepter
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2024-11-01T03:22:33
| Not valid after:  2025-11-01T03:22:33
| MD5:   2af6:88f7:a6bf:ef50:9b84:3dc6:3df5:e018
| SHA-1: cd9a:97ee:25c8:00ba:1427:c259:02ed:6e0d:9a21:7fd9
| -----BEGIN CERTIFICATE-----
<SNIP>
|_-----END CERTIFICATE-----
|_ssl-date: 2025-10-06T12:39:39+00:00; +8h00m03s from scanner time.
2049/tcp  open  mountd        syn-ack ttl 127 1-3 (RPC #100005)
3268/tcp  open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: scepter.htb0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=dc01.scepter.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:dc01.scepter.htb
| Issuer: commonName=scepter-DC01-CA/domainComponent=scepter
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2024-11-01T03:22:33
| Not valid after:  2025-11-01T03:22:33
| MD5:   2af6:88f7:a6bf:ef50:9b84:3dc6:3df5:e018
| SHA-1: cd9a:97ee:25c8:00ba:1427:c259:02ed:6e0d:9a21:7fd9
| -----BEGIN CERTIFICATE-----
<SNIP>
|_-----END CERTIFICATE-----
|_ssl-date: 2025-10-06T12:39:39+00:00; +8h00m03s from scanner time.
3269/tcp  open  ssl/ldap      syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: scepter.htb0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=dc01.scepter.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:dc01.scepter.htb
| Issuer: commonName=scepter-DC01-CA/domainComponent=scepter
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2024-11-01T03:22:33
| Not valid after:  2025-11-01T03:22:33
| MD5:   2af6:88f7:a6bf:ef50:9b84:3dc6:3df5:e018
| SHA-1: cd9a:97ee:25c8:00ba:1427:c259:02ed:6e0d:9a21:7fd9
| -----BEGIN CERTIFICATE-----
<SNIP>
|_-----END CERTIFICATE-----
|_ssl-date: 2025-10-06T12:39:39+00:00; +8h00m03s from scanner time.
5985/tcp  open  http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
5986/tcp  open  ssl/http      syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_ssl-date: 2025-10-06T12:39:39+00:00; +8h00m03s from scanner time.
| ssl-cert: Subject: commonName=dc01.scepter.htb
| Subject Alternative Name: DNS:dc01.scepter.htb
| Issuer: commonName=dc01.scepter.htb
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2024-11-01T00:21:41
| Not valid after:  2025-11-01T00:41:41
| MD5:   e84c:6894:816e:b7f5:4338:0a1f:a896:2075
| SHA-1: 4e58:3799:020d:aaf4:d5ce:0c1e:76db:32cd:5a0e:28a7
| -----BEGIN CERTIFICATE-----
<SNIP>
|_-----END CERTIFICATE-----
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
| tls-alpn: 
|_  http/1.1
9389/tcp  open  mc-nmf        syn-ack ttl 127 .NET Message Framing
47001/tcp open  http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
49664/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49665/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49666/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49668/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49671/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49688/tcp open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
49689/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49690/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49691/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49705/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49723/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49729/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49752/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC

```


ホスト名 : DC01
とりあえず、enum4-linux-ngを動かす
匿名ログインができるとかどうかを見る
- SMB
- LDAP

NFSの2049番が空いていることがわかる
- 他では見られない注目すべきポイント
## Enum4Linux-ng
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/Scepter]
└─$ enum4linux-ng -A  $Target_IP
ENUM4LINUX - next generation (v1.3.5)

 ==========================
|    Target Information    |
 ==========================
[*] Target ........... 10.129.105.186
[*] Username ......... ''
[*] Random Username .. 'bmpduneq'
[*] Password ......... ''
[*] Timeout .......... 5 second(s)

 =======================================
|    Listener Scan on 10.129.105.186    |
 =======================================
[*] Checking LDAP
[+] LDAP is accessible on 389/tcp
[*] Checking LDAPS
[+] LDAPS is accessible on 636/tcp
[*] Checking SMB
[+] SMB is accessible on 445/tcp
[*] Checking SMB over NetBIOS
[+] SMB over NetBIOS is accessible on 139/tcp

 ======================================================
|    Domain Information via LDAP for 10.129.105.186    |
 ======================================================
[*] Trying LDAP
[+] Appears to be root/parent DC
[+] Long domain name is: scepter.htb

 =============================================================
|    NetBIOS Names and Workgroup/Domain for 10.129.105.186    |
 =============================================================
[-] Could not get NetBIOS names information via 'nmblookup': timed out

 ===========================================
|    SMB Dialect Check on 10.129.105.186    |
 ===========================================
[*] Trying on 445/tcp
[+] Supported dialects and settings:
Supported dialects:
  SMB 1.0: false
  SMB 2.0.2: true
  SMB 2.1: true
  SMB 3.0: true
  SMB 3.1.1: true
Preferred dialect: SMB 3.0
SMB1 only: false
SMB signing required: true                                                                           

 =============================================================
|    Domain Information via SMB session for 10.129.105.186    |
 =============================================================
[*] Enumerating via unauthenticated SMB session on 445/tcp
[+] Found domain information via SMB
NetBIOS computer name: DC01                                                                          
NetBIOS domain name: SCEPTER                                                                         
DNS domain: scepter.htb                                                                              
FQDN: dc01.scepter.htb                                                                               
Derived membership: domain member                                                                    
Derived domain: SCEPTER                                                                              

 ===========================================
|    RPC Session Check on 10.129.105.186    |
 ===========================================
[*] Check for anonymous access (null session)
[+] Server allows authentication via username '' and password ''
[*] Check for guest access
[-] Could not establish guest session: STATUS_LOGON_FAILURE

 =====================================================
|    Domain Information via RPC for 10.129.105.186    |
 =====================================================
[+] Domain: SCEPTER
[+] Domain SID: S-1-5-21-74879546-916818434-740295365
[+] Membership: domain member

 =================================================
|    OS Information via RPC for 10.129.105.186    |
 =================================================
[*] Enumerating via unauthenticated SMB session on 445/tcp
[+] Found OS information via SMB
[*] Enumerating via 'srvinfo'
[-] Could not get OS info via 'srvinfo': STATUS_ACCESS_DENIED
[+] After merging OS information we have the following result:
OS: Windows 10, Windows Server 2019, Windows Server 2016                                             
OS version: '10.0'                                                                                   
OS release: '1809'                                                                                   
OS build: '17763'                                                                                    
Native OS: not supported                                                                             
Native LAN manager: not supported                                                                    
Platform id: null                                                                                    
Server type: null                                                                                    
Server type string: null                                                                             

 =======================================
|    Users via RPC on 10.129.105.186    |
 =======================================
[*] Enumerating users via 'querydispinfo'
[-] Could not find users via 'querydispinfo': STATUS_ACCESS_DENIED
[*] Enumerating users via 'enumdomusers'
[-] Could not find users via 'enumdomusers': STATUS_ACCESS_DENIED

 ========================================
|    Groups via RPC on 10.129.105.186    |
 ========================================
[*] Enumerating local groups
[-] Could not get groups via 'enumalsgroups domain': STATUS_ACCESS_DENIED
[*] Enumerating builtin groups
[-] Could not get groups via 'enumalsgroups builtin': STATUS_ACCESS_DENIED
[*] Enumerating domain groups
[-] Could not get groups via 'enumdomgroups': STATUS_ACCESS_DENIED

 ========================================
|    Shares via RPC on 10.129.105.186    |
 ========================================
[*] Enumerating shares
[+] Found 0 share(s) for user '' with password '', try a different user

 ===========================================
|    Policies via RPC for 10.129.105.186    |
 ===========================================
[*] Trying port 445/tcp
[-] SMB connection error on port 445/tcp: STATUS_ACCESS_DENIED
[*] Trying port 139/tcp
[-] SMB connection error on port 139/tcp: session failed

 ===========================================
|    Printers via RPC for 10.129.105.186    |
 ===========================================
[-] Could not get printer info via 'enumprinters': STATUS_ACCESS_DENIED

Completed after 28.69 seconds
```

ドメイン名が、scepter.htbであるとわかる
特にkerberos認証とかがされてることはなさそう
user列挙とかもなさそう

## SMB
明示的に匿名ログインでフォルダの列挙を試みる
```bash
┌──(kali㉿kali)-[~/Desktop/HTB/Scepter]
└─$ smbclient -N -L //$Target_IP
Anonymous login successful

        Sharename       Type      Comment
        ---------       ----      -------
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to 10.129.105.186 failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Unable to connect with SMB1 -- no workgroup available
```
特に見えるフォルダはなさそう

nxcでも試す
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/Scepter]
└─$ nxc smb scepter.htb -u 'guest' -p '' --users
SMB         10.129.105.186  445    DC01             [*] Windows 10 / Server 2019 Build 17763 x64 (name:DC01) (domain:scepter.htb) (signing:True) (SMBv1:False)
SMB         10.129.105.186  445    DC
```

## LDAP
匿名ログイン出るか試す
```bash
┌──(kali㉿kali)-[~/Desktop/HTB/Scepter]
└─$ nxc ldap scepter.htb -u 'guest' -p '' --users
LDAP        10.129.105.186  389    DC01             [*] Windows 10 / Server 2019 Build 17763 (name:DC01) (domain:scepter.htb)
LDAP        10.129.105.186  389    DC01             [-] scepter.htb\guest: STATUS_ACCOUNT_DISABLED
```
できない

## NFS
- 2049番でmountとなっているため、NFSがある可能性がある
接続してみる
```bash
┌──(kali㉿kali)-[~/Desktop/HTB/Scepter]
└─$ showmount -e $Target_IP
Export list for 10.129.105.186:
/helpdesk (everyone)
```
すると、/helpdeskというフォルダがあることがわかる

/helpdeskに接続してみる
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/Scepter]
└─$ mkdir target-NFS
sudo mount -t nfs $Target_IP:/ ./target-NFS/ -o nolock
[sudo] password for kali: 
```
接続できた

中身を見てみる
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/Scepter/target-NFS]
└─$ sudo ls -la helpdesk
total 21
drwx------ 2 nobody nogroup   64 Nov  2  2024 .
drwxrwxrwx 2 nobody nogroup   64 Oct  6  2025 ..
-rwx------ 1 nobody nogroup 2484 Nov  2  2024 baker.crt
-rwx------ 1 nobody nogroup 2029 Nov  2  2024 baker.key
-rwx------ 1 nobody nogroup 3315 Nov  2  2024 clark.pfx
-rwx------ 1 nobody nogroup 3315 Nov  2  2024 lewis.pfx
-rwx------ 1 nobody nogroup 3315 Nov  2  2024 scott.pfx
```
いくつかの証明書があることがわかる

クラックしていく
まずは、johnが読み取れるhashにする
```sh
┌──(root㉿kali)-[/home/…/HTB/Scepter/target-NFS/helpdesk]
└─# pfx2john scott.pfx > ../../scott-pfx.hash
┌──(root㉿kali)-[/home/…/HTB/Scepter/target-NFS/helpdesk]
└─# pfx2john clark.pfx > ../../clark-pfx.hash
                                                                                                     
┌──(root㉿kali)-[/home/…/HTB/Scepter/target-NFS/helpdesk]
└─# pfx2pfx2john lewis.pfx > ../../lewis-pfx.hash
```

johnでクラックする
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/Scepter]
└─$ john clark-pfx.hash scott-pfx.hash lewis-pfx.hash --wordlist=/usr/share/wordlists/rockyou.txt
Using default input encoding: UTF-8
Loaded 3 password hashes with 3 different salts (pfx, (.pfx, .p12) [PKCS#12 PBE (SHA1/SHA2) 128/128 ASIMD 4x])
Cost 1 (iteration count) is 2048 for all loaded hashes
Cost 2 (mac-type [1:SHA1 224:SHA224 256:SHA256 384:SHA384 512:SHA512]) is 256 for all loaded hashes
Will run 6 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
newpassword      (lewis.pfx)     
newpassword      (clark.pfx)     
newpassword      (scott.pfx)     
3g 0:00:00:00 DONE (2025-10-06 05:57) 6.000g/s 12288p/s 36864c/s 36864C/s 123456..iheartyou
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 
```
クラックできた。これで、3ユーザー分の証明書+秘密鍵が入ったpfxファイルをファイルを復号出来るようになった

.pfx を .pem（証明書.crt＋鍵.key）に変換する
秘密鍵の抽出
```sh
openssl pkcs12 -in clark.pfx -nocerts -out clark.key -nodes
openssl pkcs12 -in lewis.pfx -nocerts -out lewis.pfx -nodes
openssl pkcs12 -in scott.pfx -nocerts -out scott.pfx -nodes
```

証明書の抽出
```sh
openssl pkcs12 -in clark.pfx -clcerts -nokeys -out clark.crt
openssl pkcs12 -in lewis.pfx -clcerts -nokeys -out lewis.pfx
openssl pkcs12 -in scott.pfx -clcerts -nokeys -out scott.pfx
```

この抽出できた証明書と秘密鍵を使って、何かにログインできないかを試す
ここからできる
- ユーザー名がわかったので、ASREP-Roastingできる？
- SMBかLDAPにパスワード、newpasswordとしてログインできる？

## newpasswordを試す
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/Scepter]
└─$ nxc smb scepter.htb -u 'scott' -p 'newpassword' --users
nxc smb scepter.htb -u 'lewis' -p 'newpassword' --users
nxc smb scepter.htb -u 'clark' -p 'newpassword' --users
nxc smb scepter.htb -u 'baker' -p 'newpassword' --users


nxc ldap scepter.htb -u 'scott' -p 'newpassword' --users
nxc ldap scepter.htb -u 'lewis' -p 'newpassword' --users
nxc ldap scepter.htb -u 'clark' -p 'newpassword' --users
nxc ldap scepter.htb -u 'baker' -p 'newpassword' --users
SMB         10.129.105.186  445    DC01             [*] Windows 10 / Server 2019 Build 17763 x64 (name:DC01) (domain:scepter.htb) (signing:True) (SMBv1:False)
SMB         10.129.105.186  445    DC01             [-] scepter.htb\scott:newpassword STATUS_LOGON_FAILURE
SMB         10.129.105.186  445    DC01             [*] Windows 10 / Server 2019 Build 17763 x64 (name:DC01) (domain:scepter.htb) (signing:True) (SMBv1:False)
SMB         10.129.105.186  445    DC01             [-] scepter.htb\lewis:newpassword STATUS_LOGON_FAILURE
SMB         10.129.105.186  445    DC01             [*] Windows 10 / Server 2019 Build 17763 x64 (name:DC01) (domain:scepter.htb) (signing:True) (SMBv1:False)
SMB         10.129.105.186  445    DC01             [-] scepter.htb\clark:newpassword STATUS_LOGON_FAILURE
SMB         10.129.105.186  445    DC01             [*] Windows 10 / Server 2019 Build 17763 x64 (name:DC01) (domain:scepter.htb) (signing:True) (SMBv1:False)
SMB         10.129.105.186  445    DC01             [-] scepter.htb\baker:newpassword STATUS_LOGON_FAILURE
LDAP        10.129.105.186  389    DC01             [*] Windows 10 / Server 2019 Build 17763 (name:DC01) (domain:scepter.htb)
LDAP        10.129.105.186  389    DC01             [-] scepter.htb\scott:newpassword 
LDAP        10.129.105.186  389    DC01             [*] Windows 10 / Server 2019 Build 17763 (name:DC01) (domain:scepter.htb)
LDAP        10.129.105.186  389    DC01             [-] scepter.htb\lewis:newpassword 
LDAP        10.129.105.186  389    DC01             [*] Windows 10 / Server 2019 Build 17763 (name:DC01) (domain:scepter.htb)
LDAP        10.129.105.186  389    DC01             [-] scepter.htb\clark:newpassword 
LDAP        10.129.105.186  389    DC01             [*] Windows 10 / Server 2019 Build 17763 (name:DC01) (domain:scepter.htb)
LDAP        10.129.105.186  389    DC01             [-] scepter.htb\baker:newpassword
```
ダメそう

## ASREP-Roasting
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/Scepter]
└─$ impacket-GetNPUsers scepter.htb/ -dc-ip 10.129.105.186 -no-pass -usersfile valid_ad_users -format hashcat -outputfile asrep_hashes.txt
Impacket v0.13.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

[-] Kerberos SessionError: KDC_ERR_C_PRINCIPAL_UNKNOWN(Client not found in Kerberos database)
[-] Kerberos SessionError: KDC_ERR_C_PRINCIPAL_UNKNOWN(Client not found in Kerberos database)
[-] Kerberos SessionError: KDC_ERR_C_PRINCIPAL_UNKNOWN(Client not found in Kerberos database)
[-] Kerberos SessionError: KDC_ERR_C_PRINCIPAL_UNKNOWN(Client not found in Kerberos database)
```
なんか失敗する。DBに保存されてる名前は違う可能性もある

## 証明書を使う
調べると、SMBやLDAPは証明書を使ってもログインできないらしい。
WinRMは証明書でのログインが可能らしいので、ログインを試みる

### WinRMへのログイン試行
パスワードが求められるので、nopasswordとうつ
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/Scepter]
└─$ sudo openssl rsa -in baker.key -out baker_nopass.key
Enter pass phrase for baker.key:
writing RSA key
```

WinRMのログインに使うが、ログインできない
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/Scepter]
└─$ sudo evil-winrm -i 10.129.105.186 -c baker.crt -k baker_nopass.key -S
                                        
Evil-WinRM shell v3.7
                                        
Warning: Remote path completions is disabled due to ruby limitation: undefined method `quoting_detection_proc' for module Reline                                                                          
                                        
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion                                                                                     
                                        
Warning: SSL enabled
                                        
Info: Establishing connection to remote endpoint
                                        
Error: An error of type WinRM::WinRMAuthorizationError happened, message is WinRM::WinRMAuthorizationError                                                                                                
                                        
Error: Exiting with code 1

```
他の.crtと.keyも使って試すが、ログインできない,LDAPSも同様
### 証明書の中身を見てみる

## FFUF
```bash

```

## WebSite
- 
- 
- 
## WebSite Screenshot


# Foothold

```bash

```

```bash

```

```bash

```

```bash

```

```bash

```





# Lateral Movement
<横方向の動きに関する説明をここに書いてください>
```bash

```

```bash

```

```bash

```

```bash

```

```bash

```






# Privilege Escalation

```bash

```

```bash

```

```bash

```

```bash

```

```bash

```

```bash

```

```bash

```







## Notes



<このWriteupで特に重要な点や学んだことを追加で記載するセクション>

