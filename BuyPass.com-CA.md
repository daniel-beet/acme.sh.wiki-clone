See https://github.com/Neilpang/acme.sh/pull/1989

Thanks to  www.buypass.com

https://api.buypass.com/acme/directory

https://www.buypass.com/ssl/resources/go-ssl-technical-specification
https://community.buypass.com/t/63d4ay/buypass-go-ssl-endpoints

Usage:

First time register account with an email, the mail is required by buypass.com

```sh
acme.sh --server https://api.buypass.com/acme/directory \
        --register-account  --accountemail me@example.com
```


Then you can issue cert now.

```sh
acme.sh --server https://api.buypass.com/acme/directory  \
         --issue -d example.com -d www.example.com ..... \
         --days 170
```

Since buypass cert has 180 days lifetime, so we specify `--days 170` for acme.sh to renew the cert at the 170 th day.
If you don't specify days, it will renew at 60 days by default.


Once issued,  you can renew the cert without `--server` parameter.

```sh
acme.sh --renew -d example.com
```

Don't worry, all the certs will be automatically renewed as usual. 




----------------------

1. It only supports ACME v1,  no wildcard cert. You can only add `example.com` and `www.example.com`, or `sub.example.com` and `www.sub.example.com`.
2. It has 180 days lifetime.
3. ECC cert is supported, but the signing root is a RSA root.



Example:

