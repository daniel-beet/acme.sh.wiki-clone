## lexicon is a python tool for a number of dns providers.

As I'm writing this doc, it supports the following dns api:
The current supported providers are:

* Cloudflare
* DigitalOcean
* DNSimple
* DnsMadeEasy
* DNSPark
* EasyDNS
* Namesilo
* NS1
* PointHQ
* Rage4
* TransIP
* Vultr


You can check its project page: https://github.com/AnalogJ/lexicon

You must install `python` and `lexicon` before using it.

## Examples:
### 1. Using lexicon cloudflare api:
```
export PROVIDER=cloudflare
export LEXICON_CLOUDFLARE_USERNAME="xxxx@xxx.com"
export LEXICON_CLOUDFLARE_TOKEN="XXXXXXXXXXXXXXX"

acme.sh --issue  -d test.acme.sh  --dns  dns_lexicon
```

### 2. Using lexicon namesilo api:

[Namesilo](https://www.namesilo.com/) applies any submitted changes to DNS records **every 15 minutes**.  To make sure verification aligns with propagation, `--dnssleep` must be set for **16 minutes (960 seconds)**.  You may generate a new API key (namesilo-api-token) at the [api manager](https://www.namesilo.com/account_api.php) under *Account Options* after logging in.

```
export PROVIDER=namesilo
export LEXICON_NAMESILO_TOKEN="namesilo-api-token"

acme.sh --issue  -d test.acme.sh  --dns dns_lexicon  --dnssleep 960
```

### 3. Using lexicon TransIP api:
```
export PROVIDER=TRANSIP
export LEXICON_TRANSIP_USERNAME="username"
export LEXICON_TRANSIP_API_KEY="/path/to/file.key"

acme.sh --issue  -d test.acme.sh  --dns  dns_lexicon
```

For more examples, please check lexicon page: https://github.com/AnalogJ/lexicon



