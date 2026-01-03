![](https://i.imgur.com/MFFHLmO.png)


- URL : https://app.hackthebox.com/machines/Certified
- #medium 
- OS : #Windows
- Machine Author(s): [ruycr4ft](https://app.hackthebox.com/users/1253217)
- Hack Date: 2025-11-26,23:08

# Machine Information

As is common in Windows pentests, you will start the Certified box with credentials for the following account: Username: judith.mader Password: judith09
## judith.maderの認証情報
judith.mader : judith09
# Enumeration
Principle
1. **目に見えるものだけがすべてではない。** あらゆる視点を考慮しろ
2. 見えていることと、見えていないことを区別しろ
3. **常に情報を得る手段は存在する。** 対象をしっかり理解しろ

## Nmap

どのポートが空いている?

以下の出力をこの形にまとめてほしい

| ポート   | サービス | バージョン                                                          | その他 |
| ----- | ---- | -------------------------------------------------------------- | --- |

```bash
PORT      STATE SERVICE       REASON          VERSION
53/tcp    open  domain        syn-ack ttl 127 Simple DNS Plus
88/tcp    open  kerberos-sec  syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2025-11-26 21:23:08Z)
135/tcp   open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 127 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: certified.htb0., Site: Default-First-Site-Name)
|_ssl-date: 2025-11-26T21:24:52+00:00; +7h00m00s from scanner time.
| ssl-cert: Subject: 
| Subject Alternative Name: DNS:DC01.certified.htb, DNS:certified.htb, DNS:CERTIFIED
| Issuer: commonName=certified-DC01-CA/domainComponent=certified
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-06-11T21:05:29
| Not valid after:  2105-05-23T21:05:29
| MD5:   ac8a:4187:4d19:237f:7cfa:de61:b5b2:941f
| SHA-1: 85f1:ada4:c000:4cd3:13de:d1c2:f3c6:58f7:7134:d397
| -----BEGIN CERTIFICATE-----
| MIIGBjCCBO6gAwIBAgITeQAAAASyK000VBwyGAAAAAAABDANBgkqhkiG9w0BAQsF
| ADBMMRMwEQYKCZImiZPyLGQBGRYDaHRiMRkwFwYKCZImiZPyLGQBGRYJY2VydGlm
| aWVkMRowGAYDVQQDExFjZXJ0aWZpZWQtREMwMS1DQTAgFw0yNTA2MTEyMTA1Mjla
| GA8yMTA1MDUyMzIxMDUyOVowADCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoC
| ggEBAKxmajneO9wN1G0eh2Ir/K3fG2mjvtJBduOYuM2muC4YiUO9nnknPzRXbOHN
| lNrfFlfMM8vF22qiOWNOAqZy0o6xXOxCzYIaRE2gL9DIfjjQuEXY2im5VgTo4VAI
| ntc4L6xoKOzxIn8XHjXe6zdGEc/X1fxXtwTsyCknT2eZJsc3YjyaefyjYAXpLjjE
| dnhRGaadShC9lY9UNBVsfCQ8c6JNY7f+XciCgp3cDy5J09/cnpCKhW0XlFnXKx0n
| d0VyNM0B1wvU2G6823wKUZKUNzYRWzkl3L/k4Id2CxpPTV7ExOEbnIsiBJU9rijg
| uByxDydofthnDyFAiDQ/qyez4CUCAwEAAaOCAykwggMlMDgGCSsGAQQBgjcVBwQr
| MCkGISsGAQQBgjcVCIfpnVqGp+FghYmdJ4HW1CmEvYtxgWwBIQIBbgIBAjAyBgNV
| HSUEKzApBggrBgEFBQcDAgYIKwYBBQUHAwEGCisGAQQBgjcUAgIGBysGAQUCAwUw
| DgYDVR0PAQH/BAQDAgWgMEAGCSsGAQQBgjcVCgQzMDEwCgYIKwYBBQUHAwIwCgYI
| KwYBBQUHAwEwDAYKKwYBBAGCNxQCAjAJBgcrBgEFAgMFMB0GA1UdDgQWBBR9WLee
| Ma0LzKnM8ZrvzMNE41aWhTAfBgNVHSMEGDAWgBTs+xJAFaG9x9EuOy5NS3LAYt8r
| 9TCBzgYDVR0fBIHGMIHDMIHAoIG9oIG6hoG3bGRhcDovLy9DTj1jZXJ0aWZpZWQt
| REMwMS1DQSxDTj1EQzAxLENOPUNEUCxDTj1QdWJsaWMlMjBLZXklMjBTZXJ2aWNl
| cyxDTj1TZXJ2aWNlcyxDTj1Db25maWd1cmF0aW9uLERDPWNlcnRpZmllZCxEQz1o
| dGI/Y2VydGlmaWNhdGVSZXZvY2F0aW9uTGlzdD9iYXNlP29iamVjdENsYXNzPWNS
| TERpc3RyaWJ1dGlvblBvaW50MIHFBggrBgEFBQcBAQSBuDCBtTCBsgYIKwYBBQUH
| MAKGgaVsZGFwOi8vL0NOPWNlcnRpZmllZC1EQzAxLUNBLENOPUFJQSxDTj1QdWJs
| aWMlMjBLZXklMjBTZXJ2aWNlcyxDTj1TZXJ2aWNlcyxDTj1Db25maWd1cmF0aW9u
| LERDPWNlcnRpZmllZCxEQz1odGI/Y0FDZXJ0aWZpY2F0ZT9iYXNlP29iamVjdENs
| YXNzPWNlcnRpZmljYXRpb25BdXRob3JpdHkwOgYDVR0RAQH/BDAwLoISREMwMS5j
| ZXJ0aWZpZWQuaHRigg1jZXJ0aWZpZWQuaHRigglDRVJUSUZJRUQwTgYJKwYBBAGC
| NxkCBEEwP6A9BgorBgEEAYI3GQIBoC8ELVMtMS01LTIxLTcyOTc0Njc3OC0yNjc1
| OTc4MDkxLTM4MjAzODgyNDQtMTAwMDANBgkqhkiG9w0BAQsFAAOCAQEAiUUJN4vt
| 459tCI43Rt0UQcaD1vWBs5AExrx2GxaZhj7r/mi7GCfFtVrlnDw70APgBb0Jzzq/
| LnF4q1yChWUxFvLeAyPbG+hLvk9OWvb2rmCK5S7RJIcwvJp2if8OP2WVuDvmdoyi
| xy+bc8JuIZtcACdlOIVsJlDU2NaPnepd1mV2lAOE8uUkB90ZvsCfYifAPwYuPVtH
| JpZihj6kismL/7rJ/8ZTsf2qbnttf1snzQvsdiNHFUMqxi7fY4mq+E1w+0BmFnLw
| GYiHqoY9bd5Ok+wz9YSJcJpKoHFnj5ObPz6JdFT/dlXAyZkmylijfMNbJ6x22hgI
| piE6bLwDeUY3DQ==
|_-----END CERTIFICATE-----
445/tcp   open  microsoft-ds? syn-ack ttl 127
464/tcp   open  kpasswd5?     syn-ack ttl 127
593/tcp   open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  ssl/ldap      syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: certified.htb0., Site: Default-First-Site-Name)
|_ssl-date: 2025-11-26T21:24:53+00:00; +7h00m00s from scanner time.
| ssl-cert: Subject: 
| Subject Alternative Name: DNS:DC01.certified.htb, DNS:certified.htb, DNS:CERTIFIED
| Issuer: commonName=certified-DC01-CA/domainComponent=certified
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-06-11T21:05:29
| Not valid after:  2105-05-23T21:05:29
| MD5:   ac8a:4187:4d19:237f:7cfa:de61:b5b2:941f
| SHA-1: 85f1:ada4:c000:4cd3:13de:d1c2:f3c6:58f7:7134:d397
| -----BEGIN CERTIFICATE-----
| MIIGBjCCBO6gAwIBAgITeQAAAASyK000VBwyGAAAAAAABDANBgkqhkiG9w0BAQsF
| ADBMMRMwEQYKCZImiZPyLGQBGRYDaHRiMRkwFwYKCZImiZPyLGQBGRYJY2VydGlm
| aWVkMRowGAYDVQQDExFjZXJ0aWZpZWQtREMwMS1DQTAgFw0yNTA2MTEyMTA1Mjla
| GA8yMTA1MDUyMzIxMDUyOVowADCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoC
| ggEBAKxmajneO9wN1G0eh2Ir/K3fG2mjvtJBduOYuM2muC4YiUO9nnknPzRXbOHN
| lNrfFlfMM8vF22qiOWNOAqZy0o6xXOxCzYIaRE2gL9DIfjjQuEXY2im5VgTo4VAI
| ntc4L6xoKOzxIn8XHjXe6zdGEc/X1fxXtwTsyCknT2eZJsc3YjyaefyjYAXpLjjE
| dnhRGaadShC9lY9UNBVsfCQ8c6JNY7f+XciCgp3cDy5J09/cnpCKhW0XlFnXKx0n
| d0VyNM0B1wvU2G6823wKUZKUNzYRWzkl3L/k4Id2CxpPTV7ExOEbnIsiBJU9rijg
| uByxDydofthnDyFAiDQ/qyez4CUCAwEAAaOCAykwggMlMDgGCSsGAQQBgjcVBwQr
| MCkGISsGAQQBgjcVCIfpnVqGp+FghYmdJ4HW1CmEvYtxgWwBIQIBbgIBAjAyBgNV
| HSUEKzApBggrBgEFBQcDAgYIKwYBBQUHAwEGCisGAQQBgjcUAgIGBysGAQUCAwUw
| DgYDVR0PAQH/BAQDAgWgMEAGCSsGAQQBgjcVCgQzMDEwCgYIKwYBBQUHAwIwCgYI
| KwYBBQUHAwEwDAYKKwYBBAGCNxQCAjAJBgcrBgEFAgMFMB0GA1UdDgQWBBR9WLee
| Ma0LzKnM8ZrvzMNE41aWhTAfBgNVHSMEGDAWgBTs+xJAFaG9x9EuOy5NS3LAYt8r
| 9TCBzgYDVR0fBIHGMIHDMIHAoIG9oIG6hoG3bGRhcDovLy9DTj1jZXJ0aWZpZWQt
| REMwMS1DQSxDTj1EQzAxLENOPUNEUCxDTj1QdWJsaWMlMjBLZXklMjBTZXJ2aWNl
| cyxDTj1TZXJ2aWNlcyxDTj1Db25maWd1cmF0aW9uLERDPWNlcnRpZmllZCxEQz1o
| dGI/Y2VydGlmaWNhdGVSZXZvY2F0aW9uTGlzdD9iYXNlP29iamVjdENsYXNzPWNS
| TERpc3RyaWJ1dGlvblBvaW50MIHFBggrBgEFBQcBAQSBuDCBtTCBsgYIKwYBBQUH
| MAKGgaVsZGFwOi8vL0NOPWNlcnRpZmllZC1EQzAxLUNBLENOPUFJQSxDTj1QdWJs
| aWMlMjBLZXklMjBTZXJ2aWNlcyxDTj1TZXJ2aWNlcyxDTj1Db25maWd1cmF0aW9u
| LERDPWNlcnRpZmllZCxEQz1odGI/Y0FDZXJ0aWZpY2F0ZT9iYXNlP29iamVjdENs
| YXNzPWNlcnRpZmljYXRpb25BdXRob3JpdHkwOgYDVR0RAQH/BDAwLoISREMwMS5j
| ZXJ0aWZpZWQuaHRigg1jZXJ0aWZpZWQuaHRigglDRVJUSUZJRUQwTgYJKwYBBAGC
| NxkCBEEwP6A9BgorBgEEAYI3GQIBoC8ELVMtMS01LTIxLTcyOTc0Njc3OC0yNjc1
| OTc4MDkxLTM4MjAzODgyNDQtMTAwMDANBgkqhkiG9w0BAQsFAAOCAQEAiUUJN4vt
| 459tCI43Rt0UQcaD1vWBs5AExrx2GxaZhj7r/mi7GCfFtVrlnDw70APgBb0Jzzq/
| LnF4q1yChWUxFvLeAyPbG+hLvk9OWvb2rmCK5S7RJIcwvJp2if8OP2WVuDvmdoyi
| xy+bc8JuIZtcACdlOIVsJlDU2NaPnepd1mV2lAOE8uUkB90ZvsCfYifAPwYuPVtH
| JpZihj6kismL/7rJ/8ZTsf2qbnttf1snzQvsdiNHFUMqxi7fY4mq+E1w+0BmFnLw
| GYiHqoY9bd5Ok+wz9YSJcJpKoHFnj5ObPz6JdFT/dlXAyZkmylijfMNbJ6x22hgI
| piE6bLwDeUY3DQ==
|_-----END CERTIFICATE-----
3268/tcp  open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: certified.htb0., Site: Default-First-Site-Name)
|_ssl-date: 2025-11-26T21:24:52+00:00; +7h00m00s from scanner time.
| ssl-cert: Subject: 
| Subject Alternative Name: DNS:DC01.certified.htb, DNS:certified.htb, DNS:CERTIFIED
| Issuer: commonName=certified-DC01-CA/domainComponent=certified
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-06-11T21:05:29
| Not valid after:  2105-05-23T21:05:29
| MD5:   ac8a:4187:4d19:237f:7cfa:de61:b5b2:941f
| SHA-1: 85f1:ada4:c000:4cd3:13de:d1c2:f3c6:58f7:7134:d397
| -----BEGIN CERTIFICATE-----
| MIIGBjCCBO6gAwIBAgITeQAAAASyK000VBwyGAAAAAAABDANBgkqhkiG9w0BAQsF
| ADBMMRMwEQYKCZImiZPyLGQBGRYDaHRiMRkwFwYKCZImiZPyLGQBGRYJY2VydGlm
| aWVkMRowGAYDVQQDExFjZXJ0aWZpZWQtREMwMS1DQTAgFw0yNTA2MTEyMTA1Mjla
| GA8yMTA1MDUyMzIxMDUyOVowADCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoC
| ggEBAKxmajneO9wN1G0eh2Ir/K3fG2mjvtJBduOYuM2muC4YiUO9nnknPzRXbOHN
| lNrfFlfMM8vF22qiOWNOAqZy0o6xXOxCzYIaRE2gL9DIfjjQuEXY2im5VgTo4VAI
| ntc4L6xoKOzxIn8XHjXe6zdGEc/X1fxXtwTsyCknT2eZJsc3YjyaefyjYAXpLjjE
| dnhRGaadShC9lY9UNBVsfCQ8c6JNY7f+XciCgp3cDy5J09/cnpCKhW0XlFnXKx0n
| d0VyNM0B1wvU2G6823wKUZKUNzYRWzkl3L/k4Id2CxpPTV7ExOEbnIsiBJU9rijg
| uByxDydofthnDyFAiDQ/qyez4CUCAwEAAaOCAykwggMlMDgGCSsGAQQBgjcVBwQr
| MCkGISsGAQQBgjcVCIfpnVqGp+FghYmdJ4HW1CmEvYtxgWwBIQIBbgIBAjAyBgNV
| HSUEKzApBggrBgEFBQcDAgYIKwYBBQUHAwEGCisGAQQBgjcUAgIGBysGAQUCAwUw
| DgYDVR0PAQH/BAQDAgWgMEAGCSsGAQQBgjcVCgQzMDEwCgYIKwYBBQUHAwIwCgYI
| KwYBBQUHAwEwDAYKKwYBBAGCNxQCAjAJBgcrBgEFAgMFMB0GA1UdDgQWBBR9WLee
| Ma0LzKnM8ZrvzMNE41aWhTAfBgNVHSMEGDAWgBTs+xJAFaG9x9EuOy5NS3LAYt8r
| 9TCBzgYDVR0fBIHGMIHDMIHAoIG9oIG6hoG3bGRhcDovLy9DTj1jZXJ0aWZpZWQt
| REMwMS1DQSxDTj1EQzAxLENOPUNEUCxDTj1QdWJsaWMlMjBLZXklMjBTZXJ2aWNl
| cyxDTj1TZXJ2aWNlcyxDTj1Db25maWd1cmF0aW9uLERDPWNlcnRpZmllZCxEQz1o
| dGI/Y2VydGlmaWNhdGVSZXZvY2F0aW9uTGlzdD9iYXNlP29iamVjdENsYXNzPWNS
| TERpc3RyaWJ1dGlvblBvaW50MIHFBggrBgEFBQcBAQSBuDCBtTCBsgYIKwYBBQUH
| MAKGgaVsZGFwOi8vL0NOPWNlcnRpZmllZC1EQzAxLUNBLENOPUFJQSxDTj1QdWJs
| aWMlMjBLZXklMjBTZXJ2aWNlcyxDTj1TZXJ2aWNlcyxDTj1Db25maWd1cmF0aW9u
| LERDPWNlcnRpZmllZCxEQz1odGI/Y0FDZXJ0aWZpY2F0ZT9iYXNlP29iamVjdENs
| YXNzPWNlcnRpZmljYXRpb25BdXRob3JpdHkwOgYDVR0RAQH/BDAwLoISREMwMS5j
| ZXJ0aWZpZWQuaHRigg1jZXJ0aWZpZWQuaHRigglDRVJUSUZJRUQwTgYJKwYBBAGC
| NxkCBEEwP6A9BgorBgEEAYI3GQIBoC8ELVMtMS01LTIxLTcyOTc0Njc3OC0yNjc1
| OTc4MDkxLTM4MjAzODgyNDQtMTAwMDANBgkqhkiG9w0BAQsFAAOCAQEAiUUJN4vt
| 459tCI43Rt0UQcaD1vWBs5AExrx2GxaZhj7r/mi7GCfFtVrlnDw70APgBb0Jzzq/
| LnF4q1yChWUxFvLeAyPbG+hLvk9OWvb2rmCK5S7RJIcwvJp2if8OP2WVuDvmdoyi
| xy+bc8JuIZtcACdlOIVsJlDU2NaPnepd1mV2lAOE8uUkB90ZvsCfYifAPwYuPVtH
| JpZihj6kismL/7rJ/8ZTsf2qbnttf1snzQvsdiNHFUMqxi7fY4mq+E1w+0BmFnLw
| GYiHqoY9bd5Ok+wz9YSJcJpKoHFnj5ObPz6JdFT/dlXAyZkmylijfMNbJ6x22hgI
| piE6bLwDeUY3DQ==
|_-----END CERTIFICATE-----
3269/tcp  open  ssl/ldap      syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: certified.htb0., Site: Default-First-Site-Name)
|_ssl-date: 2025-11-26T21:24:53+00:00; +7h00m00s from scanner time.
| ssl-cert: Subject: 
| Subject Alternative Name: DNS:DC01.certified.htb, DNS:certified.htb, DNS:CERTIFIED
| Issuer: commonName=certified-DC01-CA/domainComponent=certified
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-06-11T21:05:29
| Not valid after:  2105-05-23T21:05:29
| MD5:   ac8a:4187:4d19:237f:7cfa:de61:b5b2:941f
| SHA-1: 85f1:ada4:c000:4cd3:13de:d1c2:f3c6:58f7:7134:d397
| -----BEGIN CERTIFICATE-----
| MIIGBjCCBO6gAwIBAgITeQAAAASyK000VBwyGAAAAAAABDANBgkqhkiG9w0BAQsF
| ADBMMRMwEQYKCZImiZPyLGQBGRYDaHRiMRkwFwYKCZImiZPyLGQBGRYJY2VydGlm
| aWVkMRowGAYDVQQDExFjZXJ0aWZpZWQtREMwMS1DQTAgFw0yNTA2MTEyMTA1Mjla
| GA8yMTA1MDUyMzIxMDUyOVowADCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoC
| ggEBAKxmajneO9wN1G0eh2Ir/K3fG2mjvtJBduOYuM2muC4YiUO9nnknPzRXbOHN
| lNrfFlfMM8vF22qiOWNOAqZy0o6xXOxCzYIaRE2gL9DIfjjQuEXY2im5VgTo4VAI
| ntc4L6xoKOzxIn8XHjXe6zdGEc/X1fxXtwTsyCknT2eZJsc3YjyaefyjYAXpLjjE
| dnhRGaadShC9lY9UNBVsfCQ8c6JNY7f+XciCgp3cDy5J09/cnpCKhW0XlFnXKx0n
| d0VyNM0B1wvU2G6823wKUZKUNzYRWzkl3L/k4Id2CxpPTV7ExOEbnIsiBJU9rijg
| uByxDydofthnDyFAiDQ/qyez4CUCAwEAAaOCAykwggMlMDgGCSsGAQQBgjcVBwQr
| MCkGISsGAQQBgjcVCIfpnVqGp+FghYmdJ4HW1CmEvYtxgWwBIQIBbgIBAjAyBgNV
| HSUEKzApBggrBgEFBQcDAgYIKwYBBQUHAwEGCisGAQQBgjcUAgIGBysGAQUCAwUw
| DgYDVR0PAQH/BAQDAgWgMEAGCSsGAQQBgjcVCgQzMDEwCgYIKwYBBQUHAwIwCgYI
| KwYBBQUHAwEwDAYKKwYBBAGCNxQCAjAJBgcrBgEFAgMFMB0GA1UdDgQWBBR9WLee
| Ma0LzKnM8ZrvzMNE41aWhTAfBgNVHSMEGDAWgBTs+xJAFaG9x9EuOy5NS3LAYt8r
| 9TCBzgYDVR0fBIHGMIHDMIHAoIG9oIG6hoG3bGRhcDovLy9DTj1jZXJ0aWZpZWQt
| REMwMS1DQSxDTj1EQzAxLENOPUNEUCxDTj1QdWJsaWMlMjBLZXklMjBTZXJ2aWNl
| cyxDTj1TZXJ2aWNlcyxDTj1Db25maWd1cmF0aW9uLERDPWNlcnRpZmllZCxEQz1o
| dGI/Y2VydGlmaWNhdGVSZXZvY2F0aW9uTGlzdD9iYXNlP29iamVjdENsYXNzPWNS
| TERpc3RyaWJ1dGlvblBvaW50MIHFBggrBgEFBQcBAQSBuDCBtTCBsgYIKwYBBQUH
| MAKGgaVsZGFwOi8vL0NOPWNlcnRpZmllZC1EQzAxLUNBLENOPUFJQSxDTj1QdWJs
| aWMlMjBLZXklMjBTZXJ2aWNlcyxDTj1TZXJ2aWNlcyxDTj1Db25maWd1cmF0aW9u
| LERDPWNlcnRpZmllZCxEQz1odGI/Y0FDZXJ0aWZpY2F0ZT9iYXNlP29iamVjdENs
| YXNzPWNlcnRpZmljYXRpb25BdXRob3JpdHkwOgYDVR0RAQH/BDAwLoISREMwMS5j
| ZXJ0aWZpZWQuaHRigg1jZXJ0aWZpZWQuaHRigglDRVJUSUZJRUQwTgYJKwYBBAGC
| NxkCBEEwP6A9BgorBgEEAYI3GQIBoC8ELVMtMS01LTIxLTcyOTc0Njc3OC0yNjc1
| OTc4MDkxLTM4MjAzODgyNDQtMTAwMDANBgkqhkiG9w0BAQsFAAOCAQEAiUUJN4vt
| 459tCI43Rt0UQcaD1vWBs5AExrx2GxaZhj7r/mi7GCfFtVrlnDw70APgBb0Jzzq/
| LnF4q1yChWUxFvLeAyPbG+hLvk9OWvb2rmCK5S7RJIcwvJp2if8OP2WVuDvmdoyi
| xy+bc8JuIZtcACdlOIVsJlDU2NaPnepd1mV2lAOE8uUkB90ZvsCfYifAPwYuPVtH
| JpZihj6kismL/7rJ/8ZTsf2qbnttf1snzQvsdiNHFUMqxi7fY4mq+E1w+0BmFnLw
| GYiHqoY9bd5Ok+wz9YSJcJpKoHFnj5ObPz6JdFT/dlXAyZkmylijfMNbJ6x22hgI
| piE6bLwDeUY3DQ==
|_-----END CERTIFICATE-----
5985/tcp  open  http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp  open  mc-nmf        syn-ack ttl 127 .NET Message Framing
49667/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49693/tcp open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
49694/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49695/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49724/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49745/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49780/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running (JUST GUESSING): Microsoft Windows 2019|10 (97%)
OS CPE: cpe:/o:microsoft:windows_server_2019 cpe:/o:microsoft:windows_10
OS fingerprint not ideal because: Missing a closed TCP port so results incomplete
Aggressive OS guesses: Windows Server 2019 (97%), Microsoft Windows 10 1903 - 21H1 (91%)
No exact OS matches for host (test conditions non-ideal).
TCP/IP fingerprint:
SCAN(V=7.95%E=4%D=11/26%OT=53%CT=%CU=%PV=Y%DS=2%DC=T%G=N%TM=69270DB6%P=aarch64-unknown-linux-gnu)
SEQ(SP=105%GCD=1%ISR=10B%TI=I%II=I%SS=S%TS=U)
SEQ(SP=105%GCD=1%ISR=10C%TI=I%TS=U)
OPS(O1=M542NW8NNS%O2=M542NW8NNS%O3=M542NW8%O4=M542NW8NNS%O5=M542NW8NNS%O6=M542NNS)
WIN(W1=FFFF%W2=FFFF%W3=FFFF%W4=FFFF%W5=FFFF%W6=FF70)
ECN(R=Y%DF=Y%TG=80%W=FFFF%O=M542NW8NNS%CC=Y%Q=)
T1(R=Y%DF=Y%TG=80%S=O%A=S+%F=AS%RD=0%Q=)
T2(R=N)
T3(R=N)
T4(R=N)
U1(R=N)
IE(R=Y%DFI=N%TG=80%CD=Z)

Network Distance: 2 hops
TCP Sequence Prediction: Difficulty=261 (Good luck!)
IP ID Sequence Generation: Incremental
Service Info: Host: DC01; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 47015/tcp): CLEAN (Timeout)
|   Check 2 (port 30204/tcp): CLEAN (Timeout)
|   Check 3 (port 24870/udp): CLEAN (Timeout)
|   Check 4 (port 38141/udp): CLEAN (Timeout)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked
|_clock-skew: mean: 6h59m59s, deviation: 0s, median: 6h59m59s
| smb2-time: 
|   date: 2025-11-26T21:24:14
|_  start_date: N/A

TRACEROUTE (using port 445/tcp)
HOP RTT       ADDRESS
1   178.63 ms 10.10.16.1
2   268.53 ms 10.129.231.186

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 14:24
Completed NSE at 14:24, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 14:24
Completed NSE at 14:24, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 14:24
Completed NSE at 14:24, 0.00s elapsed
Read data files from: /usr/share/nmap
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 113.59 seconds
           Raw packets sent: 105 (8.462KB) | Rcvd: 53 (3.050KB)

```

## whatweb
```bash

```

## Feroxbuster
```bash

```

## Dirsearch
```bash

```

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

