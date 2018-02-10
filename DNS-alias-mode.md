If your dns provider doesn't support api access,  or if you mind the secure to give the dns api access of your main domain to a shell script.  You can use dns alias mode.

For example,  your main domain is `importantDomain.com`,  which doesn't have api access, or you don't want to give the api access to acme.sh, since it's important.

And you have another domain:  "aliasDomainForValidationOnly.com", which has a supported dns api.  This domain is less important, and maybe it's used for validation only.

Ok, let's start.

### 1. First set domain CNAME:

```sh
_acme-challenge.importantDomain.com  
   =>   _acme-challenge.aliasDomainForValidationOnly.com
```


### 2. Issue a cert:

```sh
acme.sh --issue  \
  -d  importantDomain.com --challenge-alias aliasDomainForValidationOnly.com --dns dns_cf
```

The Letsencrypt CA server checks the txt record of original domain `_acme-challenge.importantDomain.com` to validate your domain,  but you have set the CNAME in step 1,  so it goes forward to the aliased domain `_acme-challenge.aliasDomainForValidationOnly.com` to check.

And acme.sh knows that, so it just added the correct txt record to `_acme-challenge.aliasDomainForValidationOnly.com`.

So, it's done.  you will get a cert for `importantDomain.com`, but you don't need to give the domain control out.



### 3. Share the same aliased domain:

If you have multiple (sub)domains, you need add CNAME for each (sub)domain,  but they can share the same aliased domain.
For example, you can add the CNAME like:

```sh
_acme-challenge.importantDomain.com  
  =>   _acme-challenge.aliasDomainForValidationOnly.com

_acme-challenge.www.importantDomain.com  
  =>   _acme-challenge.aliasDomainForValidationOnly.com

_acme-challenge.sub.importantDomain.com  
  =>   _acme-challenge.aliasDomainForValidationOnly.com

_acme-challenge.importantDomain2.com  
  =>   _acme-challenge.aliasDomainForValidationOnly.com

_acme-challenge.importantDomain3.com  
  =>   _acme-challenge.aliasDomainForValidationOnly.com
```

And then issue cert like bellow:

```sh

acme.sh --issue  \
  -d  importantDomain.com --challenge-alias aliasDomainForValidationOnly.com --dns dns_cf
  -d  www.importantDomain.com \
  -d  sub.importantDomain.com \
  -d  importantDomain2.com \
  -d  importantDomain3.com
```

Even with ACME v2 wildcard cert:

```sh

acme.sh --issue  \
  -d  importantDomain.com --challenge-alias aliasDomainForValidationOnly.com --dns dns_cf
  -d  www.importantDomain.com \
  -d  sub.importantDomain.com \
  -d  importantDomain2.com \
  -d  importantDomain3.com \
  -d  *.importantDomain.com \
  -d  *.importantDomain2.com \
  -d  *.importantDomain3.com
```

### 4. Specify different aliased domains for each domain.

Yes, you know, acme.sh support to set alias domain for each domain. Even with different dns provider:

You can set CNAME like:

```sh
_acme-challenge.importantDomain.com  
  =>   _acme-challenge.aliasDomainForValidationOnly.com

_acme-challenge.importantDomain2.com  
  => _acme-challenge.aliasDomainForValidationOnly2.com
```

Then issue cert:

```sh

acme.sh --issue  \
  -d  importantDomain.com --challenge-alias aliasDomainForValidationOnly.com --dns dns_cf
  -d  importantDomain2.com --challenge-alias aliasDomainForValidationOnly2.com
```

Even with different dns provider:

```sh
acme.sh --issue  \
  -d  importantDomain.com --challenge-alias aliasDomainForValidationOnly.com --dns dns_cf
  -d  importantDomain2.com --challenge-alias aliasDomainForValidationOnly2.com  --dns dns_gd
```

Let's assume the first domain `aliasDomainForValidationOnly.com` is hosted at cloudflare, and the second is hosted at godaddy.


### 5. Last

Do not remove the CNAME like : `_acme-challenge.importantDomain.com` after you issue the cert.  It will be reused when acme.sh tries o renew the cert.  The left cname record `_acme-challenge.importantDomain.com` doesn't harm to your domain at all.  Just keep it there.




