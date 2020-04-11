If your DNS provider doesn't support API access, or if you're concerned about security problems from giving the DNS API access to your main domain, then you can use DNS alias mode.

For example, your main domain is **example.com**, which doesn't have API access, or you don't want to give the API access to acme.sh, since it's important.

And you have another domain: **aliasDomainForValidationOnly.com**, which has a supported DNS API. This domain is less important, and maybe it's used for validation only.

Ok, let's start.

### 1. First set domain CNAME:

```text
_acme-challenge.example.com
   =>   _acme-challenge.aliasDomainForValidationOnly.com
```

or, in standard [DNS zone file](https://en.wikipedia.org/wiki/Zone_file) format, (like ISC BIND or NSD):

```text.zone_file
_acme-challenge.example.com	IN	CNAME	_acme-challenge.aliasDomainForValidationOnly.com.
```


### 2. Issue a cert:

```sh
acme.sh --issue  \
  -d  example.com --challenge-alias aliasDomainForValidationOnly.com --dns dns_cf
```

The Letsencrypt CA server checks the txt record of original domain `_acme-challenge.example.com` to validate your domain, but you have set the CNAME in step 1, so it goes forward to the aliased domain `_acme-challenge.aliasDomainForValidationOnly.com` to check.

And acme.sh knows that, so it just added the correct txt record to `_acme-challenge.aliasDomainForValidationOnly.com`.

So, it's done. you will get a cert for `example.com`, but you don't need to give the domain control out.



### 3. Share the same aliased domain:

If you have multiple (sub)domains, you need add CNAME for each (sub)domain, but they can share the same aliased domain.
For example, you can add the CNAME like:

```sh
_acme-challenge.example.com
  =>   _acme-challenge.aliasDomainForValidationOnly.com

_acme-challenge.www.example.com
  =>   _acme-challenge.aliasDomainForValidationOnly.com

_acme-challenge.sub.example.com
  =>   _acme-challenge.aliasDomainForValidationOnly.com

_acme-challenge.example.net
  =>   _acme-challenge.aliasDomainForValidationOnly.com

_acme-challenge.example.org
  =>   _acme-challenge.aliasDomainForValidationOnly.com
```

And then issue cert like bellow:

```sh

acme.sh --issue  \
  -d  example.com --challenge-alias aliasDomainForValidationOnly.com --dns dns_cf \
  -d  www.example.com \
  -d  sub.example.com \
  -d  example.net \
  -d  example.org
```

Even with ACME v2 wildcard cert:

```sh

acme.sh --issue  \
  -d  example.com --challenge-alias aliasDomainForValidationOnly.com --dns dns_cf \
  -d  www.example.com \
  -d  sub.example.com \
  -d  example.net \
  -d  example.org \
  -d  *.example.com \
  -d  *.example.net \
  -d  *.example.org
```

### 4. Specify different aliased domains for each domain.

Yes, you know, acme.sh support to set alias domain for each domain. Even with different dns provider:

You can set CNAME like:

```sh
_acme-challenge.example.com
  =>   _acme-challenge.aliasDomainForValidationOnly.com

_acme-challenge.example.net
  => _acme-challenge.aliasDomainForValidationOnly2.com
```

Then issue cert:

```sh

acme.sh --issue  \
  -d  example.com --challenge-alias aliasDomainForValidationOnly.com --dns dns_cf \
  -d  example.net --challenge-alias aliasDomainForValidationOnly2.com
```

Even with different dns provider:

```sh
acme.sh --issue  \
  -d  example.com --challenge-alias aliasDomainForValidationOnly.com --dns dns_cf \
  -d  example.net --challenge-alias aliasDomainForValidationOnly2.com  --dns dns_gd
```

Let's assume the first domain `aliasDomainForValidationOnly.com` is hosted at cloudflare, and the second is hosted at godaddy.


### 5. Last

Do not remove the CNAME like : `_acme-challenge.example.com` after you issue the cert. It will be reused when acme.sh tries to renew the cert. The left cname record `_acme-challenge.example.com` doesn't harm your domain at all. Just keep it there.


### 6. challenge-alias or domain-alias

We have another parameter: `--domain-alias`, it has the same meaning with `--challenge-alias`.

But with `--domain-alias` you don't need to add `_acme-challenge.` prefix.

For example, if you use `--challenge-alias`, you must set CNAME like bellow:

```sh
CNAME:
_acme-challenge.A.com
   =>   _acme-challenge.B.com
```
Then issue cert like:

```sh
acme.sh --issue -d  a.com  --challenge-alias  b.com  --dns dns_cf
```

If you use `--domain-alias`, the CNAME should be like:

```sh
CNAME:
_acme-challenge.A.com
   =>    myalias.B.com

```

Then issue cert like:

```sh
acme.sh --issue -d  a.com  --domain-alias  myalias.B.com   --dns dns_cf
```

**Note:** Don't use the domain name only for --domain-alias.
```sh
acme.sh --issue -d  a.com  --domain-alias B.com   --dns dns_cf
```
This would require that a TXT record is created at the domain apex i.e. @ TXT "myvalidationcode". Since adding a value at the apex of a domain requires a different syntax for adding the DNS records it cannot be used in this form.

If you really want to create the validation records at the domain apex then depending on the implementation of the dns api you have to use

```sh
acme.sh --issue -d  a.com  --domain-alias @.B.com   --dns dns_cf
```
or
```sh
acme.sh --issue -d  a.com  --domain-alias .B.com   --dns dns_cf
```