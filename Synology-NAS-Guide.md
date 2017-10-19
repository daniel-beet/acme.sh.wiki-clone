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
    $ ./acme.sh --install --nocron --home /usr/local/share/acme.sh

## Configuring DNS

For CloudFlare, we will set two environment variables that acme.sh (specifically, the `dns_cf` script from the `dnsapi` subdirectory) will read to set the DNS record. You can get your CloudFlare [API key here](https://www.cloudflare.com/a/account/my-account).

    export CF_Key="MY_SECRET_KEY_SUCH_SECRET"
    export CF_Email="myemail@example.com"

In case you use another DNS service, check the `dnsapi` directory. Instructions for many DNS providers are already included. You can also find instructions on how to add another DNS service there, although that requires some software development skills.

## Creating the certificate
Now it's time to create the certificate for your domain:

    $ cd /usr/local/share/acme.sh
    $ export CERT_DOMAIN="your-domain.tld"
    $ export CERT_DNS="dns_cf"
    $ ./acme.sh --issue -d "$CERT_DOMAIN" --dns "$CERT_DNS" \
          --certpath /usr/syno/etc/certificate/system/default/cert.pem \
          --keypath /usr/syno/etc/certificate/system/default/privkey.pem \
          --fullchainpath /usr/syno/etc/certificate/system/default/fullchain.pem \
          --reloadcmd "/usr/syno/sbin/synoservicectl --reload nginx" \
          --dnssleep 20

Please note that this will replace your Synology NAS system default certificate directly.

---------------------------------------------------------------------------------------------------------
## Alternative method that preserves your Synology NAS system default certificate

    $ export CERT_FOLDER="$(find /usr/syno/etc/certificate/_archive/ -maxdepth 1 -mindepth 1 -type d)"
    $ # Make sure $CERT_FOLDER is only one name. Else you have to manually specify the folder.
    $ export CERT_DOMAIN="your-domain.tld"
    $ export CERT_DNS="dns_cf"
    $ ./acme.sh  --issue -d "$CERT_DOMAIN" --dns "$CERT_DNS" \
        --certpath "$CERT_FOLDER/cert.pem" \
        --keypath "$CERT_FOLDER/privkey.pem" \
        --fullchainpath "$CERT_FOLDER/fullchain.pem" \
        --capath "$CERT_FOLDER/chain.pem" \
        --reloadcmd "/usr/syno/sbin/synoservicectl --reload nginx" \
        --dnssleep 20

Now you can check the DSM control panel - Security - Certificates to see the nominated certificate has been replaced by letsencrypt one. You can now configure to use this one as default and assign to specific services, like vpn, sftp, etc.
 

## Configuring Certificate Renewal
To auto renew the certificates in the future, you need to configure the cronjob. However, acme.sh seems not properly add tasks to Synology crontab. You have to do this manually. 

Configure crontab for root

    $ vim /etc/crontab

Add the following line to the crontab. Remember to use tab for spacing. 
For example, 10:00 am of the 2nd day every month run the cronjob to check if due to renew the certificates (You can modify the cronjob schedule according to your needs) 

    0    10    2    *    *    root    /usr/local/share/acme.sh/acme.sh --cron --home /usr/local/share/acme.sh/

If using the alternate method from above, the last step is to setup a schedule task to copy renewed certificates in cert library to system default directory and restart the Nginx service.

In DSM control panel, open the 'Task Scheduler' and create a new scheduled task for a user-defined script.  

* General Setting: Task - Update default Cert. User - root
* Schedule: Setup the time according to your acme.sh crontab schedule. For example, 11:00 am of the 2nd day every month.
* Task setting: User-defined-script:

```
# Note: The $CERT_FOLDER must be hardcoded here since the running environment is unknown. Don't blindly copy&paste
# change this sample directory name "AsDFgH" to the name of your Let's Encrypt cert directory

CERTDIR="AsDFgH"

# do not change anything beyond this line!

CERTROOTDIR="/usr/syno/etc/certificate"
PACKAGECERTROOTDIR="/usr/local/etc/certificate"
FULLCERTDIR="$CERTROOTDIR/_archive/$CERTDIR"

# find all subdirectories containing cert.pem files
PEMFILES=$(find $CERTROOTDIR -name cert.pem)
if [ ! -z "$PEMFILES" ]; then
        for DIR in $PEMFILES; do
                # replace all certificates, but not the ones in the _archive folder
                if [[ $DIR != *"/_archive/"* ]]; then
                        rsync -avh "$FULLCERTDIR/" "$(dirname $DIR)/"
                fi
        done
fi

# reload
/usr/syno/sbin/synoservicectl --reload nginx

# update and restart all installed packages
PEMFILES=$(find $PACKAGECERTROOTDIR -name cert.pem)
if [ ! -z "$PEMFILES" ]; then
	for DIR in $PEMFILES; do
		rsync -avh "$FULLCERTDIR/" "$(dirname $DIR)/"
		/usr/syno/bin/synopkg restart $(echo $DIR | awk -F/ '{print $6}')
	done
fi
```
Now you should be all good. 

--------------------------------------------------------------------------------------------------------------------

## Fix a broken environment after Synology DSM upgrade

    $ cd /urs/local/share/acme.sh
    $ ./acme.sh --upgrade --nocron --home /usr/local/share/acme.sh

or manually add below line into /root/.profile

    . "/usr/local/share/acme.sh/acme.sh.env"