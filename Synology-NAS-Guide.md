Since Synology introduced [Let's Encrypt](https://letsencrypt.org/), many of us benefit from free SSL. 

On the other hand, many of us don't want to expose port 80/443 to the Internet. The alternative is to use the DNS-01 protocol. Sadly the Synology implementation of Let's Encrypt currently (1-Jan-2017) only supports the HTTP-01 method which requires exposing port 80 to the Internet.

But we can access the NAS via SSH and configure it to renew certs instead of using the web dashboard.

Here's the HowTo (xpopst https://forum.synology.com/enu/viewtopic.php?f=7&t=123007).
I've used https://github.com/Neilpang/acme.sh which is a 3rd party client for Let's Encrypt, based on Shell scripting. No extra dependencies are required.

I've also used it with the DNS-01 protocol, which means, I don't have any ports open on the router to do the validation, instead it uses the [Cloudflare API](https://api.cloudflare.com/), where I host my domain.

Install:

    $ export FORCE=1
    $ wget -O -  https://get.acme.sh | sh
so install is done :)

next step is to do the configuration:

    $ cd ~/.acme.sh/
    $ vi account.conf 

set your email, cloudflare account and API (https://www.cloudflare.com/a/account/my-account)
ctrl+c 

    :wq (and you are out of VI(M))

Now to create your certificate:

    $ ./acme.sh  --issue -d YOURDOMAIN.TLD --dns dns_cf --certpath /usr/syno/etc/certificate/system/default/cert.pem --keypath /usr/syno/etc/certificate/system/default/privkey.pem --fullchainpath /usr/syno/etc/certificate/system/default/fullchain.pem

You'll need to restart the webserver with:  /usr/syno/etc/rc.sysv/nginx.sh reload

simple right?

Since acme.sh installs a cron, it will take care of the renewal for you.

HTH