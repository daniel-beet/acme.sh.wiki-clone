Using ZeroSSL.com CA


1. Get your External Account Binding(EAB) credentials at www.zerossl.com

2. Register acme account:

```
acme.sh  --register-account  --server https://acme.zerossl.com/v2/DV90 \
        --eab-kid  xxxxxxxxxxxx  \
        --eab-hmac-key  xxxxxxxxx
```

3. Done.  You can use Zerossl.com with `--server https://acme.zerossl.com/v2/DV90` as normal now.

```
acme.sh --server https://acme.zerossl.com/v2/DV90  \
     --issue  -d  example.com \
     --dns dns_cf
```

More details: https://github.com/acmesh-official/acme.sh/wiki/How-to-issue-a-cert

