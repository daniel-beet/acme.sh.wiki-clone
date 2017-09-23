How to install a SSL/TLS Let’s Encrypt cert into a cpanel account
Based on https://github.com/Neilpang/acme.sh/blob/master/deploy/README.md

We will use acme.sh app, which is a Let’s Encrypt 3rd party client, with its cpanel API.
Replace _EXAMPLE.COM_ with your domain


First we SSH into your cpanel host.
Then install acme running the following command:
`$ curl https://get.acme.sh | sh`

  `% Total    % Received % Xferd  Average Speed   Time    Time     Time  Current`
                                 `Dload  Upload   Total   Spent    Left  Speed`
`100   705  100   705    0     0    246      0  0:00:02  0:00:02 --:--:--  2690`
  `% Total    % Received % Xferd  Average Speed   Time    Time     Time  Current`
                                 `Dload  Upload   Total   Spent    Left  Speed`
`100  148k  100  148k    0     0   465k      0 --:--:-- --:--:-- --:--:--  781k`
`[Sat Sep 23 06:36:51 EDT 2017] Installing from online archive.`
`[Sat Sep 23 06:36:51 EDT 2017] Downloading https://github.com/Neilpang/acme.sh/archive/master.tar.gz`
`[Sat Sep 23 06:36:52 EDT 2017] Extracting master.tar.gz`
`[Sat Sep 23 06:36:53 EDT 2017] It is recommended to install socat first.`
`[Sat Sep 23 06:36:53 EDT 2017] We use socat for standalone server if you use standalone mode.`
`[Sat Sep 23 06:36:53 EDT 2017] If you don't use standalone mode, just ignore this warning.`
`[Sat Sep 23 06:36:53 EDT 2017] Installing to /home/EXAMPLE.COM/.acme.sh`
`[Sat Sep 23 06:36:53 EDT 2017] Installed to /home/EXAMPLE.COM/.acme.sh/acme.sh`
`[Sat Sep 23 06:36:53 EDT 2017] Installing alias to '/home/EXAMPLE.COM/.bashrc'`
`[Sat Sep 23 06:36:53 EDT 2017] OK, Close and reopen your terminal to start using acme.sh`
`[Sat Sep 23 06:36:56 EDT 2017] Installing cron job`
`[Sat Sep 23 06:36:57 EDT 2017] Good, bash is found, so change the shebang to use bash as preferred.`
`[Sat Sep 23 06:36:57 EDT 2017] OK`
`[Sat Sep 23 06:36:57 EDT 2017] Install success!`

Then you either logoff and ssh again or just source bashrc:
$ source ~/.bashrc

Add your email to be notified of issues with the TLS cert:
`$ acme.sh --update-account --accountemail _USERNAME@EXAMPLE.COM_`

Now let’s issue a test cert to see if everything is in place for the real cert to be issued and put in place.
We will use the webroot method, which requires the use to pass the location of their public_html folder. 
The default one is ~/public_html , but if the refers to an addon domain, it will be that folder instead.
`$ acme.sh --issue -w ~/public_html/ -d _EXAMPLE.COM_ **--test**`

If successful, then we issue the real cert:
`$ acme.sh --issue -w ~/public_html/ -d _EXAMPLE.COM_`

Next we enter the cpanel username (replace with your account name):
`export DEPLOY_CPANEL_USER=_username_`

Next we add the cert to the cpanel database:
`$ acme.sh --deploy --deploy-hook cpanel_uapi -d _EXAMPLE.COM_`
`[Sat Sep 23 06:53:08 EDT 2017] Certificate successfully deployed`
`[Sat Sep 23 06:53:08 EDT 2017] Success`

You can see if a crontab responsible to renew your cert every 60 days has been installed with the following command:
`$ crontab -l`
`56 0 * * * "/home/EXAMPLE.COM/.acme.sh"/acme.sh --cron --home "/home/EXAMPLE.COM/.acme.sh" > /dev/null`

In your cpanel account, you should see the new cron and also the new TLS cert applied to your domain.

**Final step is create a redirect from http to https**
