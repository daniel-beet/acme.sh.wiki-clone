In dns mode, after the dns record is added, acme.sh will use cloudflare public dns or google dns to check if the record has taken effect.

If you don't want this check,  please use `--dnssleep 300`.

```
acme.sh --issue -d xxxxx  --dns dns_xxx     --dnssleep 300

```


Then acme.sh will wait for `300` seconds instead of checking through the public dns.