```
-----BEGIN CERTIFICATE-----
MIIGbDCCBFSgAwIBAgIKCRFsFobOdP0VfTANBgkqhkiG9w0BAQsFADBLMQswCQYD
VQQGEwJOTzEdMBsGA1UECgwUQnV5cGFzcyBBUy05ODMxNjMzMjcxHTAbBgNVBAMM
FEJ1eXBhc3MgQ2xhc3MgMiBDQSA1MB4XDTE4MTIyOTAxMzIyNVoXDTE5MDYyNzIx
NTkwMFowGjEYMBYGA1UEAwwPYnV5cGFzcy5hY21lLnNoMIIBIjANBgkqhkiG9w0B
AQEFAAOCAQ8AMIIBCgKCAQEA0H5zERFpOG5en3JRnIZ6LXxDohtID0IbsdK2vjla
ARwuClpJzfcRd0iWZtsV0sUy4zlNv0CZEvwxM1egpL1cEpfrDzMOjpy7OmSyBiEV
Y6KxhBr/UNdYWZNaoWEQyHTaaYsEyoMTIL8Wt7uqnoxrVHrY4goYiJTqk8Xk8Skj
3QSLSb/jkLFfCl7gdAbgylXKhq8id5gp7VUnCUB5cp7wq1n6GCbn2k4HRzKLNXT2
4AZm/a+nNbhA6hBRm79hl2lvtefYM7wB+LbDODLT3AxabT3fjHPprAtcZ296H+gG
+w11urMCOtgADU9jgLikXkQNPQ1ZffbANs5+lN5MAwyNYwIDAQABo4ICgTCCAn0w
CQYDVR0TBAIwADAfBgNVHSMEGDAWgBQnUqRvLSqrQJOQ7NZpy/58YTt8QjAdBgNV
HQ4EFgQUYtkEq1qyZWbHkD5GhIhHxqALb+8wDgYDVR0PAQH/BAQDAgWgMB0GA1Ud
JQQWMBQGCCsGAQUFBwMBBggrBgEFBQcDAjAfBgNVHSAEGDAWMAoGCGCEQgEaAQIH
MAgGBmeBDAECATA6BgNVHR8EMzAxMC+gLaArhilodHRwOi8vY3JsLmJ1eXBhc3Mu
bm8vY3JsL0JQQ2xhc3MyQ0E1LmNybDAvBgNVHREEKDAmgg9idXlwYXNzLmFjbWUu
c2iCE3d3dy5idXlwYXNzLmFjbWUuc2gwagYIKwYBBQUHAQEEXjBcMCMGCCsGAQUF
BzABhhdodHRwOi8vb2NzcC5idXlwYXNzLmNvbTA1BggrBgEFBQcwAoYpaHR0cDov
L2NydC5idXlwYXNzLm5vL2NydC9CUENsYXNzMkNBNS5jZXIwggEFBgorBgEEAdZ5
AgQCBIH2BIHzAPEAdgC72d+8H4pxtZOUI5eqkntHOFeVCqtS6BqQlmQ2jh7RhQAA
AWf3l0qaAAAEAwBHMEUCICYVuYUQuwAqgWv0dlM/p6+nbHOkrod/t+US2Qua1Ybw
AiEA8YS0Nh/N7opKZF4p8g3vaICQG2g/Y8uqKUuTA3Dz1IIAdwBVgdTCFpA2AUrq
C5tXPFPwwOQ4eHAlCBcvo6odBxPTDAAAAWf3l0vpAAAEAwBIMEYCIQDyRw4nBKTF
qIVL8NX7vROojrXu4wpeRjx2PGsPKFl4fwIhAJyE9KedaBY7QP/YQp1JKm9gXw64
2KSv3vizaM1xSQ2DMA0GCSqGSIb3DQEBCwUAA4ICAQCMUUgNvRZrUBF0yZD1MKvd
aJ7UuGqisfuJroDhFHNFWqhahIPr3kFOA1YWTZdg/8xlnkfwZ5Q4qoC9U4y08Iy7
+nyzSHohDg1uH16JiuLyh1I/aq34wDWGEzRAKHL3FGRQYE8+xk3xT5lzVjZpuc07
sxjez02Mbd5UbrflRXjT+l0KAC2kUrZ1CI8CjHdmA74RL3GRIL/hZaWBDTp64xr/
QKJqXUKZdUIY2CGUTDKKF/RVdLqHle3wF2u0Hog/10HwlTf/KRmohPdD4XPGrU1Y
PKSfP5HGIhAPOSAbpyeR0wN0GAR84VNF2PVgttqtJCAqVLfs8dscMDce4Yujrg4a
i+aTB5pPb0fm+wa+z6kwFtu8Aauag9jN7bAw13dq6VpiLxVCPBa1mmMgjJCj5Abl
rcV5itofkhz1E/ZVTH+k2KzBICqE2DZkxLVGEuhY9k6RuicY6FWZ83eIZKx3Dmci
2FBnNob0b1jvC79lo3eyDB6G8+dFs29l8RvrNLdxiZlidLJdjbcEQd+OZ3ZWlVS7
cwQ3G6VZh1+8limcZechXCa/aonf2JyadWfCxZRZRJsy1NMLT7yd3w6haubgZ0f2
a96zwu5nXXQ7xbawEajLy6Qll9yejmKSVUVoyTH1AoZoaA/NOnjnQXveeiktArlg
86H+f+G4O5uuktiHIeAYNg==
-----END CERTIFICATE-----

```


