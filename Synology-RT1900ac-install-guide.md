I got tired of manually maintaining Let's Encrypt on my laptop to update my Router.
so I now pushed that to the router itself.


here's the HowTo (xpost https://forum.synology.com/enu/viewtopic.php?f=265&t=123003 ).


I've used https://github.com/Neilpang/acme.sh , a 3rd party client for Let's Encrypt, based on shell scripting. no extra dependencies.


I've also used it with DNS01 protocol, which means, I don't have any ports open on the router to do the validation, instead it use Cloudflare API, where I host my domain. You can use any name service provider which has an API to automatically add/update the txt record for certificate renewal. See the wiki page on [DNS API Mode](https://github.com/Neilpang/acme.sh/wiki/How-to-issue-a-cert#5-dns-api-mode).

Since the Router shell is very limited, there are several constraints. the most important of all, there is no crontab.

Trying the following default install will fail because of crontab.
```
$ wget -O -  https://get.acme.sh | sh
```

Therefore we have to do it manually:
```
$ wget https://github.com/Neilpang/acme.sh/archive/master.tar.gz
$ tar xvf master.tar.gz
$ cd acme.sh-master/
$ ./acme.sh --install --nocron --home /volume1/@appstore/acme.sh
log out and login to ssh again
```

Installation is done :)

Next, configure:
```
$ cd /volume1/@appstore/acme.sh
```

Configure your credentials for DNS API mode. When using Cloudflare, you'll need your Cloudflare email and API key which you can get [here](https://www.cloudflare.com/a/account/my-account).

Type this to the shell, replace with the values above. Again, see the wiki page on [DNS API Mode](https://github.com/Neilpang/acme.sh/wiki/How-to-issue-a-cert#5-dns-api-mode) to learn about other DNS API modes.
```
export CF_Key="sdfsdfsdfljlbjkljlkjsdfoiwje"
export CF_Email="xxxx@sss.com"
```

Now to create your cert:
```
$ ./acme.sh  --issue --post-hook "/usr/syno/sbin/synoservicecfg --restart httpd-sys" --dns dns_cf --certpath /usr/syno/etc/ssl/ssl.crt/server.crt --keypath /usr/syno/etc/ssl/ssl.key/server.key --ca-file /usr/syno/etc/ssl/ssl.intercrt/server-ca.crt --config-home /volume1/@appstore/acme.sh/ --dnssleep 15 -d YOURDOMAIN.TLD 
```

Simple right?

Since there is no crontab, we need to manually add it to cron. The Let's Encrypt cert expires in 90 days, so the recommended renewal date is 1 month before expiration, i.e. every 2 months. Use a [crontab tester](https://crontab.guru/#3_2_1_1,3,5,7,9,11_*) if you need help with this part. The following updates the certificates at 02:03 on the 1st day in January, March, May, July, September, and November.
```
$ vi /etc/crontab 
3       2       1       1,3,5,7,9,11       *       root    /volume1/@appstore/acme.sh/acme.sh --cron --home /volume1/@appstore/acme.sh
:wq
```

HTH