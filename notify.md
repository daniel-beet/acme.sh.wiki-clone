# Set notifications

acme.sh can send notifications in its cronjob. 
Every night when the renew cronjob runs, you may receive notifications based on `notify-level` and `notify-mode`.

The settings on this page are used for testing and configuration of notifications. They should not be included in your cron job. To configure notifications, use the `--set-notify` argument. This will send test notifications and update account.conf with the new settings. 

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

The notifications can be emails or any other supported ways, such as requesting to a webhook, etc.

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
export MAIL_FROM="xxx@xxx.com" # or "Xxx Xxx <xxx@xxx.com>", currently works only with sendmail
export MAIL_TO="xxx@xxx.com"   # your account e-mail will be used as default if available
```

It will try to find and use `sendmail`, `ssmtp`, `mutt`, `mail` or `msmtp` automatically, if installed.
(If you don't have any of these, you may be able to use [smtp notifications](#12-set-notification-for-smtp) instead.)

If you want to specify which application to use, please use `MAIL_BIN`:

```sh
export MAIL_BIN="sendmail"     # should be one of following: sendmail, ssmtp, mutt, mail or msmtp (with or without path)
```

Use the given account instead of the account named "default" in `msmtp` command.

```sh
export MAIL_MSMTP_ACCOUNT="account"
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

Install `sendxmpp` manually or using your distributions package manager. Configure the sending account in `~/.sendxmpprc` e.g.:

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


## 9. Set notification for dingtalk.com(??????)

Push notification to dingtalk group by group robot webhook api.
First create a group robot, get your webhook url, and set a keyword.

???????????????????????????????????????. 
?????????????????????????????? webhook ?????????. ?????? webhook url. ??????????????? keyword. ???????????????????????????.

https://ding-doc.dingtalk.com/doc#/serverapi2/qf2nxq

```sh
export DINGTALK_WEBHOOK='https://oapi.dingtalk.com/robot/send?access_token=b05ccexxxxx'
export DINGTALK_KEYWORD=acme

acme.sh  --set-notify  --notify-hook dingtalk

```

## 10. Set notification for QQ with self-built CQHTTP API

