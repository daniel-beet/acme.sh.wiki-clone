Since Synology introduced [Let's Encrypt](https://letsencrypt.org/), many of us benefit from free SSL. 


On the other hand, many of us don't want to expose port 80/443 to the Internet. The alternative is to use the DNS-01 protocol. Sadly the Synology implementation of Let's Encrypt currently (1-Jan-2017) only supports the HTTP-01 method which requires exposing port 80 to the Internet.


But we can access the NAS via SSH and configure it to renew certs instead of using the web dashboard.


Here's the HowTo (xpopst https://forum.synology.com/enu/viewtopic.php?f=7&t=123007).
I've used https://github.com/Neilpang/acme.sh which is a 3rd party client for Let's Encrypt, based on Shell scripting. No extra dependencies are required.


I've also used it with the DNS-01 protocol, which means, I don't have any ports open on the router to do the validation, instead it uses the [Cloudflare API](https://api.cloudflare.com/), where I host my domain.


Install:

```
    $ export FORCE=1
    $ wget -O -  https://get.acme.sh | sh
```
so install is done :)


It will ask you to logout and login back again, to set shell settings.


next step is to do the configuration:

```
    $ cd ~/.acme.sh/
```


set your email, cloudflare account and API (https://www.cloudflare.com/a/account/my-account)

```
export CF_Key="sdfsdfsdfljlbjkljlkjsdfoiwje"
export CF_Email="xxxx@sss.com"
```
Now to create your certificate:

    $ ./acme.sh  --issue -d YOURDOMAIN.TLD --dns dns_cf --certpath /usr/syno/etc/certificate/system/default/cert.pem --keypath /usr/syno/etc/certificate/system/default/privkey.pem --fullchainpath /usr/syno/etc/certificate/system/default/fullchain.pem --reloadcmd "/usr/syno/etc/rc.sysv/nginx.sh reload" --dnssleep 10

Please note, in this way it will replace/overwrite your Synology NAS system default certificate directly. 

---------------------------------------------------------------------------------------------------------
**Below are optional steps!!!**

Alternatively, you can change the certificates install path to your DSM cert library folder which will only replace the certificate you nominated, no impact to system default one. 

For example:
 
   `$./acme.sh  --issue -d YOURDOMAIN.TLD --dns dns_cf --certpath /usr/syno/etc/certificate/_archive/**vPATH**/cert.pem --keypath /usr/syno/etc/certificate/_archive/**vPATH**/privkey.pem --fullchainpath /usr/syno/etc/certificate/_archive/**vPATH**/fullchain.pem --capath /usr/syno/etc/certificate/_archive/**vPATH**/chain.pem --reloadcmd "/usr/syno/etc/rc.sysv/nginx.sh reload"`

You need to manually replace the 'vPATH' field with the directory name under your /usr/syno/etc/certificate/_archive/ . The directory name is a six characters unique name. 

Now you can check the DSM control panel - Security - Certificates to see the nominated certificate has been replaced by letsencrypt one. You can now configure to use this one as default and assign to specific services, like vpn, sftp, etc.
 
To auto renew the certificates in the future, you need to configure the cronjob. However, acme.sh seems not properly add tasks to Synology crontab. You have to do this manually. 

Configure crontab for root

`$ vi /etc/crontab `

Add the following line to the crontab. Remember to use tab for spacing. 
For example, 10:00 am of the 2nd day every month run the cronjob to check if due to renew the certificates (You can modify the cronjob schedule according to your needs) 

`0    10    2    *    *    root    /root/.acme.sh/acme.sh --cron --home /root/.acme.sh/`

The last step is to setup a schedule task to copy renewed certificates in cert library to system default directory and restart the Nginx service.

In DSM control panel, open the 'Task Scheduler' and create a new scheduled task for a user-defined script.  

* General Setting: Task - Update default Cert. User - root
* Schedule: Setup the time according to your acme.sh crontab schedule. For example, 11:00 am of the 2nd day every month.
* Task setting: User-defined-script

`rsync -avzh /usr/syno/etc/certificate/_archive/**vPATH**/ /usr/syno/etc/certificate/system/default/`

`/usr/syno/etc/rc.sysv/nginx.sh reload `

Now you should all good. 

--------------------------------------------------------------------------------------------------------------------

HTH