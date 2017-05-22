## 1. Create application key, secret and consumer

It is a good security practice to limit the power of individual credentials to limit the damage in case anything goes wrong. acme.sh only requires access to the domain API to deal with the dns-01 challenge.

**If you plan to manage SSL certificates for multiple/any zones:**

https://api.ovh.com/createToken/?GET=/domain/zone/*&POST=/domain/zone/*&PUT=/domain/zone/*

**If you plan to manage SSL certificates for a specif zone:**

https://api.ovh.com/createToken/?GET=/domain/zone/mydomain.com&GET=/domain/zone/mydomain.com/*&POST=/domain/zone/mydomain.com/*&PUT=/domain/zone/mydomain.com/*

In the page, please  select "Unlimited" for the Validity and replace ``mydomain.com`` by the zone you need to manage

## 2. Set API key and API secret.

```
# application key
export OVH_AK="your application key"

# application secret
export OVH_AS="your application secret"

# consumer key
export OVH_CK="your consumer key"

acme.sh --issue -d   mydomain.com   --dns   dns_ovh
```

## 3. OVH api support OVH, kimsufi, soyoustart and runabove.
The default is using  ovh-eu region.

if you are using  ovh-ca region, 

Create app key here:  https://ca.api.ovh.com/createToken/

Then please specify the region in the first step:

```
export OVH_END_POINT=ovh-ca

export OVH_AK="your application key"

export OVH_AS="your application secret"

export OVH_CK="your consumer key"

acme.sh --issue -d   mydomain.com   --dns   dns_ovh

```

Supported regions:
```
#'ovh-eu'  (default)
#'ovh-ca': 
#'kimsufi-eu'
#'kimsufi-ca'
#'soyoustart-eu'
#'soyoustart-ca'
#'runabove-ca'
```