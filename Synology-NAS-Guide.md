Since Synology introduced Let's Encrypt, many of us benefit from free SSL. 

OTOH many of us dont want to expose port 80/443 to the internet.
The alternative is to use DNS01 protocol.
Sadly, Synology implementation of LE is only acme (web).

But we can access the NAS via SSH and configure it to renew certs instead of using the web dashboard.

here's the HowTo (xpopst https://forum.synology.com/enu/viewtopic.php?f=7&t=123007).
I've used https://github.com/Neilpang/acme.sh , a 3rd party client for Let's Encrypt, based on Perl. no extra dependencies.
I've also used it with DNS01 protocol, which means, I don't have any ports open on the router to do the validation, instead it use Cloudflare API, where I host my domain.

Install
$ wget -O -  https://get.acme.sh | sh
so install is done :)

next, config
$ cd ~/.acme.sh/
$ vi account.conf 
set your email, cloudflare account and API (https://www.cloudflare.com/a/account/my-account)
ctrl+c 
:wq (and you are out of VI(M))

now to create your cert
$ ./acme.sh  --issue -d YOURDOMAIN.TLD --dns dns_cf --certpath /usr/syno/etc/certificate/system/default/cert.pem --keypath /usr/syno/etc/certificate/system/default/privkey.pem --fullchainpath /usr/syno/etc/certificate/system/default/fullchain.pem
(anyone knows how to restart the webserver?)

simple right?

Since acme.sh installs a cron, it will take care of the renewal for you.

HTH