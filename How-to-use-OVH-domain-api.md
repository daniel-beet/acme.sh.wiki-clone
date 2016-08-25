### 1. Create application key and secret
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