### ????????????
?????? CoolQ ????????? CQHTTP ?????????????????? QQ???????????????????????? CQHTTP ?????????????????????????????? [CoolQ ??????](https://cqp.cc)??? [CQHTTP](https://cqhttp.cc) ????????????

CoolQ ????????????????????????????????? QQ ?????????????????????????????????????????????????????????????????????????????????????????? QQ ??????????????????????????????????????? QQ ???????????? QQ ????????????????????????????????????CQHTTP ??? CoolQ ????????????????????? CoolQ ????????? HTTP API???

?????? hook ?????????????????????????????????:

- `CQHTTP_TOKEN`: ?????????????????? CQHTTP ??????????????????????????? Access Token ?????????
- `CQHTTP_USER`: ?????????????????????????????? QQ ?????????????????????????????????????????????????????????????????? QQ ?????????????????????
- `CQHTTP_APIROOT`: ????????????????????? CQHTTP ???????????? URL (?????????????????????)???
- `CQHTTP_CUSTOM_MSGHEAD`: ???????????????????????????????????????????????? "A message from acme.sh:".

### English version

Push notifications to QQ via the plugin CQHTTP of CoolQ. A self-built CQHTTP server is needed. Visit [the community of CoolQ](https://cqp.cc) and [the docs of CQHTTP](https://cqhttp.cc) for the details of deployment.

CoolQ is a third-party headless QQ client, which provides a strong plugin system. Before we start, you need to prepare a QQ number for the robot. And ensure that your QQ number is a friend of the robot. CQHTTP is a plugin of CoolQ, which provides HTTP API for CoolQ.

This hook can parse four environment variables:

- `CQHTTP_TOKEN`: Recommend to be not empty, QQ application token, which is set in CQHTTP server.
- `CQHTTP_USER`: Required, QQ receiver ID. Make sure that the sender has right permission to send message to the receiver.
- `CQHTTP_APIROOT`: Required, CQHTTP Server URL (without slash suffix)
- `CQHTTP_CUSTOM_MSGHEAD`: Optional, custom message header. Default value is "A message from acme.sh:".

### Demo

```sh
export CQHTTP_TOKEN="Itsjustat0ken,qwq"       # That's the access token
export CQHTTP_USER="10086"     # That's your QQ number (receiver)
export CQHTTP_APIROOT="http://cqhttp-server.local:5700"     # That's your server address

acme.sh  --set-notify  --notify-hook cqhttp
```
After that, you'll receive a message (?????????????????????????????????????????????):
```plain
A message from acme.sh: Hello, this is a notification from acme.sh
If you receive this message, your notification works.
```

## 11. Set notification for Microsoft Teams

First get your [Microsoft Teams Webhook URL](https://docs.microsoft.com/en-us/microsoftteams/platform/concepts/connectors/connectors-using#setting-up-a-custom-incoming-webhook), then set it in your systems environment:

```sh
export TEAMS_WEBHOOK_URL=""
export TEAMS_THEME_COLOR="586069"
export TEAMS_SUCCESS_COLOR="2cbe4e"
export TEAMS_ERROR_COLOR="cb2431"
export TEAMS_SKIP_COLOR="586069"
```

Ok, let's set notification hook:

```
acme.sh --set-notify  --notify-hook teams
```

The `TEAMS_WEBHOOK_URL`, `TEAMS_THEME_COLOR`, `TEAMS_SUCCESS_COLOR`, `TEAMS_ERROR_COLOR` and `TEAMS_SKIP_COLOR` will be saved in ~/.acme.sh/account.conf and will be reused when needed.

To omit default color set variable value to any non xdigit character, eg. `TEAMS_SUCCESS_COLOR="-"`.


## 12. Set notification for SMTP

acme.sh can send email notifications by connecting directly to an SMTP mail server. Most commercial email service providers (ESPs) and corporate email systems support sending through SMTP, including Amazon SES, GSuite/Google Workspaces, Outlook.com, and others.

SMTP notification is available in acme.sh v2.8.9 or later.
Please report bugs in the SMTP notify hook in [issue #3358](https://github.com/acmesh-official/acme.sh/issues/3358).

SMTP notifications in acme.sh require Python 3.4 or later, Python 2.7, or curl on the machine where you run acme.sh. (If you don't have Python or curl, you may be able to use [mail notifications](#3-set-notification-for-mail) instead.)

First, get the SMTP connection information for your mail server or service. You'll need to know:
* the SMTP hostname (e.g., smtp.example.com) and port if non-standard (e.g., 587)
* type of secure connection required: "tls" (called _STARTTLS_ or _explicit TLS_), "ssl" (called _TLS wrapper_ or _implicit TLS_), or "none"
* whether authentication (login) is required, and if so the username and password to use

Set in your system environment:
```sh
# These are required:
export SMTP_FROM="from@example.com"  # just the email address (no display names)
export SMTP_TO="to@example.com,to2@example.net"  # just the email address, use commas between multiple emails
export SMTP_HOST="smtp.example.com"
export SMTP_SECURE="tls"  # one of "none", "ssl" (implicit TLS, TLS Wrapper), "tls" (explicit TLS, STARTTLS)

# The default port depends on SMTP_SECURE: none=25, ssl=465, tls=587.
# If your SMTP server uses a different port, set it:
export SMTP_PORT="2525"

# If your SMTP server requires AUTH (login), set:
export SMTP_USERNAME="<username>"
export SMTP_PASSWORD="<password>"

# acme.sh will try to use the python3, python2.7, or curl found on the PATH.
# If it can't find one, or to run a specific command, set:
export SMTP_BIN="/path/to/python_or_curl"

# If your SMTP server is very slow to respond, you may need to set:
export SMTP_TIMEOUT="30"  # seconds for SMTP operations to timeout, default 30
```


Ok, let's set notification hook:
```sh
acme.sh --set-notify --notify-hook smtp
```

If everything works, you will see a "success" message and receive a test email at the `SMTP_TO` address. 

If you get an error message, it should explain what went wrong somewhere. The exact problem may be mixed in with less helpful error codes, so read through the message carefully. For additional troubleshooting, run the command again with `--debug` or `--debug 2`. (At debug level 2 or higher the output will show the complete SMTP session transcript, which may include the SMTP password.)

All of the `SMTP_*` settings will be saved in ~/.acme.sh/account.conf and will be reused when needed.


## 13. Set notification for Telegram

To have notifications delivered via Telegram, you first need to [create a new Telegram bot](https://core.telegram.org/bots#creating-a-new-bot), by talking to [@BotFather](https://t.me/botfather) within your Telegram client. Save the bot token that is returned after creation.

Next, you need the `chat_id` of your Telegram account (or a group). The simplest way to do this is to start a conversation with your new bot, and send a simple test message to it. Then, using `curl`, fetch the [`getUpdates`](https://core.telegram.org/bots/api#getupdates) API endpoint, and look for the chat id in the json object for the message you sent. For example:

```sh
$ bot_token="...." # enter your new bot's API token here.
$ curl -s "https://api.telegram.org/bot${bot_token}/getUpdates" | python -mjson.tool
{
    "ok": true,
    "result": [
        {
            "message": {
                "chat": {
                    "first_name": "Joe",
                    "id": 12345678,            <----- This is the Chat ID.
                    "last_name": "Bloggs",
                    "type": "private",
                    "username": "joebloggs"
                },
                ......
                "text": "text of the test message you sent",
                ......
<snip rest of output>
$
```

Once you have both the API token, and the chat ID for where you want the Bot to set notifications, set the following two variables to be used by the notification hook script:

```sh
export TELEGRAM_BOT_APITOKEN="..."   # Token returned by @BotFather during bot creation above.
export TELEGRAM_BOT_CHATID="..."     # Chat ID fetched above.
```

And then set notification hook:

```
acme.sh --set-notify --notify-hook telegram
```
