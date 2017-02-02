I got tired of manually maintaining Let's Encrypt on my laptop to update my Router.
so I now pushed that to the router itself.

here's the HowTo (xpost https://forum.synology.com/enu/viewtopic.php?f=265&t=123003 ).
I've used https://github.com/Neilpang/acme.sh , a 3rd party client for Let's Encrypt, based on Perl. no extra dependencies.
I've also used it with DNS01 protocol, which means, I don't have any ports open on the router to do the validation, instead it use Cloudflare API, where I host my domain.

Since the Router shell is very limited, there are several constraints. the most important of all, there is no crontab.

trying the default install
$ wget -O -  https://get.acme.sh | sh
, will fail because of crontab.

so we have to do it manually
$ wget https://github.com/Neilpang/acme.sh/archive/master.tar.gz
$ tar xvf master.tar.gz
$ cd acme.sh-master/
$ ./acme.sh --install --nocron --home /volume1/@appstore/acme.sh

so install is done :)

next, config
$ cd /volume1/@appstore/acme.sh
$ vi /volume1/@appstore/acme.sh/dnsapi/dns_cf.sh
set your email, cloudflare account and API (https://www.cloudflare.com/a/account/my-account)
ctrl+c 
:wq (and you are out of VI(M)

now to create your cert
$ ./acme.sh  --issue --post-hook "kill -USR1 `cat /run/httpd/httpd-sys.pid`" -d YOURDOMAIN.TLD --dns dns_cf --certpath /usr/syno/etc/ssl/ssl.crt/server.crt --keypath /usr/syno/etc/ssl/ssl.key/server.key --fullchainpath /usr/syno/etc/ssl/ssl.intercrt/server-ca.crt --config-home /volume1/@appstore/acme.sh/

simple right?

since there is no crontab, we need to manually add it to cron.
$ vi /etc/crontab 
and add something like
3       2       *       *       2       root    /root/.acme.sh/acme.sh --cron
:wq

HTH