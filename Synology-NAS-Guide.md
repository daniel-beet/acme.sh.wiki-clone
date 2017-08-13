# HTTPS certificates for your Synology NAS using acme.sh

Since Synology introduced [Let's Encrypt](https://letsencrypt.org/), many of us benefit from free SSL. 

On the other hand, many of us don't want to expose port 80/443 to the Internet, including opening ports on the router. The alternative is to use the DNS-01 protocol. Sadly the Synology implementation of Let's Encrypt currently (1-Jan-2017) only supports the HTTP-01 method which requires exposing port 80 to the Internet. Also, if the domain of your NAS has an IPv6 AAAA record set, the Synology implementation of Let's Encrypt will fail.

But we can access the NAS via SSH and configure it to renew certs instead of using the web dashboard.

The following guide will use the DNS-01 protocol using the [Cloudflare API](https://api.cloudflare.com/), where I host my domain. However, [since acme.sh supports many DNS services](https://github.com/Neilpang/acme.sh/tree/master/dnsapi), you can also choose the one you like.

## Installation of acme.sh

    $ wget https://github.com/Neilpang/acme.sh/archive/master.tar.gz
    $ tar xvf master.tar.gz
    $ cd acme.sh-master/
    $ ./acme.sh --install --nocron --home /usr/local/sbin/acme.sh

## Configuring DNS

For CloudFlare, we will set two environment variables that acme.sh (specifically, the `dns_cf` script from the `dnsapi` subdirectory) will read to set the DNS record. You can get your CloudFlare [API key here](https://www.cloudflare.com/a/account/my-account).

    export CF_Key="MY_SECRET_KEY_SUCH_SECRET"
    export CF_Email="myemail@example.com"

In case you use another DNS service, check the `dnsapi` directory. Instructions for many DNS providers are already included. You can also find instructions on how to add another DNS service there, although that requires some software development skills.

## Creating the certificate
Now it's time to create the certificate for your domain:

    $ cd /usr/local/sbin/acme.sh
    $ ./acme.sh --issue -d YOURDOMAIN.TLD --dns dns_cf \
          --certpath /usr/syno/etc/certificate/system/default/cert.pem \
          --keypath /usr/syno/etc/certificate/system/default/privkey.pem \
          --fullchainpath /usr/syno/etc/certificate/system/default/fullchain.pem \
          --reloadcmd "/usr/syno/etc/rc.sysv/nginx.sh reload" \
          --dnssleep 10

Please note, in this way it will replace/overwrite your Synology NAS system default certificate directly. 

---------------------------------------------------------------------------------------------------------
**Below are optional steps!!!**

Alternatively, you can change the certificates install path to your DSM cert library folder which will only replace the certificate you nominated, no impact to system default one. 

For example:
 
    $./acme.sh  --issue -d YOURDOMAIN.TLD --dns dns_cf \
        --certpath /usr/syno/etc/certificate/_archive/**vPATH**/cert.pem \
        --keypath /usr/syno/etc/certificate/_archive/**vPATH**/privkey.pem \
        --fullchainpath /usr/syno/etc/certificate/_archive/**vPATH**/fullchain.pem \
        --capath /usr/syno/etc/certificate/_archive/**vPATH**/chain.pem \
        --reloadcmd "/usr/syno/etc/rc.sysv/nginx.sh reload"

You need to manually replace the 'vPATH' field with the directory name under your /usr/syno/etc/certificate/_archive/ . The directory name is a six characters unique name. 

Now you can check the DSM control panel - Security - Certificates to see the nominated certificate has been replaced by letsencrypt one. You can now configure to use this one as default and assign to specific services, like vpn, sftp, etc.
 
To auto renew the certificates in the future, you need to configure the cronjob. However, acme.sh seems not properly add tasks to Synology crontab. You have to do this manually. 

Configure crontab for root

    $ vi /etc/crontab

Add the following line to the crontab. Remember to use tab for spacing. 
For example, 10:00 am of the 2nd day every month run the cronjob to check if due to renew the certificates (You can modify the cronjob schedule according to your needs) 

    0    10    2    *    *    root    /usr/local/sbin/acme.sh/acme.sh --cron --home /usr/local/sbin/acme.sh/

The last step is to setup a schedule task to copy renewed certificates in cert library to system default directory and restart the Nginx service.

In DSM control panel, open the 'Task Scheduler' and create a new scheduled task for a user-defined script.  

* General Setting: Task - Update default Cert. User - root
* Schedule: Setup the time according to your acme.sh crontab schedule. For example, 11:00 am of the 2nd day every month.
* Task setting: User-defined-script

    rsync -avzh /usr/syno/etc/certificate/_archive/**vPATH**/ /usr/syno/etc/certificate/system/default/`

    /usr/syno/etc/rc.sysv/nginx.sh reload

Now you should all good. 

--------------------------------------------------------------------------------------------------------------------

To fix the broken environment after Synology DSM upgrade, you can

    $ cd /urs/local/sbin/acme.sh
    $ ./acme.sh --upgrade --nocron --home /usr/local/sbin/acme.sh

or manually add below line into /root/.profile

    . "/usr/local/sbin/acme.sh/acme.sh.env"