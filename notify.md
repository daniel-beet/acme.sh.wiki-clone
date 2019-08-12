# Set notifications

acme.sh can send notifications in its cronjob. 
Every night when the renew cronjob runs, you may receive notifications based on `notify-level` and `notify-mode`.

To configure notifications, use the `--set-notify` argument. This will send test notifications and update account.conf with the new settings. Do not include `--set-notify` in your cron job.

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

The notifications can be emails for any other supported way, such as requesting to a webhook, etc.

You can also implement your own hook.

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
  --notify-level 2 \
  --notify-mode 0

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

If there are any bugs for postmarkapp.com API, please report here: https://github.com/Neilpang/acme.sh/issues/2309

## 6. Set notification for pushover.net

Send notification via pushover.net's api. The notification will be pushed to the specified pushover application.

Make a note of your PushOver user key from your account dashboard
Create your pushover application at https://pushover.net/apps/build and note the API Token.

```sh
#The application token.
export PUSHOVER_TOKEN="xxxxxxxx"

#Your User key.
export PUSHOVER_USER="xxxxxxxx"

#Optional, name of a custom sound listed at https://pushover.net/api#sounds (Blank or not set will play default)
export PUSHOVER_SOUND="xxxxxxxx"

#Optional, name of a registered pushover client device, names are available on your dashboard. Default = "" (all devices)
export PUSHOVER_DEVICE="xxxxxxxx"

#Optional, Priority level of notification, Lowest Priority (-2), Low Priority (-1), Normal Priority (0), High Priority (1). Default=Normal Priority
export PUSHOVER_SOUND="x"
```

Ok, let's set notification hook:

```sh
acme.sh --set-notify  --notify-hook  pushover
```

The PUSHOVER_TOKEN, PUSHOVER_USER and PUSHOVER_SOUND will be saved in ~/.acme.sh/account.conf and will be reused when needed.

If there are any bugs for pushover.net notify, please report here: https://github.com/Neilpang/acme.sh/issues/2329

## 7. Set notification for IFTTT Webhooks

Send notification via IFTTT Webhooks so that you can make acme.sh work with tons of [IFTTT](https://ifttt.com) services.

Firstly, connect our IFTTT to Webhooks service at [https://ifttt.com/maker_webhooks](https://ifttt.com/maker_webhooks) and click "Documentation" in the top right corner to get the API key.

Secondly, create our IFTTT applet with Webhooks as `this' and whatever as `that', we'll setup the event name(e.g. acme_status) for this applet trigger.

Now we can set acme.sh notification hook:

```sh
#The API key.
export IFTTT_API_KEY="xxxx"

#Our event name, this should be same as the setting of your applet.
export IFTTT_EVENT_NAME="acme_status"

#Optional: the key of notification subject, available values are "value1", "value2", "value3", default "value1"
export IFTTT_SUBJECT_KEY="value1"

#Optional: the key of notification content, available values are "value1", "value2", "value3", default "value2"
export IFTTT_CONTENT_KEY="value2"

#Now we're ready to set notify hook
acme.sh --set-notify --notify-hook ifttt
```

The `IFTTT_API_KEY`, `IFTTT_EVENT_NAME`, `IFTTT_SUBJECT_KEY` and `IFTTT_CONTENT_KEY` will be saved in ~/.acme.sh/account.conf and will be reused when needed.

If there are any bugs for IFTTT Webhooks notify, please report here: https://github.com/Neilpang/acme.sh/issues/2421


## 8. Set notification for xmpp (aka jabber)

Install and `sendxmpp` manually or using your distributions package manager. Configure the sending account in `~/.sendxmpprc` e.g.:

```sh
username: example
jserver: example.com
password: xxxxxxxx
```

Set it in your systems environment:

```sh
export XMPP_TO="xxx@xxx.com"                         # the xmpp account to send notifications to
```

Currently only `sendxmpp` is supported for sending notifications but support for similar tools can be added easily.

If you want to specify which application to use, please use `XMPP_BIN`:

```sh
export XMPP_BIN="/usr/bin/sendxmpp"                  # optional: override command to send xmpp messages
export XMPP_BIN_ARGS="--tls-ca-path=/etc/ssl/certs'" # optional: arguments for the xmpp command
```

Ok, let's set notification hook:

```sh
acme.sh --set-notify  --notify-hook xmpp
```

The `XMPP_TO`, `XMPP_BIN` and `XMPP_BIN_ARGS` will be saved in ~/.acme.sh/account.conf and will be reused when needed.

On debian based systems `sendxmpp` has problems validating certificates (see https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=854210).
