# Quick Start

## 1. Create application key and secret
https://eu.api.ovh.com/createApp/

## 2. Set api key and api secret.

```
# application key
export OVH_AK="your application key"

# application secret
export OVH_AS="your application secret"

acme.sh --issue -d   mydomain.com   --dns   dns_ovh
```

If you are first time using OVH api,  you are required to authenticate the api. (This only happens the first time.)

You will see some thing like bellow:

```
[Thu, Aug 25, 2016 10:54:03] Using OVH endpoint: ovh-eu
[Thu, Aug 25, 2016 10:54:04] OVH consumer key is empty, Let's get one:
[Thu, Aug 25, 2016 10:54:05] Please open this link to do authentication: https://eu.api.ovh.com/auth/?credentialToken=n0Qbjm6wBdBr2KiSqIuYSEnixxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
[Thu, Aug 25, 2016 10:54:05] Here is a guide for you: https://github.com/Neilpang/acme.sh/wiki/How-to-use-OVH-domain-api
[Thu, Aug 25, 2016 10:54:05] Please retry after the authentication is done.
[Thu, Aug 25, 2016 10:54:05] Error add txt for domain:_acme-challenge.mytest.mydomain.com

```


## 3. Authentication the api key.
 (This only happens the first time.)

Open the link above:
```
https://eu.api.ovh.com/auth/?credentialToken=n0Qbjm6wBdBr2KiSqIuYSEnixxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

In the page, please  select "Unlimited" for the Validity.

Click "Authorize Access"


## 4. Then go back to try again.

```
acme.sh --issue -d   mydomain.com   --dns   dns_ovh
```

Done.



## 5. OVH api support OVH, kimsufi, soyoustart and runabove.
The default is using  ovh-eu region.

if you are using  ovh-ca region, 

Create app key here:  https://ca.api.ovh.com/createApp/

Then please specify the region in the first step:

```
export OVH_END_POINT=ovh-ca

export OVH_AK="your application key"

export OVH_AS="your application secret"

acme.sh --issue -d   mydomain.com   --dns   dns_ovh

```


All the supported regions:
```
#'ovh-eu'  (default)
#'ovh-ca': 
#'kimsufi-eu'
#'kimsufi-ca'
#'soyoustart-eu'
#'soyoustart-ca'
#'runabove-ca'
```






# Advanced Usage

## 1. Create application key, secret and consumer

- **OVH Europe**: `https://api.ovh.com/createToken/?GET=/domain/zone/*&POST=/domain/zone/*&PUT=/domain/zone/*&DELETE=/domain/zone/*/record/*`
- **OVH Canada**: `https://ca.api.ovh.com/createToken/?GET=/domain/zone/*&POST=/domain/zone/*&PUT=/domain/zone/*&DELETE=/domain/zone/*/record/*`

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

- **OVH Europe**: `https://api.ovh.com/createToken/?GET=/domain/zone/mydomain.com/*&POST=/domain/zone/mydomain.com/*&PUT=/domain/zone/mydomain.com/*&GET=/domain/zone/mydomain.com&DELETE=/domain/zone/mydomain.com/record/*`
- **OVH Canada**: `https://ca.api.ovh.com/createToken/?GET=/domain/zone/mydomain.com/*&POST=/domain/zone/mydomain.com/*&PUT=/domain/zone/mydomain.com/*&GET=/domain/zone/mydomain.com&DELETE=/domain/zone/mydomain.com/record/*`