```

Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number:
            09:11:6c:16:86:ce:74:fd:15:7d
    Signature Algorithm: sha256WithRSAEncryption
        Issuer: C = NO, O = Buypass AS-983163327, CN = Buypass Class 2 CA 5
        Validity
            Not Before: Dec 29 01:32:25 2018 GMT
            Not After : Jun 27 21:59:00 2019 GMT
        Subject: CN = buypass.acme.sh
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                Public-Key: (2048 bit)
                Modulus:
                    00:d0:7e:73:11:11:69:38:6e:5e:9f:72:51:9c:86:
                    7a:2d:7c:43:a2:1b:48:0f:42:1b:b1:d2:b6:be:39:
                    5a:01:1c:2e:0a:5a:49:cd:f7:11:77:48:96:66:db:
                    15:d2:c5:32:e3:39:4d:bf:40:99:12:fc:31:33:57:
                    a0:a4:bd:5c:12:97:eb:0f:33:0e:8e:9c:bb:3a:64:
                    b2:06:21:15:63:a2:b1:84:1a:ff:50:d7:58:59:93:
                    5a:a1:61:10:c8:74:da:69:8b:04:ca:83:13:20:bf:
                    16:b7:bb:aa:9e:8c:6b:54:7a:d8:e2:0a:18:88:94:
                    ea:93:c5:e4:f1:29:23:dd:04:8b:49:bf:e3:90:b1:
                    5f:0a:5e:e0:74:06:e0:ca:55:ca:86:af:22:77:98:
                    29:ed:55:27:09:40:79:72:9e:f0:ab:59:fa:18:26:
                    e7:da:4e:07:47:32:8b:35:74:f6:e0:06:66:fd:af:
                    a7:35:b8:40:ea:10:51:9b:bf:61:97:69:6f:b5:e7:
                    d8:33:bc:01:f8:b6:c3:38:32:d3:dc:0c:5a:6d:3d:
                    df:8c:73:e9:ac:0b:5c:67:6f:7a:1f:e8:06:fb:0d:
                    75:ba:b3:02:3a:d8:00:0d:4f:63:80:b8:a4:5e:44:
                    0d:3d:0d:59:7d:f6:c0:36:ce:7e:94:de:4c:03:0c:
                    8d:63
                Exponent: 65537 (0x10001)
        X509v3 extensions:
            X509v3 Basic Constraints: 
                CA:FALSE
            X509v3 Authority Key Identifier: 
                keyid:27:52:A4:6F:2D:2A:AB:40:93:90:EC:D6:69:CB:FE:7C:61:3B:7C:42

            X509v3 Subject Key Identifier: 
                62:D9:04:AB:5A:B2:65:66:C7:90:3E:46:84:88:47:C6:A0:0B:6F:EF
            X509v3 Key Usage: critical
                Digital Signature, Key Encipherment
            X509v3 Extended Key Usage: 
                TLS Web Server Authentication, TLS Web Client Authentication
            X509v3 Certificate Policies: 
                Policy: 2.16.578.1.26.1.2.7
                Policy: 2.23.140.1.2.1

            X509v3 CRL Distribution Points: 

                Full Name:
                  URI:http://crl.buypass.no/crl/BPClass2CA5.crl

            X509v3 Subject Alternative Name: 
                DNS:buypass.acme.sh, DNS:www.buypass.acme.sh
            Authority Information Access: 
                OCSP - URI:http://ocsp.buypass.com
                CA Issuers - URI:http://crt.buypass.no/crt/BPClass2CA5.cer

            CT Precertificate SCTs: 
                Signed Certificate Timestamp:
                    Version   : v1 (0x0)
                    Log ID    : BB:D9:DF:BC:1F:8A:71:B5:93:94:23:97:AA:92:7B:47:
                                38:57:95:0A:AB:52:E8:1A:90:96:64:36:8E:1E:D1:85
                    Timestamp : Dec 29 01:32:26.650 2018 GMT
                    Extensions: none
                    Signature : ecdsa-with-SHA256
                                30:45:02:20:26:15:B9:85:10:BB:00:2A:81:6B:F4:76:
                                53:3F:A7:AF:A7:6C:73:A4:AE:87:7F:B7:E5:12:D9:0B:
                                9A:D5:86:F0:02:21:00:F1:84:B4:36:1F:CD:EE:8A:4A:
                                64:5E:29:F2:0D:EF:68:80:90:1B:68:3F:63:CB:AA:29:
                                4B:93:03:70:F3:D4:82
                Signed Certificate Timestamp:
                    Version   : v1 (0x0)
                    Log ID    : 55:81:D4:C2:16:90:36:01:4A:EA:0B:9B:57:3C:53:F0:
                                C0:E4:38:78:70:25:08:17:2F:A3:AA:1D:07:13:D3:0C
                    Timestamp : Dec 29 01:32:26.985 2018 GMT
                    Extensions: none
                    Signature : ecdsa-with-SHA256
                                30:46:02:21:00:F2:47:0E:27:04:A4:C5:A8:85:4B:F0:
                                D5:FB:BD:13:A8:8E:B5:EE:E3:0A:5E:46:3C:76:3C:6B:
                                0F:28:59:78:7F:02:21:00:9C:84:F4:A7:9D:68:16:3B:
                                40:FF:D8:42:9D:49:2A:6F:60:5F:0E:B8:D8:A4:AF:DE:
                                F8:B3:68:CD:71:49:0D:83
    Signature Algorithm: sha256WithRSAEncryption
         8c:51:48:0d:bd:16:6b:50:11:74:c9:90:f5:30:ab:dd:68:9e:
         d4:b8:6a:a2:b1:fb:89:ae:80:e1:14:73:45:5a:a8:5a:84:83:
         eb:de:41:4e:03:56:16:4d:97:60:ff:cc:65:9e:47:f0:67:94:
         38:aa:80:bd:53:8c:b4:f0:8c:bb:fa:7c:b3:48:7a:21:0e:0d:
         6e:1f:5e:89:8a:e2:f2:87:52:3f:6a:ad:f8:c0:35:86:13:34:
         40:28:72:f7:14:64:50:60:4f:3e:c6:4d:f1:4f:99:73:56:36:
         69:b9:cd:3b:b3:18:de:cf:4d:8c:6d:de:54:6e:b7:e5:45:78:
         d3:fa:5d:0a:00:2d:a4:52:b6:75:08:8f:02:8c:77:66:03:be:
         11:2f:71:91:20:bf:e1:65:a5:81:0d:3a:7a:e3:1a:ff:40:a2:
         6a:5d:42:99:75:42:18:d8:21:94:4c:32:8a:17:f4:55:74:ba:
         87:95:ed:f0:17:6b:b4:1e:88:3f:d7:41:f0:95:37:ff:29:19:
         a8:84:f7:43:e1:73:c6:ad:4d:58:3c:a4:9f:3f:91:c6:22:10:
         0f:39:20:1b:a7:27:91:d3:03:74:18:04:7c:e1:53:45:d8:f5:
         60:b6:da:ad:24:20:2a:54:b7:ec:f1:db:1c:30:37:1e:e1:8b:
         a3:ae:0e:1a:8b:e6:93:07:9a:4f:6f:47:e6:fb:06:be:cf:a9:
         30:16:db:bc:01:ab:9a:83:d8:cd:ed:b0:30:d7:77:6a:e9:5a:
         62:2f:15:42:3c:16:b5:9a:63:20:8c:90:a3:e4:06:e5:ad:c5:
         79:8a:da:1f:92:1c:f5:13:f6:55:4c:7f:a4:d8:ac:c1:20:2a:
         84:d8:36:64:c4:b5:46:12:e8:58:f6:4e:91:ba:27:18:e8:55:
         99:f3:77:88:64:ac:77:0e:67:22:d8:50:67:36:86:f4:6f:58:
         ef:0b:bf:65:a3:77:b2:0c:1e:86:f3:e7:45:b3:6f:65:f1:1b:
         eb:34:b7:71:89:99:62:74:b2:5d:8d:b7:04:41:df:8e:67:76:
         56:95:54:bb:73:04:37:1b:a5:59:87:5f:bc:96:29:9c:65:e7:
         21:5c:26:bf:6a:89:df:d8:9c:9a:75:67:c2:c5:94:59:44:9b:
         32:d4:d3:0b:4f:bc:9d:df:0e:a1:6a:e6:e0:67:47:f6:6b:de:
         b3:c2:ee:67:5d:74:3b:c5:b6:b0:11:a8:cb:cb:a4:25:97:dc:
         9e:8e:62:92:55:45:68:c9:31:f5:02:86:68:68:0f:cd:3a:78:
         e7:41:7b:de:7a:29:2d:02:b9:60:f3:a1:fe:7f:e1:b8:3b:9b:
         ae:92:d8:87:21:e0:18:36

```