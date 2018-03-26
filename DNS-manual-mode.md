Warning: DNS manual mode can not renew automatically.

If your domain providor offers an DNS API, it's highly recommended to use DNS API mode instead. With the DNS API mode, you can automate the renewals. 


If your domain provider does **not** offer an API where you can add/edit TXT records of your domain, it is recommended to use [DNS alias mode](https://github.com/Neilpang/acme.sh/wiki/DNS-alias-mode) instead. 


DNS manual mode **should** be used for testing. If you do use it for your production server, remember to renew your certificate within 90 days. [Please, make sure you understand DNS manual mode](https://github.com/Neilpang/acme.sh/issues/1029).




1. First step:
```sh

acme.sh --issue -d example.com --dns \
 --yes-I-know-dns-manual-mode-enough-go-ahead-please

```

2. Please add the TXT record to your DNS records. This step is required every time you renew your certificate. With DNS api mode, this step can be automated.


3. Now retry with `--renew` command.

```sh

acme.sh --renew -d example.com \
  --yes-I-know-dns-manual-mode-enough-go-ahead-please

```


Known issues:
[The supported validation types are: dns-01 , but you specified: http-01](https://github.com/Neilpang/acme.sh/issues/1433)