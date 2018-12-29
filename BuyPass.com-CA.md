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

