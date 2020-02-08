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


## 9. Set notification for dingtalk.com(钉钉)

Push notification to dingtalk group by group robot webhook api.
First create a group robot, get your webhook url, and set a keyword.

推送通知到钉钉群聊天机器人. 
先在群设置中添加一个 webhook 机器人. 获得 webhook url. 并设置一个 keyword. 目前不支持签名模式.

https://ding-doc.dingtalk.com/doc#/serverapi2/qf2nxq

```sh
export DINGTALK_WEBHOOK='https://oapi.dingtalk.com/robot/send?access_token=b05ccexxxxx'
export DINGTALK_KEYWORD=acme

acme.sh  --set-notify  --notify-hook dingtalk

```

## 10. Set notification for QQ with self-built CQHTTP API

### 中文说明
通过 CoolQ 的插件 CQHTTP 将消息推送到 QQ。需要您自行部署 CQHTTP 服务端。部署详情参见 [CoolQ 社区](https://cqp.cc)和 [CQHTTP](https://cqhttp.cc) 的文档。

CoolQ 是一个第三方开发的无头 QQ 客户端，并提供了一些插件功能。在开始之前，您需要自行准备一个 QQ 号码作为机器人，并保证您的 QQ 和机器人 QQ 是好友关系以便发送通知。CQHTTP 是 CoolQ 的一个插件，为 CoolQ 提供了 HTTP API。

这个 hook 有四个环境变量可供传入:

- `CQHTTP_TOKEN`: 必需，将 CQHTTP 配置文件中填写的 Access Token 填入。如果您没有设置 Access Token，请将其设为 `__ACME_SH_TOKEN_EMPTY__`。
- `CQHTTP_USER`: 必需，接收推送通知的 QQ 号码。您需要自行保证机器人号码可以向接收者的 QQ 号码发送消息。
- `CQHTTP_APIROOT`: 必需，您搭建的 CQHTTP 服务器的 URL (不包含斜杠结尾)。
- `CQHTTP_CUSTOM_MSGHEAD`: 可选，自定义的消息开头。默认值是 "A message from acme.sh:".

### English version

Push notifications to QQ via the plugin CQHTTP of CoolQ. a self-built CQHTTP server is needed, visit [the community of CoolQ](https://cqp.cc) and [the docs of CQHTTP](https://cqhttp.cc) for the details of deployment.

CoolQ is a third-party headless QQ client, which provides a strong plugin system. Before we start, you need to prepare a QQ number for the robot. And ensure that your QQ number is a friend of the robot. CQHTTP is a plugin of CoolQ, which provides HTTP API for CoolQ.

This hook can parse four environment variables:

- `CQHTTP_TOKEN`: Required, QQ application token, which is set in CQHTTP server. If it's empty, please set it to `__ACME_SH_TOKEN_EMPTY__`.
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
After that, you'll recieve a message (在这之后，你将收到一条信息如下):
```plain
A message from acme.sh: Hello, this is a notification from acme.sh
If you receive this message, your notification works.
```





