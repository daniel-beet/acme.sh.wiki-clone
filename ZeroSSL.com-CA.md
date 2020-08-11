Using ZeroSSL.com CA  

https://zerossl.com/letsencrypt-alternative/#acme


1. Get your External Account Binding(EAB) credentials at www.zerossl.com

    Register an acccount at www.zerossl.com, and generate you EAB credentials.

2. Register acme account:

```
acme.sh  --register-account  --server zerossl \
        --eab-kid  xxxxxxxxxxxx  \
        --eab-hmac-key  xxxxxxxxx
```

3. Done.  You can use Zerossl.com with `--server zerossl` as normal now.

```
acme.sh --server zerossl  \
     --issue  -d  example.com \
     --dns dns_cf
```

4. If you don't want to specify `--server zerossl` every time you issue a cert, you can set `zerossl` as the default CA:

```
acme.sh --set-default-ca  --server zerossl
```
Then you can issue any cert from zerossl:

```
acme.sh --issue -d  example.com --dns dns_cf
```



More details: https://github.com/acmesh-official/acme.sh/wiki/How-to-issue-a-cert

