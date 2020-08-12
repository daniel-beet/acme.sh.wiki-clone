For the `--server` parameter, you can specify an ACME server directory URL,  and you can also give a short friendly name for known CAs.

The supported short names are:

|    Short Name      |       ACME server URL |
|--------------------|    -------------------|
| letsencrypt       |  https://acme-v02.api.letsencrypt.org/directory |
| letsencrypt_test   | https://acme-staging-v02.api.letsencrypt.org/directory |
| buypass  | https://api.buypass.com/acme/directory  | 
| buypass_test |  https://api.test4.buypass.no/acme/directory |
| zerossl | https://acme.zerossl.com/v2/DV90  |


The short name will be treated as the same as the URL:

The following usages have the same meaning:

```
acme.sh --issue ....   --server  zerossl

-or-

acme.sh --issue ....   --server  https://acme.zerossl.com/v2/DV90
```



