DNS manual mode can not renew automatically.  It's just for testing purpose.

Please use dns api mode or [dns alias mode](https://github.com/Neilpang/acme.sh/wiki/DNS-alias-mode) instead.



DNS manual mode: 
https://github.com/Neilpang/acme.sh/issues/1029


1. First step:
```sh

acme.sh --issue -d example.com --dns \ --yes-I-know-dns-manual-mode-enough-go-ahead-please

```

2. Please add the txt record to your dns records.


3. Now retry with `--renew` command.

```sh

acme.sh --renew -d example.com \ --yes-I-know-dns-manual-mode-enough-go-ahead-please

```