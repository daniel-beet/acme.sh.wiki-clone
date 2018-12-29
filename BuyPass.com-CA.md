See https://github.com/Neilpang/acme.sh/pull/1989

Thanks to  www.buypass.com

https://api.buypass.com/acme/directory

https://www.buypass.com/ssl/resources/go-ssl-technical-specification


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