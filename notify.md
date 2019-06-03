# Set notification

acme.sh can send notifications in its cronjob. 
Every night when the renew cronjob runs, you may receive notifications based on `notify-level` and `notify-mode`.

```

  --notify-level  0|1|2|3           Set the notification level:  Default value is 2.

                 0: disabled, no notification will be sent.
                 1: send notification only when there is an error. No news is good news.
                 2: send notification when a cert is successfully renewed, or there is an error
                 3: send notification when a cert is skipped, renewed, or error. You will receive notification every night with this level.


  --notify-mode   0|1               Set notification mode. Default value is 0.

                  0: Bulk mode. Send all the domain's notifications in one message(email)
                  1: Cert mode. Send a message for every single cert. You may receive a bulk of emails in one night.

  --notify-hook   [hookname]        Set the notify hook


```

The notifications can be emails for another supported way, such as request to a webhook etc.

As for now,  there is email notification supported, but there will be more soon. You can also implement your hook.

See example: `mailgun.sh`  or `sendgrid.sh`


## Usage:
```sh
acme.sh --set-notify  [--notify-hook mailgun]

acme.sh --set-notify  [--notify-level 2]
acme.sh --set-notify  [--notify-mode 0]

```

There can be multiple `--notify-hook` parameters:

```
acme.sh --set-notify  --notify-hook mailgun  --notify-hook mail
```

And, all the parameters can be used together:

```sh
acme.sh --set-notify  --notify-hook mailgun  --notify-hook mail  \
  --set-notify  --notify-level 2 \
  --set-notify  --notify-mode 0

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


## 3. Set notification for mail

Set it in your systems environment:

```sh
export MAIL_FROM="xxx@xxx.com" # currently work only with sendmail
export MAIL_TO="xxx@xxx.com"   # your account e-mail will be used as default if available
```

It will try to find and use `sendmail`, `ssmtp`, `mutt` or `mail` automatically, if installed.

If you want to specify which application to use,  please use `MAIL_BIN`:

```sh
export MAIL_BIN="sendmail"     # should be one of following: sendmail, ssmtp, mutt or mail
```

Ok, let's set notification hook:

```sh
acme.sh --set-notify  --notify-hook mail
```

The `MAIL_BIN`, `MAIL_TO` and `MAIL_FROM` will be saved in ~/.acme.sh/account.conf and will be reused when needed.


## 4. Set notification for Slack Webhooks

First get your [Slack Webhook URL](https://slack.com/apps/A0F7XDUAZ-incoming-webhooks), then set it in your systems environment:

```sh
export SLACK_WEBHOOK_URL="..."
export SLACK_CHANNEL="..."     # overwrites Slack Webhook channel
export SLACK_USERNAME="..."    # overwrites Slack Webhook username
```

Ok, let's set notification hook:

```
acme.sh --set-notify  --notify-hook slack
```

The `SLACK_WEBHOOK_URL`, `SLACK_CHANNEL` and `SLACK_USERNAME` will be saved in ~/.acme.sh/account.conf and will be reused when needed.


## 5. Set notification for postmarkapp.com

Send notification by postmarkapp.com API. The notification email will be sent to your email address. First get your [token](https://account.postmarkapp.com), then set it in your systems environment:

```sh
#The api token.
export  POSTMARK_TOKEN="xxxxxxxx"

#The mail to address.
export  POSTMARK_TO="xxx@xxx.com"

#The mail from address.
export  POSTMARK_FROM="xxx@xxx.com"
```

Ok, let's set notification hook:

```sh
acme.sh --set-notify  --notify-hook  postmark
```

The `POSTMARK_TOKEN`, `POSTMARK_TO` and `POSTMARK_FROM` will be saved in ~/.acme.sh/account.conf and will be reused when needed.
