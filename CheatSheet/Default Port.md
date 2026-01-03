
## DCの条件判断
DCである可能性が高いポートの確認
- `88/tcp` (Kerberos) - DCの可能性高
- `135/tcp` (RPC)
- `139/tcp`, `445/tcp` (SMB)
- `389/tcp` (LDAP) - DCが持つディレクトリサービス
- `636/tcp` (LDAPS)
- `3268/tcp`, `3269/tcp` (Global Catalog) - ほぼ確定でDC

| サービス名                                       | プロトコル   | デフォルトポート      | 説明                                                                          |
| ------------------------------------------- | ------- | ------------- | --------------------------------------------------------------------------- |
| Echo                                        | TCP/UDP | 7             |                                                                             |
| Discard                                     | TCP/UDP | 9             |                                                                             |
| Daytime                                     | TCP/UDP | 13            |                                                                             |
| QOTD                                        | TCP/UDP | 17            |                                                                             |
| CHARGEN                                     | TCP/UDP | 19            |                                                                             |
| FTP (Control)                               | TCP     | 21            |                                                                             |
| SSH                                         | TCP     | 22            |                                                                             |
| Telnet                                      | TCP     | 23            |                                                                             |
| SMTP                                        | TCP     | 25            |                                                                             |
| Time                                        | TCP/UDP | 37            |                                                                             |
| WHOIS                                       | TCP     | 43            |                                                                             |
| DNS                                         | UDP/TCP | 53            | 今後はTCPに依存する割合が多くなりそう<br>現状 : 基本的にUDP<br>パケットサイズが大きくてUDPで受け取れない時は、TCPで通信<br> |
| DHCP (Server)                               | UDP     | 67            |                                                                             |
| DHCP (Client)                               | UDP     | 68            |                                                                             |
| TFTP                                        | UDP     | 69            |                                                                             |
| Gopher                                      | TCP     | 70            |                                                                             |
| HTTP                                        | TCP     | 80            |                                                                             |
| kerberos-sec                                | TCP     | 88            | [[Pentest_Technique/Linux・Windows共通#Kerberos]]                              |
| POP3                                        | TCP     | 110           |                                                                             |
| NTP                                         | UDP     | 123           |                                                                             |
| NetBIOS経由SMB/Samba                          | TCP/UDP | 137, 138, 139 |                                                                             |
| IMAP                                        | TCP     | 143           |                                                                             |
| SNMP                                        | UDP     | 161           |                                                                             |
| SNMP Trap                                   | UDP     | 162           |                                                                             |
| Rsync                                       | TCP     | 385           | SMBとおんなじ感じでファイル共有あるかも、要確認                                                   |
| LDAP                                        | TCP/UDP | 389           |                                                                             |
| HTTPS                                       | TCP     | 443           |                                                                             |
| SMB/Samba                                   | TCP     | 445           |                                                                             |
| SMTPS                                       | TCP     | 465           |                                                                             |
| IPSec ISAKMP                                | UDP     | 500           |                                                                             |
| Syslog                                      | UDP     | 514           |                                                                             |
|                                             |         |               |                                                                             |
| LDAPS                                       | TCP     | 636           | LDAP SSL                                                                    |
| FTPS (Data)                                 | TCP     | 989           |                                                                             |
| FTPS (Control)                              | TCP     | 990           |                                                                             |
| IMAPS                                       | TCP     | 993           |                                                                             |
| POP3S                                       | TCP     | 995           |                                                                             |
| MS SQL                                      | TCP     | 1433          | Microsoft SQL Server                                                        |
| MS SQL                                      | TCP     | 1434          | Microsoft SQL Server                                                        |
| Oracle DB                                   | TCP     | 1521          |                                                                             |
| NFS                                         | TCP     | 2049          | Windowsでは稀で、検証が必要であり、キーとなる可能性がある、showmountコマンドで接続できる                        |
| MS SQL                                      | TCP     | 2433          | MSSQLの隠しモード                                                                 |
| MySQL                                       | TCP     | 3306          | デフォルト                                                                       |
|                                             |         |               |                                                                             |
| RDP                                         | TCP     | 3389          |                                                                             |
| NAT-T                                       | UDP     | 4500          |                                                                             |
| SIP                                         | TCP/UDP | 5060          |                                                                             |
| SIP (TLS)                                   | TCP     | 5061          |                                                                             |
| LLMNR                                       | UDP     | 5355          |                                                                             |
| PostgreSQL                                  | TCP     | 5432          |                                                                             |
| VNC                                         | TCP     | 5900          |                                                                             |
| WinRM                                       | TCP     | 5985          |                                                                             |
| IRC                                         | TCP     | 6667          |                                                                             |
| ADWS                                        | TCP     | 9389          | Active Directory Web Services                                               |
| Active Directory Management Gateway Service | TCP     | 9389          |                                                                             |
| WinRMって書いてあるけどアクセスできないことが多い                 | http    | 47001         | http-server-header: Microsoft-HTTPAPI/2.0                                   |
| RPC によってランダムに割り当てられた非特権 TCP ポート             | TCP     |               | 49152 ～ 65535                                                               |

引用・参考
- Windows のサービス概要およびネットワーク ポート要件
	- https://learn.microsoft.com/ja-jp/troubleshoot/windows-server/networking/service-overview-and-network-port-requirements