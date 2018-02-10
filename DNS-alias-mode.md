Set domain CNAME:

```sh
_acme-challenge.example.com  =>   _acme-challenge.exampleAlias.com
```


```sh
acme.sh --issue  \
  -d  example.com --challenge-alias exampleAlias.com --dns dns_cf
```