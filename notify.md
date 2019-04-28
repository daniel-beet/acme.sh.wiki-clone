# Set notification

acme.sh can send notifications in its cronjob. 
Every night when the renew cronjob runs, you may receive notifications based on `notify-level` or `notify-mode`.

The notifications can be emails for another supported way, such as request to a webhook etc.

As for now,  there is email notification supported, but there will be more soon. You can also implement your hook.

See example: `mailgun.sh`  or `sendgrid.sh`


## Usage:
```sh
acme.sh --set-notify  [--notify-hook mailgun]

acme.sh --set-notify  [--notify-level 2]
acme.sh --set-notify  [--notify-mode 0]

```

```

  --notify-level  0|1|2|3           Set the notification level:  Default value is 2.
                                     0: disabled, no notification will be sent.
                                     1: send notification only when there is an error. No news is good news.
                                     2: send notification when a cert is successfully renewed, or there is an error
                                     3: send notification when a cert is skipped, renewdd, or error
  --notify-mode   0|1               Set notification mode. Default value is 0.
                                     0: Bulk mode. Send all the domain's notifications in one message(mail)
                                     1: Cert mode. Send a message for every single cert.
  --notify-hook   [hookname]        Set the notify hook


```





## 1. Set notification for mailgun.com

Send notification by mailgun.com api. The notification email will be sent to your email address.

Please register a free account at mailgun.com, you will get your api key.

If you don't have a domain, you can use the sandbox domain in your account.
It looks like `sandbox888888.mailgun.org`.
If you use the sandbox domain, you must add your receiving email address as an "Authorized recipient"
https://app.mailgun.com/app/account/authorized


```sh
#The api key in your account.
export  MAILGUN_API_KEY="xxxxxxxx"

#The api domain, you can use the sandbox domain in your account.
export  MAILGUN_API_DOMAIN="xxxxxx.com"

#Optional,  the mail from address. it must be user@MAILGUN_API_DOMAIN
export  MAILGUN_FROM="xxx@xxxxxx.com"

#The mail to address, which is to receive the notification.
export  MAILGUN_TO="yyyy@gmail.com"

#Optional, if your mailgun account is in eu region, you must set MAILGUN_REGION
export  MAILGUN_REGION="us|eu"          #optional, use "us" as default

acme.sh --set-notify  --notify-hook  mailgun
```


## 2. Set notification for sendgrid.com

```sh
export SENDGRID_API_KEY="xxxxxxxxxx"
export SENDGRID_FROM="xxxx@cccc.com"
export SENDGRID_TO="xxxx@xxx.com"

acme.sh --set-notify  --notify-hook sendgrid
```


