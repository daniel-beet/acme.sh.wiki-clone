DNS manual mode can not renew automatically.  It's just for testing purpose.

Please use dns api mode or [dns alias mode](https://github.com/Neilpang/acme.sh/wiki/DNS-alias-mode) instead.




```sh

acme.sh --issue -d example.com  -dns   \
   --yes-I-know-dns-manual-mode-enough-go-ahead-please

acme.sh --renew -d example.com   \
   --yes-I-know-dns-manual-mode-enough-go-ahead-please

```