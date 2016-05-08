## lexicon is a python tool for a number dns providers.

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
* Vultr


You can check its project page: https://github.com/AnalogJ/lexicon

You must install `python` and `lexicon` before using it.

## Examples:
### 1. Using lexicon cloudflare api:
```
export LEXICON_CLOUDFLARE_USERNAME="xxxx@xxx.com"
export LEXICON_CLOUDFLARE_TOKEN="XXXXXXXXXXXXXXX"

acme.sh --issue  -d test.acme.sh  --dns  dns_lexicon
```


For more examples, please check lexicon page: https://github.com/AnalogJ/lexicon



