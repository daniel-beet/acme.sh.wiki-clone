Using ZeroSSL.com CA  

ZeroSSL doesn't have stagging and no production rate limit, you can issue unlimited SSL of 90 Days.

Alternatively you can create Account and grab your acme creditentals faster on ZeroSSL.com and even manage issued SSL. 


https://zerossl.com/letsencrypt-alternative/#acme

### 1.  Register your account with an email address:

```
acme.sh  --register-account  -m myemail@example.com --server zerossl
```

If you already have an account at www.zerossl.com, you can register your acme account with External Account Binding(EAB) credentials.

1. Generate your EAB credentials at: https://app.zerossl.com/developer
2. Register acme account:

```
acme.sh  --register-account  --server zerossl \
        --eab-kid  xxxxxxxxxxxx  \
        --eab-hmac-key  xxxxxxxxx
```

### 2. Done.  You can use Zerossl.com with `--server zerossl` as normal now.

```
acme.sh --server zerossl  \
     --issue  -d  example.com \
     --dns dns_cf
```

### 3. If you don't want to specify `--server zerossl` every time you issue a cert, you can set `zerossl` as the default CA:

```
acme.sh --set-default-ca  --server zerossl
```

See: https://github.com/acmesh-official/acme.sh/wiki/Server

Then you can issue any cert from zerossl:

```
acme.sh --issue -d  example.com --dns dns_cf
```


More details: 

https://github.com/acmesh-official/acme.sh/wiki/How-to-issue-a-cert

