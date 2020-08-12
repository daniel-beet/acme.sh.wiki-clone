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


For now, the default CA is `letsencrypt`.   If you want to use another CA, you need to specify `--server` for each command.

For example, if your want to use `zerossl` CA :

```
acme.sh  --register-account  --server zerossl  -m  myemail@example.com

--or--

acme.sh  --issue --server zerossl  -d  example.com  --dns dns_cf

```

There is a way to change the default CA:

```
acme.sh  --set-default-ca  zerossl
```

From now on,  you will issue cert from `zerossl` without `--server` parameters.

```
acme.sh --issue  -d example.com  --dns dns_cf
```



