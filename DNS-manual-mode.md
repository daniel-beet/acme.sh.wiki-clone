Warning: DNS manual mode can not renew automatically.

If your domain provider does **not** offer an API where you can add/edit TXT records of your domain, you should use [DNS alias mode](https://github.com/Neilpang/acme.sh/wiki/DNS-alias-mode). 

If your domain providor **does** offer an DNS API, you should use DNS api mode.


DNS manual mode: 
https://github.com/Neilpang/acme.sh/issues/1029


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