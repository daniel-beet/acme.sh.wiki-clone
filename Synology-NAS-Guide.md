# HTTPS certificates for your Synology NAS using acme.sh

Since Synology introduced [Let's Encrypt](https://letsencrypt.org/), many of us benefit from free SSL. 

On the other hand, many of us don't want to expose port 80/443 to the Internet, including opening ports on the router. The alternative is to use the DNS-01 protocol. Sadly the Synology implementation of Let's Encrypt currently (1-Jan-2017) only supports the HTTP-01 method which requires exposing port 80 to the Internet. Also, if the domain of your NAS has an IPv6 AAAA record set, the Synology implementation of Let's Encrypt will fail.

But we can access the NAS via SSH and configure it to renew certs instead of using the web dashboard.

The following guide will use the DNS-01 protocol using the [Cloudflare API](https://api.cloudflare.com/), where I host my domain. However, [since acme.sh supports many DNS services](https://github.com/Neilpang/acme.sh/tree/master/dnsapi), you can also choose the one you like.

## Installation of acme.sh

    $ sudo -i
    $ wget https://github.com/Neilpang/acme.sh/archive/master.tar.gz
    $ tar xvf master.tar.gz
    $ cd acme.sh-master/
    $ ./acme.sh --install --nocron --home /usr/local/share/acme.sh --accountemail "email@gmailcom"

## Installation of reload-certs.sh (optional)
You can use [this third party script](https://github.com/bartowl/synology-stuff/blob/master/reload-certs.sh) to automatically restart services for which certificates were changed.

    $ wget -O /usr/local/bin/reload-certs.sh https://github.com/bartowl/synology-stuff/raw/master/reload-certs.sh
    $ chmod +x /usr/local/bin/reload-certs.sh

Be sure to close your session after installation and reconnect for the following steps. 

## Configuring DNS

For CloudFlare, we will set two environment variables that acme.sh (specifically, the `dns_cf` script from the `dnsapi` subdirectory) will read to set the DNS record. You can get your CloudFlare [API key here](https://dash.cloudflare.com/profile).

    export CF_Key="MY_SECRET_KEY_SUCH_SECRET"
    export CF_Email="myemail@example.com"

If you generated an API Token, instead of using your global account key, set CF_Token instead.

    export CF_Token="MY_SECRET_TOKEN_SUCH_SECRET"
    export CF_Email="myemail@example.com"

In case you use another DNS service, check the `dnsapi` directory. Instructions for many DNS providers are already included. You can also find instructions on how to add another DNS service there, although that requires some software development skills.

## Creating the certificate
Now it's time to create the certificate for your domain:

    $ cd /usr/local/share/acme.sh
    $ export CERT_DOMAIN="your-domain.tld"
    $ export CERT_DNS="dns_cf"
    $ ./acme.sh --issue -d "$CERT_DOMAIN" --dns "$CERT_DNS" \
          --cert-file /usr/syno/etc/certificate/system/default/cert.pem \
          --key-file /usr/syno/etc/certificate/system/default/privkey.pem \
          --fullchain-file /usr/syno/etc/certificate/system/default/fullchain.pem \
          --reloadcmd "/usr/syno/sbin/synoservicectl --reload nginx" \
          --dnssleep 20

Please note that this will replace your Synology NAS system default certificate directly without rolling out for other services, just the DSM console.

---------------------------------------------------------------------------------------------------------
## Alternative and recommended method that fully integrates with Synology NAS system certificate management
This requires the reload-certs.sh script.

    $ cd /usr/syno/etc/certificate/_archive
    $ export CERT_DIR="$(mktemp -d XXXXXX)"
    $ export CERT_FOLDER="/usr/syno/etc/certificate/_archive/$CERT_DIR"
    $ export CERT_DOMAIN="your-domain.tld"
    $ export CERT_DNS="dns_cf"
    $ cp -a INFO INFO.bak
    $ jq '.+={"'$CERT_DIR'":{"desc":"'$CERT_DOMAIN'","services":[]}}' INFO.bak > INFO
    $ cd /usr/local/share/acme.sh
    $ ./acme.sh  --issue -d "$CERT_DOMAIN" --dns "$CERT_DNS" \
        --cert-file "$CERT_FOLDER/cert.pem" \
        --key-file "$CERT_FOLDER/privkey.pem" \
        --fullchain-file "$CERT_FOLDER/fullchain.pem" \
        --capath "$CERT_FOLDER/chain.pem" \
        --reloadcmd "/usr/local/bin/reload-certs.sh" \
        --dnssleep 20

Now you can check the DSM control panel - Security - Certificates to see the new certificate that has been created. 
You can now configure to use this one as default and/or assign to specific services, like vpn, sftp, etc.

## Configuring Certificate Renewal
To auto renew the certificates in the future, you need to configure a task in the task scheduler. It is not advised to set this up as a custom cronjob (as was previously described in this wiki page) as the DSM security advisor will tell you that you have a critical warning regarding unknown cronjob(s).

In DSM control panel, open the 'Task Scheduler' and create a new scheduled task for a user-defined script.  

* General Setting: Task - Update default Cert. User - root
* Schedule: Setup a weekly renewal. For example, 11:00 am every saturday.
* Task setting: User-defined-script:

```
# renew certificates, this used to be explained as a custom cronjob but works just as well within this script according to the output of the task. 
/usr/local/share/acme.sh/acme.sh --cron --home /usr/local/share/acme.sh/
```
Make sure you created the certificates with proper reloadcmd. Now you should be all good. 

--------------------------------------------------------------------------------------------------------------------

## Fix a broken environment after Synology DSM upgrade

    $ cd /usr/local/share/acme.sh
    $ ./acme.sh --force --upgrade --nocron --home /usr/local/share/acme.sh

or manually add below line into /root/.profile

    . "/usr/local/share/acme.sh/acme.sh.env"