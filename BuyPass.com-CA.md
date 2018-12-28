See https://github.com/Neilpang/acme.sh/pull/1989

Thanks to  www.buypass.com

https://api.buypass.com/acme/directory
https://www.buypass.com/ssl/resources/go-ssl-technical-specification


Usage:

```sh
acme.sh --server https://api.buypass.com/acme/directory  \
         --issue -d example.com -d www.example.com .....
```


1. It only supports ACME v1,  no wildcard cert.
2. It has 180 days lifetime.

