# How to use DNS API 

If your DNS provider doesn't provide API access, you can use our DNS alias mode:

https://github.com/acmesh-official/acme.sh/wiki/DNS-alias-mode

## 1. Cloudflare Option:

Currently Cloudflare doesn't allow API access in free tld domain. You will get the Txt records insert failed. Make sure to verify your api token before editing  dns_cf, you can alternatively use DNS manual mode if you are using free tld on cloudflare.

Cloudflare Domain API offers two methods to automatically issue certs.

### Using the global API key
First you need to login to your Cloudflare account to get your [API key](https://dash.cloudflare.com/profile). Each token generated is not stored on cloudflare account and will have expiry if not set correctly. You will get this in API keys section.  
```
export CF_Key="sdfsdfsdfljlbjkljlkjsdfoiwje"
export CF_Email="xxxx@sss.com"
```

### Using the new cloudflare api token, you will get this after normal login and  scroll down on dashboard and copy credentials. 
```
export CF_Token="sdfsdfsdfljlbjkljlkjsdfoiwje"
export CF_Account_ID="xxxxxxxxxxxxx"
```
In order to use the new token, the token currently needs access read access to Zone.Zone, and write access to Zone.DNS, across all Zones.  See [Issue #2398](https://github.com/Neilpang/acme.sh/issues/2398) for more info.

Alternatively, if the certificate only covers a single zone, you can restrict the API Token only for write access to Zone.DNS for a single domain, and then specify the `CF_Zone_ID` directly:

```
export CF_Token="sdfsdfsdfljlbjkljlkjsdfoiwje"
export CF_Account_ID="xxxxxxxxxxxxx"
export CF_Zone_ID="xxxxxxxxxxxxx"
```

Ok, let's issue a cert now:
```
acme.sh --issue --dns dns_cf -d example.com -d www.example.com
```

The `CF_Key` and `CF_Email` or `CF_Token`and `CF_Account_ID`will be saved in `~/.acme.sh/account.conf` and will be reused when needed.

## 2. DNSPod.cn Option:

The DNSPod.cn Domain API option requires that you first login to your account to get a DNSPod API Key and ID.

```
export DP_Id="1234"
export DP_Key="sADDsdasdgdsf"
```

Ok, let's issue a cert now:
```
acme.sh --issue --dns dns_dp -d example.com -d www.example.com
```

The `DP_Id` and `DP_Key` will be saved in `~/.acme.sh/account.conf` and will be reused when needed.


## 3. Use CloudXNS.com domain API to automatically issue cert

First you need to login to your CloudXNS account to get your API Key and Secret.

```
export CX_Key="1234"
export CX_Secret="sADDsdasdgdsf"
```

Ok, let's issue a cert now:
```
acme.sh --issue --dns dns_cx -d example.com -d www.example.com
```

The `CX_Key` and `CX_Secret` will be saved in `~/.acme.sh/account.conf` and will be reused when needed.


## 4. Use GoDaddy.com domain API to automatically issue cert

First you need to login to your GoDaddy account to get your API Key and Secret.

https://developer.godaddy.com/keys/

Please create a Production key, instead of a Test key.

```
export GD_Key="sdfsdfsdfljlbjkljlkjsdfoiwje"
export GD_Secret="asdfsdafdsfdsfdsfdsfdsafd"
```

Ok, let's issue a cert now:
```
acme.sh --issue --dns dns_gd -d example.com -d www.example.com
```

The `GD_Key` and `GD_Secret` will be saved in `~/.acme.sh/account.conf` and will be reused when needed.


## 5. Use PowerDNS embedded API to automatically issue cert

First you need to login to your PowerDNS account to enable the API and set your API-Token in the configuration.

https://doc.powerdns.com/md/httpapi/README/

```
export PDNS_Url="http://ns.example.com:8081"
export PDNS_ServerId="localhost"
export PDNS_Token="0123456789ABCDEF"
export PDNS_Ttl=60
```

Ok, let's issue a cert now:
```
acme.sh --issue --dns dns_pdns -d example.com -d www.example.com
```

The `PDNS_Url`, `PDNS_ServerId`, `PDNS_Token` and `PDNS_Ttl` will be saved in `~/.acme.sh/account.conf` and will be reused when needed.


## 6. Use OVH/kimsufi/soyoustart/runabove API to automatically issue cert

https://github.com/Neilpang/acme.sh/wiki/How-to-use-OVH-domain-api


## 7. Use nsupdate to automatically issue cert

First, generate a key for updating the zone
```
b=$(dnssec-keygen -a hmac-sha512 -b 512 -n USER -K /tmp foo)
cat > /etc/named/keys/update.key <<EOF
key "update" {
    algorithm hmac-sha512;
    secret "$(awk '/^Key/{print $2}' /tmp/$b.private)";
};
EOF
rm -f /tmp/$b.{private,key}
```

Include this key in your named configuration
```
include "/etc/named/keys/update.key";
```

Next, configure your zone to allow dynamic updates.

Depending on your named version, use either
```
zone "example.com" {
    type master;
    allow-update { key "update"; };
};
```
or
```
zone "example.com" {
    type master;
    update-policy {
        grant update subdomain example.com.;
    };
}
```

Finally, make the DNS server and update Key available to `acme.sh`

```
export NSUPDATE_SERVER="dns.example.com"
export NSUPDATE_KEY="/path/to/your/nsupdate.key"
```
and optionally (depending on DNS server)
```
export NSUPDATE_ZONE="example.com"
```

Ok, let's issue a cert now:
```
acme.sh --issue --dns dns_nsupdate -d example.com -d www.example.com
```

The `NSUPDATE_SERVER`, `NSUPDATE_KEY`, and `NSUPDATE_ZONE` settings will be saved in `~/.acme.sh/account.conf` and will be reused when needed.


## 8. Use LuaDNS domain API

Get your API token at https://api.luadns.com/settings

```
export LUA_Key="sdfsdfsdfljlbjkljlkjsdfoiwje"
export LUA_Email="xxxx@sss.com"
```

To issue a cert:
```
acme.sh --issue --dns dns_lua -d example.com -d www.example.com
```

The `LUA_Key` and `LUA_Email` will be saved in `~/.acme.sh/account.conf` and will be reused when needed.


## 9. Use DNSMadeEasy domain API

Get your API credentials at https://cp.dnsmadeeasy.com/account/info

```
export ME_Key="sdfsdfsdfljlbjkljlkjsdfoiwje"
export ME_Secret="qdfqsdfkjdskfj"
```

To issue a cert:
```
acme.sh --issue --dns dns_me -d example.com -d www.example.com
```

The `ME_Key` and `ME_Secret` will be saved in `~/.acme.sh/account.conf` and will be reused when needed.


## 10. Use Amazon Route53 domain API

https://github.com/Neilpang/acme.sh/wiki/How-to-use-Amazon-Route53-API

```
export  AWS_ACCESS_KEY_ID=XXXXXXXXXX
export  AWS_SECRET_ACCESS_KEY=XXXXXXXXXXXXXXX

```

To issue a cert:
```
acme.sh --issue --dns dns_aws -d example.com -d www.example.com
```

If you get an `AWS Route53 rate exceeded` error, you can add a sleep time between api requests:

```
export  AWS_DNS_SLOWRATE=1 (sleep between API requests in seconds)
```

The `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY` and `AWS_DNS_SLOWRATE` will be saved in `~/.acme.sh/account.conf` and will be reused when needed. The `AWS_DNS_SLOWRATE` will enable the sleep between API requests to AWS servers. It will help to mitigate the AWS rate limit

## 11. Use Aliyun domain API to automatically issue cert

First you need to login to your Aliyun account to get your API key.
[https://ak-console.aliyun.com/#/accesskey](https://ak-console.aliyun.com/#/accesskey)

```
export Ali_Key="sdfsdfsdfljlbjkljlkjsdfoiwje"
export Ali_Secret="jlsdflanljkljlfdsaklkjflsa"
```

Ok, let's issue a cert now:
```
acme.sh --issue --dns dns_ali -d example.com -d www.example.com
```

The `Ali_Key` and `Ali_Secret` will be saved in `~/.acme.sh/account.conf` and will be reused when needed.

## 12. Use ISPConfig 3.1 API

This only works for ISPConfig 3.1 (and newer).

Create a Remote User in the ISPConfig Control Panel. The Remote User must have access to at least `DNS zone functions` and `DNS txt functions`.

```
export ISPC_User="xxx"
export ISPC_Password="xxx"
export ISPC_Api="https://ispc.domain.tld:8080/remote/json.php"
export ISPC_Api_Insecure=1
```
If you have installed ISPConfig on a different port, then alter the 8080 accordingly.
Leaver ISPC_Api_Insecure set to 1 if you have not a valid ssl cert for your installation. Change it to 0 if you have a valid ssl cert.

To issue a cert:
```
acme.sh --issue --dns dns_ispconfig -d example.com -d www.example.com
```

The `ISPC_User`, `ISPC_Password`, `ISPC_Api`and `ISPC_Api_Insecure` will be saved in `~/.acme.sh/account.conf` and will be reused when needed.

## 13. Use Alwaysdata domain API

First you need to login to your Alwaysdata account to get your API Key.

```sh
export AD_API_KEY="myalwaysdataapikey"
```

Ok, let's issue a cert now:

```sh
acme.sh --issue --dns dns_ad -d example.com -d www.example.com
```

The `AD_API_KEY` will be saved in `~/.acme.sh/account.conf` and will be reused
when needed.

## 14. Use Linode domain API

The tokens created in the classic manager and cloud manager are incompatible
with one another. While the classic manager makes an all or nothing API, the
newer cloud manager interface promises to produce API keys with a finer
permission system. However, either way works just fine.

### Classic Manager ###

Classic Manager: https://manager.linode.com/profile/api

First you need to login to your Linode account to get your API Key.

Then add an API key with label *ACME* and copy the new key into the following
command.

```sh
export LINODE_API_KEY="..."
```

Due to the reload time of any changes in the DNS records, we have to use the
`dnssleep` option to wait at least 15 minutes for the changes to take effect.

Ok, let's issue a cert now:

```sh
acme.sh --issue --dns dns_linode --dnssleep 900 -d example.com -d www.example.com
```

The `LINODE_API_KEY` will be saved in `~/.acme.sh/account.conf` and will be
reused when needed.

### Cloud Manager ###

Cloud Manager: https://cloud.linode.com/profile/tokens

First you need to login to your Linode account to get your API Key.

   1. Click on "Add a Personal Access Token".
   2. Give the new key a "Label" (we recommend *ACME*)
   3. Give it Read/Write access to "Domains"
   4. "Submit" and copy the new key into the `LINODE_V4_API_KEY` command below.

```sh
export LINODE_V4_API_KEY="..."
```

Due to the reload time of any changes in the DNS records, we have to use the
`dnssleep` option to wait at least 15 minutes for the changes to take effect.

Ok, let's issue a cert now:

```sh
acme.sh --issue --dns dns_linode_v4 --dnssleep 900 -d example.com -d www.example.com
```

The `LINODE_V4_API_KEY` will be saved in `~/.acme.sh/account.conf` and will be
reused when needed.

## 15. Use FreeDNS

FreeDNS (https://freedns.afraid.org/) does not provide an API to update DNS records (other than IPv4 and IPv6
dynamic DNS addresses).  The acme.sh plugin therefore retrieves and updates domain TXT records by logging
into the FreeDNS website to read the HTML and posting updates as HTTP.  The plugin needs to know your
userid and password for the FreeDNS website.

```sh
export FREEDNS_User="..."
export FREEDNS_Password="..."
```

You need only provide this the first time you run the acme.sh client with FreeDNS validation and then again
whenever you change your password at the FreeDNS site.  The acme.sh FreeDNS plugin does not store your userid
or password but rather saves an authentication token returned by FreeDNS in `~/.acme.sh/account.conf` and
reuses that when needed.

Now you can issue a certificate.

```sh
acme.sh --issue --dns dns_freedns -d example.com -d www.example.com
```

Note that you cannot use acme.sh automatic DNS validation for FreeDNS public domains or for a subdomain that
you create under a FreeDNS public domain.  You must own the top level domain in order to automatically
validate with acme.sh at FreeDNS.

If you have any issues with FreeDNS API please report them here...
https://github.com/Neilpang/acme.sh/issues/2305

## 16. Use cyon.ch

You only need to set your cyon.ch login credentials.
If you also have 2 Factor Authentication (OTP) enabled, you need to set your secret token too and have `oathtool` installed.

```
export CY_Username="your_cyon_username"
export CY_Password="your_cyon_password"
export CY_OTP_Secret="your_otp_secret" # Only required if using 2FA
```

To issue a cert:
```
acme.sh --issue --dns dns_cyon -d example.com -d www.example.com
```

The `CY_Username`, `CY_Password` and `CY_OTP_Secret` will be saved in `~/.acme.sh/account.conf` and will be reused when needed.

## 17. Use Domain-Offensive/Resellerinterface/Domainrobot API

ATTENTION: You need to be a registered Reseller to be able to use the ResellerInterface. As a normal user you can not use this method.

You will need your login credentials (Partner ID+Password) to the Resellerinterface, and export them before you run `acme.sh`:
```
export DO_PID="KD-1234567"
export DO_PW="cdfkjl3n2"
```

Ok, let's issue a cert now:
```
acme.sh --issue --dns dns_do -d example.com -d www.example.com
```

## 18. Use Gandi LiveDNS API

You must enable the new Gandi LiveDNS API first and the create your api key, See: http://doc.livedns.gandi.net/

```
export GANDI_LIVEDNS_KEY="fdmlfsdklmfdkmqsdfk"
```

Ok, let's issue a cert now:
```
acme.sh --issue --dns dns_gandi_livedns -d example.com -d www.example.com
```

## 19. Use Knot (knsupdate) DNS API to automatically issue cert

First, generate a TSIG key for updating the zone.

```
keymgr tsig generate -t acme_key hmac-sha512 > /etc/knot/acme.key
```

Include this key in your knot configuration file.

```
include: /etc/knot/acme.key
```

Next, configure your zone to allow dynamic updates.

Dynamic updates for the zone are allowed via proper ACL rule with the `update` action. For in-depth instructions, please see [Knot DNS's documentation](https://www.knot-dns.cz/documentation/).

```
acl:
  - id: acme_acl
    address: 192.168.1.0/24
    key: acme_key
    action: update

zone:
  - domain: example.com
    file: example.com.zone
    acl: acme_acl
```

Finally, make the DNS server and TSIG Key available to `acme.sh`

```
export KNOT_SERVER="dns.example.com"
export KNOT_KEY=`grep \# /etc/knot/acme.key | cut -d' ' -f2`
```

Ok, let's issue a cert now:
```
acme.sh --issue --dns dns_knot -d example.com -d www.example.com
```

The `KNOT_SERVER` and `KNOT_KEY` settings will be saved in `~/.acme.sh/account.conf` and will be reused when needed.

## 20. Use DigitalOcean API (native)

You need to obtain a read and write capable API key from your DigitalOcean account. See: https://www.digitalocean.com/help/api/

```
export DO_API_KEY="75310dc4ca779ac39a19f6355db573b49ce92ae126553ebd61ac3a3ae34834cc"
```

Ok, let's issue a cert now:
```
acme.sh --issue --dns dns_dgon -d example.com -d www.example.com
```

## 21. Use ClouDNS.net API

You need to set the HTTP API user ID and password credentials. See: https://www.cloudns.net/wiki/article/42/. For security reasons, it's recommended to use a sub user ID that only has access to the necessary zones, as a regular API user has access to your entire account.

```
# Use this for a sub auth ID
export CLOUDNS_SUB_AUTH_ID=XXXXX
# Use this for a regular auth ID
#export CLOUDNS_AUTH_ID=XXXXX
export CLOUDNS_AUTH_PASSWORD="YYYYYYYYY"
```

Ok, let's issue a cert now:
```
acme.sh --issue --dns dns_cloudns -d example.com -d www.example.com
```
The `CLOUDNS_AUTH_ID` and `CLOUDNS_AUTH_PASSWORD` will be saved in `~/.acme.sh/account.conf` and will be reused when needed.

## 22. Use Infoblox API

First you need to create/obtain API credentials on your Infoblox appliance.

```
export Infoblox_Creds="username:password"
export Infoblox_Server="ip or fqdn of infoblox appliance"
```

Ok, let's issue a cert now:
```
acme.sh --issue --dns dns_infoblox -d example.com -d www.example.com
```

Note: This script will automatically create and delete the ephemeral txt record.
The `Infoblox_Creds` and `Infoblox_Server` will be saved in `~/.acme.sh/account.conf` and will be reused when needed.


## 23. Use VSCALE API

First you need to create/obtain API tokens on your [settings panel](https://vscale.io/panel/settings/tokens/).

```
export VSCALE_API_KEY="sdfsdfsdfljlbjkljlkjsdfoiwje"
```

Ok, let's issue a cert now:
```
acme.sh --issue --dns dns_vscale -d example.com -d www.example.com
```

##  24. Use Dynu API

First you need to create/obtain API credentials from your Dynu account. See: https://www.dynu.com/resources/api/documentation

```
export Dynu_ClientId="xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
export Dynu_Secret="yyyyyyyyyyyyyyyyyyyyyyyyy"
```

Ok, let's issue a cert now:
```
acme.sh --issue --dns dns_dynu -d example.com -d www.example.com
```

The `Dynu_ClientId` and `Dynu_Secret` will be saved in `~/.acme.sh/account.conf` and will be reused when needed.

## 25. Use DNSimple API

First you need to login to your DNSimple account and generate a new oauth token.

https://dnsimple.com/a/{your account id}/account/automation

Note that this is an _account_ token and not a user token. The account token is
needed to infer the `account_id` used in requests. A user token will not be able
to determine the correct account to use.

```
export DNSimple_OAUTH_TOKEN="sdfsdfsdfljlbjkljlkjsdfoiwje"
```

To issue the cert just specify the `dns_dnsimple` API.

```
acme.sh --issue --dns dns_dnsimple -d example.com
```

The `DNSimple_OAUTH_TOKEN` will be saved in `~/.acme.sh/account.conf` and will
be reused when needed.

If you have any issues with this integration please report them to
https://github.com/pho3nixf1re/acme.sh/issues.

## 26. Use NS1.com API

```
export NS1_Key="fdmlfsdklmfdkmqsdfk"
```

Ok, let's issue a cert now:
```
acme.sh --issue --dns dns_nsone -d example.com -d www.example.com
```

## 27. Use DuckDNS.org API

```
export DuckDNS_Token="aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee"
```

Please note that since DuckDNS uses StartSSL as their cert provider, thus
--insecure may need to be used when issuing certs:
```
acme.sh --insecure --issue --dns dns_duckdns -d mydomain.duckdns.org
```

For issues, please report to https://github.com/raidenii/acme.sh/issues.

## 28. Use Name.com API

Create your API token here: https://www.name.com/account/settings/api

Note: `Namecom_Username` should be your Name.com username and not the token name.  If you accidentally run the script with the token name as the username see `~/.acme.sh/account.conf` to fix the issue

```
export Namecom_Username="testuser"
export Namecom_Token="xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
```

And now you can issue certs with:

```
acme.sh --issue --dns dns_namecom -d example.com -d www.example.com
```
If you had Two-step Authentication enabled, make sure to change your security setting, read this guide for help: [Using API with Two-step Authentication](https://www.name.com/support/articles/360007989433-Using-API-with-Two-step-Authentication)

For issues, please report to https://github.com/raidenii/acme.sh/issues.

## 29. Use Dyn Managed DNS API to automatically issue cert

First, login to your Dyn Managed DNS account: https://portal.dynect.net/login/

It is recommended to add a new user specific for API access.

The minimum "Zones & Records Permissions" required are:
```
RecordAdd
RecordUpdate
RecordDelete
RecordGet
ZoneGet
ZoneAddNode
ZoneRemoveNode
ZonePublish
```

Pass the API user credentials to the environment:
```
export DYN_Customer="customer"
export DYN_Username="apiuser"
export DYN_Password="secret"
```

Ok, let's issue a cert now:
```
acme.sh --issue --dns dns_dyn -d example.com -d www.example.com
```

The `DYN_Customer`, `DYN_Username` and `DYN_Password` will be saved in `~/.acme.sh/account.conf` and will be reused when needed.

## 30. Use pdd.yandex.ru API

```
export PDD_Token="xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
```

Follow these instructions to get the token for your domain https://tech.yandex.com/domain/doc/concepts/access-docpage/
```
acme.sh --issue --dns dns_yandex -d mydomain.example.org
```

Sometimes cloudflare / google doesn't pick new dns records fast enough. You can add `--dnssleep XXX` to params as workaround.

For issues, please report to https://github.com/non7top/acme.sh/issues.

## 31. Use Hurricane Electric

Hurricane Electric (https://dns.he.net/) doesn't have an API so just set your login credentials like so:

```
export HE_Username="yourusername"
export HE_Password="password"
```

Then you can issue your certificate:

```
acme.sh --issue --dns dns_he -d example.com -d www.example.com
```

The `HE_Username` and `HE_Password` settings will be saved in `~/.acme.sh/account.conf` and will be reused when needed.

Please report any issues to https://github.com/angel333/acme.sh or to <me@ondrejsimek.com>.

## 32. Use UnoEuro API to automatically issue cert

First you need to login to your UnoEuro account to get your API key.

```
export UNO_Key="sdfsdfsdfljlbjkljlkjsdfoiwje"
export UNO_User="UExxxxxx"
```

Ok, let's issue a cert now:
```
acme.sh --issue --dns dns_unoeuro -d example.com -d www.example.com
```

The `UNO_Key` and `UNO_User` will be saved in `~/.acme.sh/account.conf` and will be reused when needed.

## 33. Use INWX

[INWX](https://www.inwx.de/) offers an [xmlrpc api](https://www.inwx.de/de/help/apidoc)  with your standard login credentials, set them like so:

```
export INWX_User="yourusername"
export INWX_Password="password"
```

Then you can issue your certificates with:

```
acme.sh --issue --dns dns_inwx -d example.com -d www.example.com
```

The `INWX_User` and `INWX_Password` settings will be saved in `~/.acme.sh/account.conf` and will be reused when needed.

If your account is secured by mobile tan you have also defined the shared secret.

```
export INWX_Shared_Secret="shared secret"
```

You may need to re-enable the mobile tan to gain the shared secret.

## 34. User Servercow API v1

Create a new user from the servercow control center. Don't forget to activate **DNS API** for this user.

```
export SERVERCOW_API_Username=username
export SERVERCOW_API_Password=password
```

Now you cann issue a cert:

```
acme.sh --issue --dns dns_servercow -d example.com -d www.example.com
```
Both, `SERVERCOW_API_Username` and `SERVERCOW_API_Password` will be saved in `~/.acme.sh/account.conf` and will be reused when needed.

## 35. Use Namesilo.com API

You'll need to generate an API key at https://www.namesilo.com/account_api.php
Optionally you may restrict the access to an IP range there.

```
export Namesilo_Key="xxxxxxxxxxxxxxxxxxxxxxxx"
```

And now you can issue certs with:

```
acme.sh --issue --dns dns_namesilo --dnssleep 900 -d example.com -d www.example.com
```

## 36. Use autoDNS (InternetX)

[InternetX](https://www.internetx.com/) offers an [xml api](https://help.internetx.com/display/API/AutoDNS+XML-API)  with your standard login credentials, set them like so:

```
export AUTODNS_USER="yourusername"
export AUTODNS_PASSWORD="password"
export AUTODNS_CONTEXT="context"
```

Then you can issue your certificates with:

```
acme.sh --issue --dns dns_autodns -d example.com -d www.example.com
```

The `AUTODNS_USER`, `AUTODNS_PASSWORD` and `AUTODNS_CONTEXT` settings will be saved in `~/.acme.sh/account.conf` and will be reused when needed.

## 37. Use Azure DNS

You have to create a service principal first. See:[How to use Azure DNS](https://github.com/acmesh-official/acme.sh/wiki/How-to-use-Azure-DNS)

```
export AZUREDNS_SUBSCRIPTIONID="12345678-9abc-def0-1234-567890abcdef"
export AZUREDNS_TENANTID="11111111-2222-3333-4444-555555555555"
export AZUREDNS_APPID="3b5033b5-7a66-43a5-b3b9-a36b9e7c25ed"
export AZUREDNS_CLIENTSECRET="1b0224ef-34d4-5af9-110f-77f527d561bd"
```

Then you can issue your certificates with:

```
acme.sh --issue --dns dns_azure -d example.com -d www.example.com
```

`AZUREDNS_SUBSCRIPTIONID`, `AZUREDNS_TENANTID`,`AZUREDNS_APPID` and `AZUREDNS_CLIENTSECRET` settings will be saved in `~/.acme.sh/account.conf` and will be reused when needed.

## 38. Use selectel.com(selectel.ru) domain API to automatically issue cert

First you need to login to your account to get your API key from: https://my.selectel.ru/profile/apikeys.

```sh
export SL_Key="sdfsdfsdfljlbjkljlkjsdfoiwje"

```

Ok, let's issue a cert now:
```
acme.sh --issue --dns dns_selectel -d example.com -d www.example.com
```

The `SL_Key` will be saved in `~/.acme.sh/account.conf` and will be reused when needed.

## 39. Use zonomi.com domain API to automatically issue cert

First you need to login to your account to find your API key from: http://zonomi.com/app/dns/dyndns.jsp

Your will find your api key in the example urls:

```sh
https://zonomi.com/app/dns/dyndns.jsp?host=example.com&api_key=1063364558943540954358668888888888
```

```sh
export ZM_Key="1063364558943540954358668888888888"

```

Ok, let's issue a cert now:
```
acme.sh --issue --dns dns_zonomi -d example.com -d www.example.com
```

The `ZM_Key` will be saved in `~/.acme.sh/account.conf` and will be reused when needed.

## 40. Use DreamHost DNS API

DNS API keys may be created at https://panel.dreamhost.com/?tree=home.api.
Ensure the created key has add and remove privelages.

```
export DH_API_KEY="<api key>"
acme.sh --issue --dns dns_dreamhost -d example.com -d www.example.com
```

The 'DH_API_KEY' will be saved in `~/.acme.sh/account.conf` and will
be reused when needed.

## 41. Use DirectAdmin API
The DirectAdmin interface has it's own Let's encrypt functionality, but this
script can be used to generate certificates for names which are not hosted on
DirectAdmin

User must provide login data and URL to the DirectAdmin incl. port.
You can create an user which only has access to

- CMD_API_DNS_CONTROL
- CMD_API_SHOW_DOMAINS

By using the Login Keys function.
See also https://www.directadmin.com/api.php and https://www.directadmin.com/features.php?id=1298

```
export DA_Api="https://remoteUser:remotePassword@da.domain.tld:8443"
export DA_Api_Insecure=1
```
Set `DA_Api_Insecure` to 1 for insecure and 0 for secure -> difference is whether ssl cert is checked for validity (0) or whether it is just accepted (1)

Ok, let's issue a cert now:
```
acme.sh --issue --dns dns_da -d example.com -d www.example.com
```

The `DA_Api` and `DA_Api_Insecure` will be saved in `~/.acme.sh/account.conf` and will be reused when needed.

## 42. Use KingHost DNS API

API access must be enabled at https://painel.kinghost.com.br/painel.api.php

```
export KINGHOST_Username="yourusername"
export KINGHOST_Password="yourpassword"
acme.sh --issue --dns dns_kinghost -d example.com -d *.example.com
```

The `KINGHOST_username` and `KINGHOST_Password` will be saved in `~/.acme.sh/account.conf` and will be reused when needed.

## 43. Use Zilore DNS API

First, get your API key at https://my.zilore.com/account/api

```
export Zilore_Key="5dcad3a2-36cb-50e8-cb92-000002f9"
```

Ok, let's issue a cert now:
```
acme.sh --issue --dns dns_zilore -d example.com -d *.example.com
```

The `Zilore_Key` will be saved in `~/.acme.sh/account.conf` and will be reused when needed.

## 44. Use Loopia API

User must provide login credentials to the Loopia API.
The user needs the following permissions:

- getDomains
- getSubdomains
- addSubdomain
- removeSubdomain
- getZoneRecords
- addZoneRecord

Set the API endpoint:

```
export LOOPIA_Api="https://api.loopia.<TLD>/RPCSERV"
```

Depending on your hosting location, `<TLD>` is one of: `com`, `no`,
`rs`, `se`. The default endpoint is `se` TLD.

Set the login credentials:

```
export LOOPIA_User="user@loopiaapi"
export LOOPIA_Password="password"
```

And to issue a cert run:

```
acme.sh --issue --dns dns_loopia -d example.com -d *.example.com
```

The exported variables will be saved in `~/.acme.sh/account.conf` and
will be reused when needed.

## 45. Use ACME DNS API

ACME DNS is a limited DNS server with RESTful HTTP API to handle ACME DNS challenges easily and securely.
https://github.com/joohoi/acme-dns

```
export ACMEDNS_UPDATE_URL="https://auth.acme-dns.io/update"
export ACMEDNS_USERNAME="<username>"
export ACMEDNS_PASSWORD="<password>"
export ACMEDNS_SUBDOMAIN="<subdomain>"

acme.sh --issue --dns dns_acmedns -d example.com -d www.example.com
```

The credentials will be saved in `~/.acme.sh/account.conf` and will
be reused when needed.
## 46. Use TELE3 API

First you need to login to your TELE3 account to set your API-KEY.
https://www.tele3.cz/system-acme-api.html

```
export TELE3_Key="MS2I4uPPaI..."
export TELE3_Secret="kjhOIHGJKHg"

acme.sh --issue --dns dns_tele3 -d example.com -d *.example.com
```

The TELE3_Key and TELE3_Secret will be saved in ~/.acme.sh/account.conf and will be reused when needed.

## 47. Use Euserv.eu API

First you need to login to your euserv.eu account and activate your API Administration (API Verwaltung).
[https://support.euserv.com](https://support.euserv.com)

Once you've activate, login to your API Admin Interface and create an API account.
Please specify the scope (active groups: domain) and assign the allowed IPs.

```
export EUSERV_Username="99999.user123"
export EUSERV_Password="Asbe54gHde"
```

Ok, let's issue a cert now: (Be aware to use the `--insecure` flag, cause euserv.eu is still using self-signed certificates!)
```
acme.sh --issue --dns dns_euserv -d example.com -d *.example.com --insecure
```

The `EUSERV_Username` and `EUSERV_Password` will be saved in `~/.acme.sh/account.conf` and will be reused when needed.

Please report any issues to https://github.com/initit/acme.sh or to <github@initit.de>

## 48. Use DNSPod.com domain API to automatically issue cert

First you need to get your API Key and ID by this [get-the-user-token](https://www.dnspod.com/docs/info.html#get-the-user-token).

```
export DPI_Id="1234"
export DPI_Key="sADDsdasdgdsf"
```

Ok, let's issue a cert now:
```
acme.sh --issue --dns dns_dpi -d example.com -d www.example.com
```

The `DPI_Id` and `DPI_Key` will be saved in `~/.acme.sh/account.conf` and will be reused when needed.

## 49. Use Google Cloud DNS API to automatically issue cert

First you need to authenticate to gcloud.

```
gcloud init
```

**The `dns_gcloud` script uses the active gcloud configuration and credentials.**
There is no logic inside `dns_gcloud` to override the project and other settings.
If needed, create additional [gcloud configurations](https://cloud.google.com/sdk/gcloud/reference/topic/configurations).
You can change the configuration being used without *activating* it; simply set the `CLOUDSDK_ACTIVE_CONFIG_NAME` environment variable.

To issue a certificate you can:
```
export CLOUDSDK_ACTIVE_CONFIG_NAME=default  # see the note above
acme.sh --issue --dns dns_gcloud -d example.com -d '*.example.com'
```

`dns_gcloud` also supports [DNS alias mode](https://github.com/Neilpang/acme.sh/wiki/DNS-alias-mode).

## 50. Use ConoHa API

First you need to login to your ConoHa account to get your API credentials.

```
export CONOHA_Username="xxxxxx"
export CONOHA_Password="xxxxxx"
export CONOHA_TenantId="xxxxxx"
export CONOHA_IdentityServiceApi="https://identity.xxxx.conoha.io/v2.0"
```

To issue a cert:
```
acme.sh --issue --dns dns_conoha -d example.com -d www.example.com
```

The `CONOHA_Username`, `CONOHA_Password`, `CONOHA_TenantId` and `CONOHA_IdentityServiceApi` will be saved in `~/.acme.sh/account.conf` and will be reused when needed.

## 51. Use netcup DNS API to automatically issue cert

First you need to login in your CCP account to get your API Key and API Password.
```
export NC_Apikey="<Apikey>"
export NC_Apipw="<Apipassword>"
export NC_CID="<Customernumber>"
```

Now, let's issue a cert:
```
acme.sh --issue --dns dns_netcup -d example.com -d www.example.com
```

The `NC_Apikey`,`NC_Apipw` and `NC_CID` will be saved in `~/.acme.sh/account.conf` and will be reused when needed.
## 52. Use GratisDNS.dk

GratisDNS.dk (https://gratisdns.dk/) does not provide an API to update DNS records (other than IPv4 and IPv6
dynamic DNS addresses).  The acme.sh plugin therefore retrieves and updates domain TXT records by logging
into the GratisDNS website to read the HTML and posting updates as HTTP.  The plugin needs to know your
userid and password for the GratisDNS website.

```sh
export GDNSDK_Username="..."
export GDNSDK_Password="..."
```
The username and password will be saved in `~/.acme.sh/account.conf` and will be reused when needed.


Now you can issue a certificate.

Note: It usually takes a few minutes (usually 3-4 minutes) before the changes propagates to gratisdns.dk nameservers (ns3.gratisdns.dk often are slow),
and in rare cases I have seen over 5 minutes before google DNS catches it. Therefor a DNS sleep of at least 300 seconds are recommended-

```sh
acme.sh --issue --dns dns_gdnsdk --dnssleep 300 -d example.com -d *.example.com
```

## 53. Use Namecheap

You will need your namecheap username, API KEY (https://www.namecheap.com/support/api/intro.aspx) and your external IP address (or an URL to get it), this IP will need to be whitelisted at Namecheap.
Due to Namecheap's API limitation all the records of your domain will be read and re applied, make sure to have a backup of your records you could apply if any issue would arise.

```sh
export NAMECHEAP_USERNAME="..."
export NAMECHEAP_API_KEY="..."
export NAMECHEAP_SOURCEIP="..."
```

NAMECHEAP_SOURCEIP can either be an IP address or an URL to provide it (e.g. https://ifconfig.co/ip).

The username and password will be saved in `~/.acme.sh/account.conf` and will be reused when needed.

Now you can issue a certificate.

```sh
acme.sh --issue --dns dns_namecheap -d example.com -d *.example.com
```

If you find any bugs of namecheap dns API, please report here: https://github.com/Neilpang/acme.sh/issues/2107

## 54. Use MyDNS.JP API

First, register to MyDNS.JP and get MasterID and Password.

```
export MYDNSJP_MasterID=MasterID
export MYDNSJP_Password=Password
```

To issue a certificate:

```
acme.sh --issue --dns dns_mydnsjp -d example.com -d www.example.com
```
The `MYDNSJP_MasterID` and `MYDNSJP_Password` will be saved in `~/.acme.sh/account.conf` and will be reused when needed.

## 55. Use hosting.de API

Create an API key in your hosting.de account here: https://secure.hosting.de

The key needs the following rights:
- DNS_ZONES_EDIT
- DNS_ZONES_LIST

Set your API Key and endpoint:

```
export HOSTINGDE_APIKEY='xxx'
export HOSTINGDE_ENDPOINT='https://secure.hosting.de'
```

The plugin can also be used for the http.net API. http.net customers have to set endpoint to https://partner.http.net.

Ok, let's issue a cert now:
```
acme.sh --issue --dns dns_hostingde -d example.com -d *.example.com
```

The hosting.de API key and endpoint will be saved in `~/.acme.sh/account.conf` and will be reused when needed.

## 56. Use Neodigit.net API

```
export NEODIGIT_API_TOKEN="eXJxTkdUVUZmcHQ3QWJackQ4ZGlMejRDSklRYmo5VG5zcFFKK2thYnE0WnVnNnMy"
```

Ok, let's issue a cert now:
```
acme.sh --issue --dns dns_neodigit -d example.com -d www.example.com
```

Neodigit API Token will be saved in `~/.acme.sh/account.conf` and will be used when needed.

## 57. Use Exoscale API

Create an API key and secret key in the Exoscale account section

Set your API and secret key:

```
export EXOSCALE_API_KEY='xxx'
export EXOSCALE_SECRET_KEY='xxx'
```

Now, let's issue a cert:
```
acme.sh --issue --dns dns_exoscale -d example.com -d www.example.com
```

The `EXOSCALE_API_KEY` and `EXOSCALE_SECRET_KEY` will be saved in `~/.acme.sh/account.conf` and will be reused when needed.

## 58. Using PointHQ API to issue certs

Log into [PointHQ account management](https://app.pointhq.com/profile) and copy the API key from the page there.

```export PointHQ_Key="apikeystringgoeshere"
exportPointHQ_Email="accountemail@yourdomain.com"
```

You can then issue certs by using:
```acme.sh --issue --dns dns_pointhq -d example.com -d www.example.com
```

## 59. Use Active24 API

Create an API token in the Active24 account section, documentation on https://faq.active24.com/cz/790131-REST-API-rozhran%C3%AD.

Set your API token:

```
export ACTIVE24_Token='xxx'
```

Now, let's issue a cert, set `dnssleep` for propagation new DNS record:
```
acme.sh --issue --dns dns_active24 -d example.com -d www.example.com --dnssleep 1000
```

The `ACTIVE24_Token` will be saved in `~/.acme.sh/account.conf` and will be reused when needed.

## 60. Use do.de API

Create an API token in your do.de account ([Create token here](https://www.do.de/account/letsencrypt/) | [Documentation](https://www.do.de/wiki/LetsEncrypt_-_Entwickler)).

Set your API token:
```
export DO_LETOKEN='FmD408PdqT1E269gUK57'
```

To issue a certificate run:
```
acme.sh --issue --dns dns_doapi -d example.com -d *.example.com
```

The API token will be saved in `~/.acme.sh/account.conf` and will be reused when needed.

## 61. Use Nexcess API

First, you'll need to login to the [Nexcess.net Client Portal](https://portal.nexcess.net) and [generate a new API token](https://portal.nexcess.net/api-token).

Once you have a token, set it in your systems environment:

```
export NW_API_TOKEN="YOUR_TOKEN_HERE"
export NW_API_ENDPOINT="https://portal.nexcess.net"
```

Finally, we'll issue the certificate: (Nexcess DNS publishes at max every 15 minutes, we recommend setting a 900 second `--dnssleep`)

```
acme.sh --issue --dns dns_nw -d example.com --dnssleep 900
```

The `NW_API_TOKEN` and `NW_API_ENDPOINT` will be saved in `~/.acme.sh/account.conf` and will be reused when needed.

If you find any bugs of Nexcess dns API, please report here: https://github.com/Neilpang/acme.sh/issues/2088

## 62. Use Thermo.io API

First, you'll need to login to the [Thermo.io Client Portal](https://core.thermo.io) and [generate a new API token](https://core.thermo.io/api-token).

Once you have a token, set it in your systems environment:

```
export NW_API_TOKEN="YOUR_TOKEN_HERE"
export NW_API_ENDPOINT="https://core.thermo.io"
```

Finally, we'll issue the certificate: (Thermo DNS publishes at max every 15 minutes, we recommend setting a 900 second `--dnssleep`)

```
acme.sh --issue --dns dns_nw -d example.com --dnssleep 900
```

The `NW_API_TOKEN` and `NW_API_ENDPOINT` will be saved in `~/.acme.sh/account.conf` and will be reused when needed.

## 63. Use Futurehosting API

First, you'll need to login to the [Futurehosting Client Portal](https://my.futurehosting.com) and [generate a new API token](https://my.futurehosting.com/api-token).

Once you have a token, set it in your systems environment:

```
export NW_API_TOKEN="YOUR_TOKEN_HERE"
export NW_API_ENDPOINT="https://my.futurehosting.com"
```

Finally, we'll issue the certificate: (Futurehosting DNS publishes at max every 15 minutes, we recommend setting a 900 second `--dnssleep`)

```
acme.sh --issue --dns dns_nw -d example.com --dnssleep 900
```

The `NW_API_TOKEN` and `NW_API_ENDPOINT` will be saved in `~/.acme.sh/account.conf` and will be reused when needed.

## 64. Use Rackspace API

Set username and API key, which is available under "My Profile & Settings"

```
export RACKSPACE_Username='username'
export RACKSPACE_Apikey='xxx'
```

Now, let's issue a cert:

```
acme.sh --issue --dns dns_rackspace -d example.com -d www.example.com
```

If you find any bugs of Rackspace dns API, please report here: https://github.com/Neilpang/acme.sh/issues/2091

## 65. Use Online API

First, you'll need to retrive your API key, which is available under https://console.online.net/en/api/access

```
export ONLINE_API_KEY='xxx'
```

To issue a cert run:

```
acme.sh --issue --dns dns_online -d example.com -d www.example.com
```

`ONLINE_API_KEY` will be saved in `~/.acme.sh/account.conf` and will be reused when needed.

If you find any bugs of online.net dns API, please report here: https://github.com/Neilpang/acme.sh/issues/2093

## 66. Use MyDevil.net

Make sure that you can execute own binaries:

```sh
devil binexec on
```

Install acme.sh, or simply `git clone` it into some directory on your MyDevil host account (in which case you should link to it from your `~/bin` directory).

If you're not using private IP and depend on default IP provided by host, you may want to edit `crontab` too, and make sure that `acme.sh --cron` is run also after reboot (you can find out how to do that on their wiki pages).

To issue a new certificate, run:

```sh
acme.sh --issue --dns dns_mydevil -d example.com -d *.example.com
```

After certificate is ready, you can install it with [deploy command](https://github.com/Neilpang/acme.sh/wiki/deployhooks#14-deploy-your-cert-on-mydevilnet).

## 67. Use Core-Networks API to automatically issue cert

First you need to login to your Core-Networks account to to set up an API-User.
Then export username and password to use these credentials.

```
export CN_User="user"
export CN_Password="passowrd"
```

Ok, let's issue a cert now:
```
acme.sh --issue --dns dns_cn -d example.com -d www.example.com
```

The `CN_User` and `CN_Password` will be saved in `~/.acme.sh/account.conf` and will be reused when needed.

If you find any bugs of core-networks.de dns API, please report here: https://github.com/Neilpang/acme.sh/issues/2142

## 68. Use NederHost API

Create an API token in Mijn NederHost.

Set your API key:
```
export NederHost_Key='xxx'
```

To issue a certificate run:
```
acme.sh --issue --dns dns_nederhost -d example.com -d *.example.com
```

If you find any bugs of NederHost dns API, please report here: https://github.com/Neilpang/acme.sh/issues/2089

## 69. Use Zone.ee DNS API

First, you'll need to retrive your API key. Estonian insructions https://help.zone.eu/kb/zoneid-api-v2/

```
export ZONE_Username=yourusername
export ZONE_Key=keygoeshere
```

To issue a cert run:

```
acme.sh --issue -d example.com -d www.example.com --dns dns_zone
```

`ZONE_Username` and `ZONE_Key` will be saved in `~/.acme.sh/account.conf` and will be reused when needed.


If you find any bugs of zone.eu (zone.ee) dns API, please report here: https://github.com/Neilpang/acme.sh/issues/2146



## 70. Use UltraDNS API

UltraDNS is a paid for service that provides DNS, as well as Web and Mail forwarding (as well as reporting, auditing, and advanced tools).

More information can be found here: https://www.security.neustar/lp/ultra20/index.html

The REST API documentation for this service is found here: https://portal.ultradns.com/static/docs/REST-API_User_Guide.pdf

Set your UltraDNS User name, and password; these would be the same you would use here:

https://portal.ultradns.com/ - or if you create an API only user, that username and password would be better utilized.

```
export ULTRA_USR="abcd"
export ULTRA_PWD="efgh"

To issue a cert run:

acme.sh --issue --dns dns_ultra -d example.com -d www.example.com
```

`ULTRA_USR` and `ULTRA_PWD` will be saved in `~/.acme.sh/account.conf` and will be resued when needed.

## 71. Use deSEC.io

Sign up for dynDNS at https://desec.io first.

Set your API token (password) and domain (username) from your email sent by desec.io
```
export DEDYN_TOKEN=d41d8cd98f00b204e9800998ecf8427e
export DEDYN_NAME=foobar.dedyn.io
```
To issue a certificate run:
```
acme.sh --issue --dns dns_desec -d foobar.dedyn.io -d *.foobar.dedyn.io
```

If you find any bugs of deSEC.io  API, please report here: https://github.com/Neilpang/acme.sh/issues/2180


## 72. Use OpenProvider API

First, you need to enable API access and retrieve your password hash on https://rcp.openprovider.eu/account/dashboard.php

```
export OPENPROVIDER_USER='username'
export OPENPROVIDER_PASSWORDHASH='xxx'

acme.sh --issue --dns dns_openprovider -d example.com -d www.example.com
```

`OPENPROVIDER_USER` and `OPENPROVIDER_PASSWORDHASH` will be saved in `~/.acme.sh/account.conf` and will be reused when needed.

If you find any bugs of OpenProvider dns API, please report here: https://github.com/Neilpang/acme.sh/issues/2104

## 73. Use MaraDNS API

Make sure you've configured MaraDNS properly and setup a zone file for your domain. See [`csv2(5)`](https://manpages.debian.org/stretch/maradns/csv2.5.en.html).

Set the path to your zone file, and path to duende's pid file (see, [`duende(8)`](https://manpages.debian.org/stretch/duende/duende.8.en.html) or `ps -C duende o pid,cmd`).
The pid file is used to ask duende to reload the configuration automatically after DNS records are added.
```
export MARA_ZONE_FILE="/etc/maradns/db.domain.com"
export MARA_DUENDE_PID_PATH="/run/maradns/etc_maradns_mararc.pid"
```

Ensure that the acme.sh process has write access to the zone file and read access to the pid file.

Issuing a certificate:
```
acme.sh --issue --dns dns_maradns -d example.com -d '*.example.com'
```

`MARA_ZONE_FILE` and `MARA_DUENDE_PID_PATH` will be saved in `~/.acme.sh/account.conf` and will be reused when needed.


If you find any bugs of MaraDNS DNS API, please report here: https://github.com/Neilpang/acme.sh/issues/2072


## 74. Use DDNSS.de API

First create an account at https://ddnss.de. After that create a new host record.
In the defenition for the host make sure to set the checkbox for "Wildcard" and for "TXT".

Note your Api Key displayed at ddnss.de and export in DDNSS_Token variable
```
export DDNSS_Token="e4155767few8erwf59353633tz342ce85werddc99432"
```
Note: You will only have one token, even if you have multiple Hosts with DDNSS under your account.

After that you can issue a new certificate:
```
acme.sh --issue --dns dns_ddnss -d example.com
```

If you find any bugs of ddnss.de API, please report here: https://github.com/Neilpang/acme.sh/issues/2230

## 75. Use NLnetLabs NSD

You need to export two variables. Your zonefile which the script will automatically edit:
```
export Nsd_ZoneFile="/etc/nsd/zones/example.com.zone"
```
And something that calls the nsd-control reload command, either via a script:
```
export Nsd_Command="/usr/local/bin/sign-and-update.sh example.com"
```
or directly:
```
export Nsd_Command="sudo nsd-control reload example.com"
```
The variables are saved per-domain, not per-account.

To issue a new certificate, run:
```
acme.sh --issue --dns dns_nsd -d example.com -d *.example.com
```

If you find any bugs of NLnetLabs NSD dns API, please report here: https://github.com/Neilpang/acme.sh/issues/2245

## 76. Use Schlundtech

[Schlundtech](https://www.schlundtech.de/) offers an [xml api](https://www.schlundtech.de/services/xml-gateway/)  with your standard login credentials, set them like so:

```
export SCHLUNDTECH_USER="yourusername"
export SCHLUNDTECH_PASSWORD="password"
```

Then you can issue your certificates with:

```
acme.sh --issue --dns dns_schlundtech -d example.com -d www.example.com
```

The `SCHLUNDTECH_USER` and `SCHLUNDTECH_PASSWORD` settings will be saved in `~/.acme.sh/account.conf` and will be reused when needed.

If you find any bugs of Schlundtech.de API, please report here: https://github.com/Neilpang/acme.sh/issues/2246

## 77. Use your one.com credentials as you would login into the control panel.

```
export ONECOM_User="sdfsdfsdfljlbjkljlkjsdfoiwje"
export ONECOM_Password="xxxx@sss.com"
```

Ok, let's issue a cert now:
```
acme.sh --issue --dns dns_one -d example.com -d www.example.com
```

Note:
It's no longer possible to add TXT Records with the Name "_acme-challenge" to the base Domain.  
To override the fallback value, you must use a CNAME and proxy it.  
For example:  
CNAME _acme-challenge.yourdomain.com => proxy_acme-challenge.yourdomain.com  
The TXT Records have to be created on proxy_acme-challenge.yourdomain.com  
Since the default CNAME TTL is 3600 seconds, it is recommended to leave the CNAME record.  
But if you would like to use the build-in SSL (for your Web-Site etc.) from one.com, you have to delete the Record. 

You can set "ONECOM_KeepCnameProxy" to keep the CNAME record.

```
export ONECOM_KeepCnameProxy=1
```

By default the CNAME record will be removed.

The `ONECOM_User`,`ONECOM_Password` and `ONECOM_KeepCnameProxy` will be saved in `~/.acme.sh/account.conf` and will be reused when needed.

If you find any bugs of one.com dns API, please report here: https://github.com/Neilpang/acme.sh/issues/2103

## 78. Use acmeproxy DNS API

[Acmeproxy](https://github.com/mdbraber/acmeproxy/) can be used to as a single host in your network to request certificates through a DNS API. Clients can connect with one single host (the acmeproxy) so you don't need to store your DNS API credentials on every single host that wants to request a certificate.

```
export ACMEPROXY_ENDPOINT="https://acmeproxy.yourhost.com:9096"
export ACMEPROXY_USERNAME="username"
export ACMEPROXY_PASSWORD="password"
```

Then you can issue your certificates with:

```
acme.sh --issue --dns dns_acmeproxy -d example.com -d www.example.com
```

The `ACMEPROXY_ENDPOINT`, `ACMEPROXY_USERNAME` and `ACMEPROXY_PASSWORD` settings will be saved in `~/.acme.sh/account.conf` and will be reused when needed.

If you find any bugs of acmeproxy DNS API, please report here: https://github.com/Neilpang/acme.sh/issues/2251

## 79. Use internetbs.net API

Create an API token in your internetbs.net account.

Set your API token:
```
export INTERNETBS_API_KEY="..."
export INTERNETBS_API_PASSWORD="..."
```

To issue a certificate run:
```
acme.sh --issue --dns dns_internetbs -d example.com -d www.example.com
```

The `INTERNETBS_API_KEY` and `INTERNETBS_API_PASSWORD` will be saved in `~/.acme.sh/account.conf` and will be reused when needed.

If you find any bugs of internetbs.net API, please report here: https://github.com/Neilpang/acme.sh/issues/2261

## 80. Use durabledns.com API

Create an API token in your durabledns.com account.

Set your API token:
```
export DD_API_User="..."
export DD_API_Key="..."
```

To issue a certificate run:
```
acme.sh --issue --dns dns_durabledns -d example.com -d '*.example.com'
```

The `DD_API_User` and `DD_API_Key` will be saved in `~/.acme.sh/account.conf` and will be reused when needed.

If you find any bugs of durabledns.com API, please report here: https://github.com/Neilpang/acme.sh/issues/2281


## 81. Use reg.ru API

Set your API credentials:
```
export REGRU_API_Username='test'
export REGRU_API_Password='test'
```

To issue a certificate run:
```
acme.sh --issue --dns dns_regru -d 'example.com' -d '*.example.com'
```

The `REGRU_API_Username` and `REGRU_API_Password` will be saved in `~/.acme.sh/account.conf` and will be reused when needed.

If you find any bugs of reg.ru API, please report here: [Issue #2336](../issues/2336)

RU:

Установите свои учетные данные API:

В [Настройки API](https://www.reg.ru/user/account/#/settings/api/) для авторизации устанавливаем пароль в настройках "Альтернативный пароль", и добавляем IP в "Диапазоны IP-адресов" для избежание ошибки
```
response='{
   "charset" : "utf-8",
   "error_code" : "ACCESS_DENIED_FROM_IP",
   "error_params" : {
      "command_name" : "service/get_list"
   },
   "error_text" : "Access to API from this IP denied",
   "messagestore" : null,
   "result" : "error"
}'
```
Диапазоны IP-адресов
acme-v02.api.letsencrypt.org - `172.65.32.248` для получения SSL и api.reg.ru - `194.58.116.30` для тхт записи _acme-challenge

Для авторизатции в API выполните:
```
export REGRU_API_Username='водим свой логин для входа на REG.RU'
export REGRU_API_Password='водим пароль каторый настроили в настройках API для авторизации'
```
Для получения сертификата выполните:
```
acme.sh --issue --dns dns_regru -d 'example.com' -d '*.example.com'
```
Настройки для авторизации `REGRU_API_Username` и `REGRU_API_Password` будут сохранены в `~/.acme.sh/account.conf` и будут использоваться повторно при необходимости из конфига acme.

Если вы обнаружите какие-либо ошибки в API reg.ru, сообщите об этом здесь: [Issue #2336](../issues/2336)

## 82. Use Vultr DNS API to automatically issue cert

You'll need an API key for your Vultr account which you can find [under the Account settings](https://my.vultr.com/settings/#settingsapi) and you'll want to ensure the API key is allowed for any IPs you might be using acme.sh with.

Vultr supports creating sub-accounts with limited permissions, and it's a good idea to create a sub-account with only the 'Manage DNS' permission and use an API key from that sub-account.

```sh
export VULTR_API_KEY="<Your API key>"
```

To issue a cert:

```sh
acme.sh --issue --dns dns_vultr -d example.com -d www.example.com
```

The `VULTR_API_KEY` will be saved in `~/.acme.sh/account.conf` and will be reused when needed.

If you find any bugs of Vultr API, please report here: [Issue #2374](../../issues/2374)



## 83. Use jdcloud.com DNS API to automatically issue cert

支持京东云 jdcloud.com 的免费dns服务.  请先登陆控制台获取 api key id 和 api key secret:

https://uc.jdcloud.com/account/accesskey

```sh
export JD_ACCESS_KEY_ID="sdfsdfsdfljlbjkljlkjsdfoiwje"
export JD_ACCESS_KEY_SECRET="xxxxxxx"
```

然后生成证书:

```sh
acme.sh --issue --dns dns_jd  -d example.com -d www.example.com
```

`JD_ACCESS_KEY_ID` 和 `JD_ACCESS_KEY_SECRET` 会自动保存在这里 `~/.acme.sh/account.conf`, 下次再生成证书时, 可以自动重用.

高级选项:
1. 默认使用的是 `cn-north-1` 区域. 目前不需要改动, 如果需要改的话, 再生成证书之前执行:
```
export JD_REGION="cn-north-1" # 这里写你要改的区域
```

有 bug 的话可以报到这里: https://github.com/Neilpang/acme.sh/issues/2388


## 84. Use hexonet.com DNS API to automatically issue a cert

Create a role user in your Account -> Settings -> ShareAccess

Set the Access Control like bellow:
```
QueryDNSZoneRRList(dnszone=*):ALLOW
UpdateDNSZone():ALLOW
```

Remember the role id and role password.

```sh
export Hexonet_Login='username!roleId'
export Hexonet_Password="role password"
```

For example:
My user name is `neilpang`, my role id is: `testid`. So I use the following format:

```sh
export Hexonet_Login='neilpang!testid'
```


To issue a cert:

```sh
acme.sh --issue --dns dns_hexonet  -d example.com -d www.example.com
```

The `Hexonet_Login` and `Hexonet_Password` will be saved in `~/.acme.sh/account.conf` and will be reused when needed.

If you find any bugs, please report here:

https://github.com/Neilpang/acme.sh/issues/2389


## 85. Use Domeneshop DNS API to automatically issue a cert

You'll have to get a Domeneshop API key and secret (https://api.domeneshop.no/docs/).

```
export DOMENESHOP_Token="1234567890"
export DOMENESHOP_Secret="1234567890abcdefghijklmnopqrstuvw"
```

Ok, let's issue a cert now:
```
acme.sh --issue --dns dns_domeneshop -d example.com -d www.example.com
```

The `DOMENESHOP_Token` and `DOMENESHOP_Secret` will be saved in `~/.acme.sh/account.conf` and will be reused when needed.

If you find any bugs, please report here:

https://github.com/Neilpang/acme.sh/issues/2457


## 86. Use OPNsense embedded API to automatically issue cert

First you need to login to your OPNsense account and create an API Key for a user with access to the Bind service.

https://docs.opnsense.org/development/api.html

```
export OPNs_Host="opnsense.example.com"
export OPNs_Port="443"
export OPNs_Key="qocfU9RSbt8vTIBcnW8bPqCrpfAHMDvj5OzadE7Str+rbjyCyk7u6yMrSCHtBXabgDDXx/dY0POUp7ZA"
export OPNs_Token="pZEQ+3ce8dDlfBBdg3N8EpqpF5I1MhFqdxX06le6Gl8YzyQvYCfCzNaFX9O9+IOSyAs7X71fwdRiZ+Lv"
export OPNs_Api_Insecure=0
```

Ok, let's issue a cert now:
```
acme.sh --issue --dns dns_opnsense -d example.com -d www.example.com
```

The `OPNs_Host`, `OPNs_Port`, `OPNs_Key`, `OPNs_Token` and `OPNs_Api_Insecure` will be saved in `~/.acme.sh/account.conf` and will be reused when needed.

If you find any bugs, please report here:

https://github.com/Neilpang/acme.sh/issues/2480

## 87. Use the RcodeZero API to automatically issue cert

First you need to login to your RcodeZero account, enable the REST API and generate an ACME API token (only the ACME API token will work wih acme.sh. It has limited access and could only be used to add/remove challenges to the zones).

https://my.rcodezero.at/enableapi

```
export RCODE0_API_TOKEN="acme_1232342342343OEH1G1gDcKNMsN7mx9EZgSU6AX79u5KRSxWnC"
```

Ok, let's issue a cert now:
```
acme.sh --issue --dns dns_rcode0 -d example.com -d www.example.com
```

The `RCODE0_API_TOKEN` will be saved in `~/.acme.sh/account.conf` and will be reused when needed.

The RcodeZero API driver supports two addtional environment variables
```
export RCODE0_URL=https://my.rcodezero.at
```
Use a different RcodeZero API Endpoint (e.g. the RcodeZero Testsystem)
```
export RCODE0_TTL=60
```
Use a different TTL for the generated records

If you find any bugs, please report here:

https://github.com/Neilpang/acme.sh/issues/2490

## 88. Use MailinaBox

Use the MailinaBox (MIAB) Custom DNS REST API interface to MIAB DNS. You only need to set your MIAB login credentials and the fully qualified domain name of the MIAB Server. Suggest single quote over double quote to ensure characters are not interpreted by the shell - important for passwords.

```
export MIAB_Username='your_MIAB_admin_username'
export MIAB_Password='your_MIAB_admin_password'
export MIAB_Server='FQDN_of_your_MIAB_Server'
```

To issue a cert:
```
acme.sh --issue --dns dns_miab -d example.com -d www.example.com
```

The `MIAB_Username`, `MIAB_Password` and `MIAB_Server` will be saved in `~/.acme.sh/account.conf` and will be reused when needed.

If you find any bugs, please report here:

https://github.com/Neilpang/acme.sh/issues/2550




## 89. Use nic.ru DNS
You need to login to nic.ru account and register your application [here](https://www.nic.ru/manager/oauth.cgi?step=oauth.app_register).

You need to define the following environment variables befor issuing a cert: 
* `NIC_Username` - login for site `nic.ru` in form `000000/NIC-D`
* `NIC_Password` - password for site `nic.ru`. It may be administrative or technical password ([details](https://www.nic.ru/help/use-of-administrative-and-technical-passwords-according-to-the-agreement_6148.html))
* `NIC_ClientID` - your application identifier ([details](https://www.nic.ru/help/oauth-server_5809.html))
* `NIC_ClientSecret` - your application secret
* _`NIC_Token` is base64 encoded string `<client_id>:<client_secret>`. **This variable is deprecated**. It is used for backward compatibility. If NIC_ClientID and NIC_ClientSecret are not defined, then they are calculated using old NIC_Token variable._

```bash
export NIC_Username='000000/NIC-D'
export NIC_Password='xxxxxxxx'
export NIC_ClientID='xxxxxxxx'
export NIC_ClientSecret='xxxxxxxx'
```

To issue a cert:
```bash
acme.sh --issue --dns dns_nic -d domain.com -d www.domain.com
```
The NIC_Username, NIC_Password, NIC_ClientID and NIC_ClientSecret will be saved in ~/.acme.sh/account.conf and will be reused when needed.

If you find any bugs, please report here:

https://github.com/Neilpang/acme.sh/issues/2547

Docs: 
* https://www.nic.ru/help/upload/file/API_DNS-hosting-en.pdf
* https://www.nic.ru/help/oauth-server_5809.html

## 90. Use Leaseweb.com domain API to automatically issue cert

First you need to login to your Leaseweb account to get your API Key.

```
export LSW_Key="safas-3fs3sd-34sdf-safss"
```

Ok, let's issue a cert now:
```
acme.sh --issue --dns dns_leaseweb -d example.com -d www.example.com
```

The `LSW_Key` will be saved in `~/.acme.sh/account.conf` and will be reused when needed.

If you find any bugs, please report here: https://github.com/Neilpang/acme.sh/issues/2558


## 91. Use variomedia.de domain API to automatically issue cert

First you need to obtain your API Key from variomedia's customer support.

```
export VARIOMEDIA_API_TOKEN="sdfsdfsdfljlbjkljlkjsdfoiwjesdfsdfsdfljlbjkljlkjsdfoiwje"
```

Ok, let's issue a cert now:
```
acme.sh --issue --dns dns_variomedia -d example.com -d www.example.com
```

The `VARIOMEDIA_API_TOKEN` will be saved in `~/.acme.sh/account.conf` and will be reused when needed.

If you find any bugs, please report here: https://github.com/Neilpang/acme.sh/issues/2564


## 92. Use Plesk XML API to automatically issue cert

Before using the module, you must set your Plesk user name and password, and the address of your Plesk XML API (sometimes called a URI, URL or web link). The URI usually looks similar to this:

```
https://address-of-my-plesk-server.net:8443/enterprise/control/agent.php
```

All commands are CASE SENSITIVE:

```
export pleskxml_uri="address of my Plesk server's API"
export pleskxml_user="my plesk username"
export pleskxml_pass="my plesk password"
```

Ok, let's issue a cert now:
```
acme.sh --issue --dns dns_pleskxml -d example.com -d www.example.com
```

The `pleskxml_uri`, `pleskxml_user` and `pleskxml_pass` will be saved in `~/.acme.sh/account.conf` and reused when needed.

If you find any bugs, please report here: https://github.com/Neilpang/acme.sh/issues/2577

## 93. Use PDNS Manager API

[PDNS Manager](https://pdnsmanager.org/) is a web frontend for [Power DNS](https://www.powerdns.com/).

This script uses PDNS Manager API and its "[Update via GET request](https://pdnsmanager.org/documentation/api/)" method. So only single record update possible and no wildcards, for now.

```
export PDNS_MANAGER_URL=https://mypdnsmanagerurl.nx
export PDNS_MANAGER_RECORDID=<record id>
export PDNS_MANAGER_PASSWORD=<record password>
```

* Add your domain to PDNS Manager.
* [Create a password](https://pdnsmanager.org/documentation/api/) for your record.

Then issue a new certificate:

```
acme.sh --issue -d example.com --dns dns_pdnsmanager
```

## 94. Use Misaka.io domain API

Get your API token at https://console.misaka.io/settings

```
export Misaka_Key="sdfsdfsdfljlbjkljlkjsdfoiwje"
```

To issue a cert:
```
acme.sh --issue --dns dns_misaka -d example.com -d www.example.com
```

## 95. Use easyDNS.net API to automatically issue a cert
You need to sign up for beta API access [here](http://docs.sandbox.rest.easydns.net/beta_signup.php).
Docs: http://sandbox.rest.easydns.net:3000/

```
export EASYDNS_Key="xxxxxxxxxxxxxxxxx.xxxxxxxx"
export EASYDNS_Token="xxxxxxxxxxxxxxx.xxxxxxxx"
```

Ok, let's issue a cert now:
```
acme.sh --issue --dns dns_easydns -d example.com -d www.example.com
```
The `EASYDNS_Key` and `EASYDNS_Token` will be saved in `~/.acme.sh/account.conf` and will be reused when needed.

If you find any bugs, please report here:

https://github.com/Neilpang/acme.sh/issues/2647

## 96. Use CloudDNS API to automatically issue a cert
Docs: https://github.com/vshosting/clouddns

```
export CLOUDDNS_EMAIL="email@example.com"
export CLOUDDNS_PASSWORD="xxxxxxxx"
export CLOUDDNS_CLIENT_ID="xxxxxxxxxxxxxxxxxxxx"
```

Ok, let's issue a cert now:
```
acme.sh --issue --dns dns_clouddns -d example.com -d www.example.com
```
The `CLOUDDNS_EMAIL`, `CLOUDDNS_PASSWORD` and `CLOUDDNS_CLIENT_ID` will be saved in `~/.acme.sh/account.conf` and will be reused when needed.

If you find any bugs, please report here:

https://github.com/Neilpang/acme.sh/issues/2699

## 97. Use dynv6 API to automatically issue a cert
This uses the [dynv6 SSH API](https://dynv6.com/docs/apis) to issue the certificate. You will need a ssh key to authenticate. You can specify your own key with `export KEY="path/to/keyfile"` or if no key is specified one will be created for you which you will have to add [here](https://dynv6.com/keys). In both cases the path to the keyfile will be saved for reuse. 

Alternatively you can use the [HTTP REST API](https://dynv6.github.io/api-spec/). For this you will need a HTTP Token, which you can generate from the [dynv6 website](https://dynv6.com/keys). Use it with `export DYNV6_TOKEN="value"`.

If both a SSH Key and a HTTP Token are specified the REST API will be used.

To issue a cert use:
```
acme.sh --issue --dns dns_dynv6 -d www.example.dynv6.net
```
If you find any bugs, please report here:  
https://github.com/acmesh-official/acme.sh/issues/2702

## 98. Use All-Inkl.com domain API to automatically issue cert

You need your login credentials for All-Inkl (https://kas.all-inkl.com).
```
export KAS_Login="....."
export KAS_Authtype="sha1"
export KAS_Authdata="....."
```
Now you are able to issue a cert:
```
acme.sh --issue --dns dns_kas -d example.com -d www.example.com
```
The `KAS_Login`, `KAS_Authtype` and `KAS_Authdata` will be saved in `~/.acme.sh/account.conf` and will be reused when needed.

## 99. Use Constellix domain API

Get your API credentials at https://manage.constellix.com/users

```
export CONSTELLIX_Key="XXX"
export CONSTELLIX_Secret="XXX"
```

To issue a cert:
```
acme.sh --issue --dns dns_constellix -d example.com -d www.example.com
```

The `CONSTELLIX_Key` and `CONSTELLIX_Secret` will be saved in `~/.acme.sh/account.conf` and will be reused when needed.

## 100. Use Namemaster domain API

Get your API credentials at https://namemaster.de DNS/API 

```
export NM_user="XXX"
export NM_sha256="XXX"
```

To issue a cert:
```
acme.sh --issue --dns dns_nm -d example.com -d www.example.com
```

The `NM_user` and `NM_sha256` will be saved in `~/.acme.sh/account.conf` and will be reused when needed.

## 101. Use HostingUkraine domain API

How get your API credentials: https://api.adm.tools/osnovnie-polozheniya/dostup-k-api/

```
# Your login:
HostingUkraine_Login="XXX"
# Your api token:
HostingUkraine_Token="XXX"
```

To issue a cert:
```
./acme.sh --issue --dns dns_hostingukraine -d yourdomain.com -d www.yourdomain.com
```
To issue a wildcard cert: 
```
./acme.sh --issue --dns dns_hostingukraine -d yourdomain.com -d '*.yourdomain.com' 
```

The `HostingUkraine_Login` and `HostingUkraine_Token` will be saved in `~/.acme.sh/account.conf` and will be reused when needed.

## 102. Use ArvanCloud domain API

Get your API token at https://npanel.arvancloud.com/profile/api-keys

```
export Arvan_Token="fsdasdfsdfljlbjkljlkjsdfoiwje"
```

To issue a cert:
```
acme.sh --issue --dns dns_arvan -d example.com -d www.example.com
```

The `Arvan_Token` will be saved in `~/.acme.sh/account.conf` and will be reused when needed.

## 103. Use Joker.com domain API

You must activate Dynamic DNS in Joker.com DNS configuration first. Username and password below refer to Dynamic DNS authentication, not your Joker.com login credentials. See https://joker.com/faq/content/11/427/en/what-is-dynamic-dns-dyndns.html.

**NOTE:** This script does not support wildcard certificates, because Joker.com API does not support adding two TXT records with the same subdomain. Adding the second record will overwrite the first one. See https://joker.com/faq/content/6/496/en/let_s-encrypt-support.html:
> ... this request will replace all TXT records for the specified label by the provided content...

Report bugs to https://github.com/acmesh-official/acme.sh/issues/2840

```
export JOKER_USERNAME="xxxx"
export JOKER_PASSWORD="xxxx"
```

To issue a cert:
```
acme.sh --issue --dns dns_joker -d example.com
```

The `JOKER_USERNAME`  and `JOKER_PASSWORD` will be saved in `~/.acme.sh/account.conf` and will be reused when needed.

## 104. Use 1984Hosting domain API

[1984Hosting](https://1984hosting.com/) does not provide an API to update DNS records 
(other than IPv4 and IPv6 dynamic DNS addresses). The `acme.sh` plugin therefore 
retrieves and updates domain TXT records by logging into the 1984Hosting 
website to read the HTML and posting updates as HTTP. The plugin needs to 
know your username and password for the 1984Hosting website.

```sh
export One984HOSTING_Username=<your_username>
export One984HOSTING_Password=<your_password>
```

You need only provide this the first time you run the `acme.sh` client with 1984Hosting 
validation and then again whenever you change your password at the 1984Hosting site. 
The `acme.sh` 1984Hosting plugin does not store your username or password, 
but rather saves an authentication token returned by 1984Hosting 
in `~/.acme.sh/account.conf` and reuses it when needed.

Ok, let's issue a cert now:

```sh
acme.sh --issue --dns dns_1984hosting -d example.com -d *.example.com
```

If you have any issues with 1984Hosting DNS API please report them [here](https://github.com/Neilpang/acme.sh/issues/2851).

## 105. Use Aruba domain API

Get your api token following instruction here at https://admin.arubabusiness.it/DashBoard/WebApiGuide.aspx
```
export ARUBA_TK="sdfsdfsdfljlbjkljlkjsdfoiwje" #ARUBA API Token
export ARUBA_AK="xxxxxxxxxxxxx" #ARUBA Username
export ARUBA_AS="xxxxxxxxxxxxx" #ARUBA Password
```
Ok, let's issue a cert now:
```
acme.sh --issue --dns dns_aruba -d example.com -d www.example.com
```

If you find any bugs, please report here:
https://github.com/JTrotta/acme.sh/issues


## 106. Use TransIP domain API:

### Using the access token
First you need to login to your TransIP account to get your [private key](https://www.transip.nl/cp/account/api/).
```
export TRANSIP_Username="MyUserName"
export TRANSIP_Key_File="private_key"
```
Note: TransIP is rather slow, so adding a --dnssleep of 300 might be adviced.
Note 2: if the DNS fails with something like
```
Please refer to https://curl.haxx.se/libcurl/c/libcurl-errors.html for error code: 60
== Info: SSL certificate problem: unable to get local issuer certificate
```
Than maybe the root CA of TransIP is NOT in your cacerts.
You can check this manually with
```
curl -vvI https://api.transip.nl
```
Currently, the root CA of TransIP is COMODO_RSA_Certification_Authority.crt
you can add this trusted root CA with
```
--ca-bundle COMODO_RSA_Certification_Authority.crt
```

This script will create a new access token with a default lifetime of 30 minutes. Note that these tokens are by default IP-whitelisted and will not work if your IP is not whitelisted in the Transip control panel.

If you find any bugs, please report here:
https://github.com/mdbraber/acme.sh/issues


## 107. Use dyndnsfree.de API

```
export df_user="XXX"
export df_password="XXX"
```

To issue a cert:
```
acme.sh --issue --dns dns_df -d example.com -d www.example.com
```

The `df_user` and `df_password` will be saved in `~/.acme.sh/account.conf` and will be reused when needed.

## 108. Use Njalla API

```
export NJALLA_Token="XXX"
```

To issue a cert:
```
acme.sh --issue --dns dns_njalla -d example.com -d www.example.com
```

Please report any bugs here: https://github.com/acmesh-official/acme.sh/issues/2913


## 109. Use Vercel API

Obtain an account token from https://vercel.com/account/tokens.

```
export VERCEL_TOKEN="sdfsdfsdfljlbjkljlkjsdfoiwje"
```

To issue a cert:
```
acme.sh --issue --dns dns_vercel -d example.com -d www.example.com
```


## 110. Use Hetzner DNS API

First you need to create/obtain API tokens on your [Hetzner DNS console](https://dns.hetzner.com/settings/api-token).

```sh
export HETZNER_Token="somelongrandomstring"
```

Ok, let's issue a cert now:
```sh
acme.sh --issue --dns dns_hetzner -d example.com -d www.example.com
```

The `HETZNER_Token` settings will be saved in `[acme.sh-config-home-path]/account.conf`
 and will be reused when needed.
The domain(s) zone_id(s) will be saved in `CERT_HOME/[domain]/[domain].conf` 
in order to avoid multiple `get_zone_id` requests two months later. 

> If you're not already using the new "gases" name servers (hydrogen, oxygen and helium)
> don't forget to change the domain's whois ns section to them and wait about 24-48 Hours.
> See [Hetzner wiki](https://wiki.hetzner.de/index.php/What_has_changed).

## 111. Use kapper.net DNS API
Contact kapper.net support via support@kapper.net to get your kapper.net DNS Panel API Key and Secret.

For initialzation call following in commandline
```
export KAPPERNETDNS_Key="yourKAPPERNETapikey"
export KAPPERNETDNS_Secret="yourKAPPERNETapisecret"
```

You can start the acme.sh with following parameters for testing

```
acme.sh --issue --dns dns_kappernet -d <example.com> 

or for a wildcertificate

acme.sh --issue --dns dns_kappernet -d <example.com> -d *.<example.com> 

```
Please replace "<example.com>" with the name of the domain you wish to create a certificate for.

After the test you can replace your kapper.net DNS Panel API Key and Secret, it is stored in ~/.acme.sh/account.conf.

For repeated calls use
```
acme.sh --issue --dns dns_kappernet -d <example.com>
```

## 112. Use Wedos API

First create your WAPI password and add your IP address to access list at customer portal. For more info visit, [https://kb.wedos.com/en/kategorie/wapi-api-interface/](https://kb.wedos.com/en/kategorie/wapi-api-interface/)

Set variables:

```
export WEDOS_User='xxx@xxx.xx'
export WEDOS_Pass='xxx123'
or use sha1 hash of WEDOS_Pass directly
export WEDOS_Hash='xxxxxxxxxx'
```

Issue your certs:
```
acme.sh --issue --dns dns_wedos -d xxx.xx -d www.xxx.xx
```

WEDOS_User and WEDOS_Hash will be stored in `~/.acme.sh/account.conf` and will be reused when needed.

## 113. Use Shellrent API

Shellrent API offers one method to automatically issue certs.

First you need to login to your Shellrent account to get your API key.
In order to use the token, you need to authorize your IP to have access to it.
More Info on https://api.shellrent.com and https://guide.shellrent.com
```
export SH_Username="usrXXXX"
export SH_Token="sdfsdfsdfljlbjkljlkjsdfoiwje"
```

Alternatively, if the certificate only covers a single zone, you can speed up the process by specify the SH_Domain_ID directly:

```
export SH_Username="usrXXXX"
export SH_Token="sdfsdfsdfljlbjkljlkjsdfoiwje"
export SH_Domain_ID="xxxxxxxxxxxxx"
```
Let's issue a cert now:

```
acme.sh --issue --dns dns_shellrent -d example.com -d www.example.com
```
The SH_Username and SH_Token and SH_Domain_ID will be saved in ~/.acme.sh/account.conf and will be reused when needed.

## 114. Use OpenStack domain API

This provider supports [OpenStack Designate](https://docs.openstack.org/designate)
for creating DNS records.

Report any issues to https://github.com/acmesh-official/acme.sh/issues/3054

This provider requires the OpenStack Client (python-openstackclient) and
Designate client (python-designateclient) be installed and available in your path.

It also requires you use Keystone V3 credentials, which can be either password or
application credentials provided as environment variables. Any other
authentication method will not save your credentials for renewal.

You will most likely want to source your OpenStack RC file to set your environment variables:

```
. openrc.sh
```
or manually like:
```
export OS_AUTH_URL=https://keystone.example.com:5000/
export OS_USERNAME=<username>
export OS_PASSWORD=<password>
export OS_PROJECT_NAME=<project name>
export OS_PROJECT_DOMAIN_NAME=Default
export OS_USER_DOMAIN_NAME=Default
```

To issue a cert:
```
acme.sh --issue --dns dns_openstack -d example.com
```

Your OpenStack credentials will be saved to `~/.acme.sh/account.conf` and reused on renewal.

## 115. Use Netlify API

1. Generate a Personal Access Token at https://app.netlify.com/user/applications

2. Set your token for use with

```
export NETLIFY_ACCESS_TOKEN='arstdhneioqwfpgjluyzxcvbkm'
```

3.  Issue a cert with:

```
acme.sh --issue --dns dns_netlify -d example.com -d www.example.com
```

[Report a Netlify-related Bug](https://github.com/acmesh-official/acme.sh/issues/3088)

## 116. Use Akamai Edge DNS API

This provider supports the [Akamai Edge DNS](https://developer.akamai.com/api/cloud_security/edge_dns_zone_management/v2.html) API for creating DNS records.

Report any issues to https://github.com/acmesh-official/acme.sh/issues/3157

This provider requires Akamai Open Edgegrid Credentials with EdgeDNS API access authorization. To create and establish your Akamai OPEN CREDENTIALS, see the [authorization](https://developer.akamai.com/legacy/introduction/Prov_Creds.html) and [credentials](https://developer.akamai.com/legacy/introduction/Conf_Client.html) sections of the Akamai Developer Get Started guide.

The Akamai Open Edgegrid credentials must be specified as environment variables as follows:
```
export AKAMAI_CLIENT_TOKEN=<akamai edgegrid client token>
export AKAMAI_ACCESS_TOKEN=<akamai edgegrid access token>
export AKAMAI_CLIENT_SECRET=<akamai edgegrid client secret>
export AKAMAI_HOST=<akamai edgegrid api host>
```

To issue a cert:
```
acme.sh --issue --dns dns_edgedns -d example.com
```

Your Akamai Edgegrid credentials will be saved to `~/.acme.sh/account.conf` and reused on renewal.

## 117. Use WEDOS DNS API
WEDOS DNS provider comes from Czech Republic. DNS API implementation for WEDOS require your WEDOS's account to allow WAPI interface. You have to login to WEDOS administration, in setting allow WAPI interface (in days when this manual were written it was for free completelly). Configure WAPI interface to XML interface and register the IP addresses (IPv4 and IPv6) of the server where you plan to use acme.sh. That is from the manual side.

By doing this setting you should have WEDOS web account username and configured WAPI password. This must be configured to your acme.sh account in the first execution of acme.sh script. To save it to `~/.acme.sh/account.conf` (and for subsequent acme.sh executions) just execute following before first execution of acme.sh script.
```
export WEDOS_Username=<your user name to login to wedos web account>
export WEDOS_Wapipass=<your WAPI passwords you setup using wedos web pages>
```

Then you can issue a certificates:
```
acme.sh --issue --dns dns_wedos -d "*.example.com" -d "examle.com"
```

If you face any bug, please use this [page](https://github.com/acmesh-official/acme.sh/issues/3166) to report it. But before reporting run the acme.sh with `--debug 2` switch and append full acme.sh output to the issue report. 

Enjoy it.

## 118. Use Websupport DNS API
Obtain an api key and secret from https://admin.websupport.sk/en/auth/apiKey
```
export WS_ApiKey="xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
export WS_ApiSecret="xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
```

To issue a cert:
```
acme.sh --issue --dns dns_websupport -d "*.example.com" -d "examle.com"
```

Report any issues to https://github.com/trgosk/acme.sh/issues

## 119. Use infomaniak.com API

Infomaniak hosts a large number of domains and other hosted services.
Create a token with Domain scope in the API dashboard at
https://manager.infomaniak.com/v3/<account_id>/api/dashboard
and export it as an environment variable:

```
export INFOMANIAK_API_TOKEN=xxx
acme.sh --issue --dns dns_infomaniak -d example.com -d www.example.com
```

Please report any issue to https://github.com/acmesh-official/acme.sh/issues/3188

## 120. Use bookmyname.com API

Bookmyname hosts domains and has a small API.

Export your login/pass as an environment variable:

```
export BOOKMYNAME_USERNAME=xxx
export BOOKMYNAME_PASSWORD=yyy
acme.sh --issue --dns dns_bookmyname --dnssleep 600 -d example.com -d www.example.com
```

Please report any issue to https://github.com/acmesh-official/acme.sh/issues/3209

## 121. Use anexia.com CloudDNS API

For DNS records managed via https://engine.anexia-it.com/clouddns

Export your token as an environment variable:

```
export ANX_Token='XXXXXXXXXXXXXXXXXXX'
acme.sh --issue --dns dns_anx -d example.com -d www.example.com
```
Please report any issue to https://github.com/acmesh-official/acme.sh/issues/3238

## 122. Use Synology DSM Synology DNS Server API
To issue a cert:

    ./acme.sh --issue --dns dns_synology_dsm -d example.com -d www.example.com
To issue a wildcard cert:

    ./acme.sh --issue --dns dns_synology_dsm -d example.com -d *.example.com
You can find more details [here](https://github.com/arabezar/acme.sh/wiki)

Please report any issue [here](https://github.com/acmesh-official/acme.sh/issues/3248)

## 123. Use HuaweiCloud API
Export your credentials as an environment variable:

ProjectID can be found at [here](https://console-intl.huaweicloud.com/iam/?region=ap-southeast-1#/myCredential)
It seems that project ID can be retrieved automatically, but this dnsapi does not implement it yet.

```
export HUAWEICLOUD_Username=<Your Username> # Usually hwxxxxxx
export HUAWEICLOUD_Password=<Your Password>
export HUAWEICLOUD_ProjectID=<A Project ID> 
```

To issue a cert:
```
./acme.sh --issue --dns dns_huaweicloud -d example.com -d www.example.com
```
To issue a wildcard cert:
```
./acme.sh --issue --dns dns_huaweicloud -d example.com -d *.example.com
```

## 124. Use Simply API
Export your credentials, you will find your API key by logging in to your Simply.com account:

```
export SIMPLY_AccountName=<Your accountname>
export SIMPLY_ApiKey=<Your API-key>
```

To issue a cert:
```
./acme.sh --issue --dns dns_simply -d example.com -d www.example.com
```
To issue a wildcard cert:
```
./acme.sh --issue --dns dns_simply -d example.com -d *.example.com
```

## 125. Use World4You API
Export your credentials as an environment variable:

```
export WORLD4YOU_USERNAME=<customer-id>
export WORLD4YOU_PASSWORD=<password>
```

To issue a cert:
```
./acme.sh --issue --dns dns_world4you -d example.com -d www.example.com
```
To issue a wildcard cert:
```
./acme.sh --issue --dns dns_world4you -d example.com -d *.example.com
```

# Use custom API

If your API is not supported yet, you can write your own DNS API.

Let's assume you want to name it 'myapi':

1. Create a bash script named `~/.acme.sh/dns_myapi.sh`,
2. In the script you must have a function named `dns_myapi_add()` which will be called by acme.sh to add the DNS records.
3. Then you can use your API to issue cert like this:

```
acme.sh --issue --dns dns_myapi -d example.com -d www.example.com
```

For more details, please check our sample script: [dns_myapi.sh](https://github.com/Neilpang/acme.sh/blob/master/dnsapi/dns_myapi.sh)

See:  [DNS API Dev Guide](https://github.com/Neilpang/acme.sh/wiki/DNS-API-Dev-Guide)

## 126. Use Scaleway API

First, you'll need to retrieve your [Api Key](https://www.scaleway.com/en/docs/generate-api-keys/)

```
export SCALEWAY_API_TOKEN='xxx'
```

To issue a cert run:

```
acme.sh --issue --dns dns_scaleway -d example.com -d www.example.com
```

-----------------------------------

**More APIs coming soon...**

# Use lexicon DNS API

[How to use lexicon DNS API](https://github.com/Neilpang/acme.sh/wiki/How-to-use-lexicon-DNS-API)
