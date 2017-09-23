# How to install a SSL/TLS Let’s Encrypt cert into a cpanel account
Based on https://github.com/Neilpang/acme.sh/blob/master/deploy/README.md

## We will use acme.sh app, which is a Let’s Encrypt 3rd party client, with its cpanel API.
Replace _EXAMPLE.COM_ with your domain
***

## First we SSH into your cpanel host.
Then install acme running the following command:

`$ curl https://get.acme.sh | sh`

## Then you either logoff and ssh again or just source bashrc:

`$ source ~/.bashrc`

## Add your email to be notified of issues with the TLS cert:

`$ acme.sh --update-account --accountemail `_USERNAME@EXAMPLE.COM_

## Now let’s issue a test cert to see if everything is in place for the real cert to be issued and put in place.
We will use the webroot method, which requires the user to enter the location of their public_html folder. 

The default one is ~/public_html , but if you are using an addon domain, it will be that folder instead.

`$ acme.sh --issue --keylength ec-256 --ecc --webroot ~/public_html/ -d `_EXAMPLE.COM_ **--staging**

## If successful, then we issue the real cert:
`$ acme.sh --issue --keylength ec-256 --ecc --webroot ~/public_html/ -d `_EXAMPLE.COM_ **--force**

## Next we enter the cpanel username (replace with your account name):
`$ export DEPLOY_CPANEL_USER=_username_`

## Next we add the cert to the cpanel database:
`$ acme.sh --deploy --deploy-hook cpanel_uapi -d `_EXAMPLE.COM_

`[Sat Sep 23 06:53:08 EDT 2017] Certificate successfully deployed`

`[Sat Sep 23 06:53:08 EDT 2017] Success`

***

## You can see if a crontab responsible to renew your cert every 60 days has been installed with the following command:
`$ crontab -l`
`56 0 * * * "/home/EXAMPLE.COM/.acme.sh"/acme.sh --cron --home "/home/EXAMPLE.COM/.acme.sh" > /dev/null`

## In your cpanel account, you should see the new cron and also the new TLS cert applied to your domain.

## **Final step is create a redirect from http to https**
