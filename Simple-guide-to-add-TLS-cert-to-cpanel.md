# How to install a SSL/TLS Let’s Encrypt cert into a cPanel account
Based on https://github.com/Neilpang/acme.sh/blob/master/deploy/README.md

## We will use acme.sh app, which is a Let’s Encrypt 3rd party client, with its cPanel API.
Replace _EXAMPLE.COM_ with your domain
***

## First we SSH into your cPanel host.
`$ ssh -l `_USERNAME_` -p `_PORT_PROVIDED_BY_CPANEL_SUPPORT_ `  ` _SERVER_ADDRESS_

## Then install acme running the following command:

`$ curl https://get.acme.sh | sh`

## Then you either logoff and ssh again or just source bashrc:

`$ source ~/.bashrc`

## Add your email to be notified of issues renewing the TLS cert:

`$ acme.sh --update-account --accountemail `_emailaddress@EXAMPLE.COM_

## Now let’s issue a test cert to see if everything is in place for the real cert to be issued and put in place.
We will use the webroot method, which requires the user to enter the location of their public_html folder. 

The default one is ~/public_html , but if you are using an addon domain, it will be that folder instead.

`$ acme.sh --issue --webroot ~/public_html/ -d `_EXAMPLE.COM_ **--staging**


### Be sure that the cert was issued without errors before proceeding. 

Keep in mind Let's Encrypt API limits https://letsencrypt.org/docs/rate-limits/

If errors happen, rerun the command with --debug 2 and see if you detect the problem.


## Once successful issue the real cert:

`$ acme.sh --issue --webroot ~/public_html/ -d `_EXAMPLE.COM_ **--force**

We use --force to replace the initially issued staging cert

https://letsencrypt.org/docs/staging-environment/


***


## Next we enter the cPanel username (replace with your account name):

`$ export DEPLOY_cPanel_USER=`_username_

## Next we add the cert to the cPanel database:

`$ acme.sh --deploy --deploy-hook cpanel_uapi --domain `_EXAMPLE.COM_

`[Sat Sep 23 06:53:08 EDT 2017] Certificate successfully deployed`

`[Sat Sep 23 06:53:08 EDT 2017] Success`

### Please note, that the cpanel_uapi hook will deploy only the first domain when your certificate will automatically renew. Therefore you should issue a separate certificate for each domain.

***

## You can see if a crontab responsible to renew your cert every 60 days has been installed with the following command:

`$ crontab -l`

`56 0 * * * "/home/EXAMPLE.COM/.acme.sh"/acme.sh --cron --home "/home/EXAMPLE.COM/.acme.sh" > /dev/null`

## In your cPanel account, you should see the new cron and also the new TLS cert applied to your domain.

## **Final step is create a redirect from http to https**
Go to cPanel File Manager, create a .htaccess file in the root of your public_html folder, edit, and add the following:

`RewriteCond %{HTTPS} off`

`# First rewrite to HTTPS:`

`RewriteRule .* https://%{HTTP_HOST}%{REQUEST_URI} [L,R=301]`


***


# ADVANCE SETUP

## Once your site is running smoothly with TLS, you can have browsers preload HTTPS.

It's called HSTS Preload. Before continuing, read more at https://scotthelme.co.uk/hsts-preloading/

Once informed, edit .htaccess and add the following:

`<IfModule mod_headers.c>`

`Header set Strict-Transport-Security "max-age=60; " env=HTTPS`

`</IfModule>`


### This will add HSTS for 60 seconds. If the site is working as expect, increase it to 86400 seconds (one day).

`<IfModule mod_headers.c>`

`Header set Strict-Transport-Security "max-age=86400; " env=HTTPS`

`</IfModule>`


### Once that is proven to work, change to 6 months.

`<IfModule mod_headers.c>`

`Header set Strict-Transport-Security "max-age=15768000; " env=HTTPS`

`</IfModule>`

## You may consider to add preload flag and submit to https://hstspreload.org/