# Quick-Start

## 1. Create application key, secret and consumer

- OVH Europe: https://api.ovh.com/createToken/?GET=/domain/zone/*&POST=/domain/zone/*&PUT=/domain/zone/*
- OVH Canada: https://ca.api.ovh.com/createToken/?GET=/domain/zone/*&POST=/domain/zone/*&PUT=/domain/zone/*

In the page, please  select "Unlimited" for the Validity.

## 2. Profite!

```
# Credentials
export OVH_AK="your application key"
export OVH_AS="your application secret"
export OVH_CK="your consumer key"

# Generate your certificate
acme.sh --issue -d mydomain.com --dns dns_ovh
```

# Security

It is a good security practice to limit what a given API key can in the event it is lost, stolen or anything wrong happens to limit the potential damages. OVH API keys can be limited to a specific domain zone using a simple pattern mechanism. For example, to restrict an OVH API key to manage "mydomain.com", you may use the following settings. Of course this can easily be customized to support any or multiple domains:

- OVH Europe: https://api.ovh.com/createToken/?GET=/domain/zone/mydomain.com/*&POST=/domain/zone/mydomain.com/*&PUT=/domain/zone/mydomain.com/*&GET=/domain/zone/mydomain.com
- OVH Canada: https://ca.api.ovh.com/createToken/?GET=/domain/zone/mydomain.com/*&POST=/domain/zone/mydomain.com/*&PUT=/domain/zone/mydomain.com/*&GET=/domain/zone/mydomain.com
