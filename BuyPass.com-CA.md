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

1. It only supports ACME v1,  no wildcard cert. but you can add as many domains to the cert as you want.
2. It has 180 days lifetime.
3. ECC cert is supported, but the signing root is a RSA root.



Example:

```
-----BEGIN CERTIFICATE-----
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
-----END CERTIFICATE-----
```