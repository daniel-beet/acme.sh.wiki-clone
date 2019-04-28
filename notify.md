# Set notification


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

```sh
export  MAILGUN_API_KEY="xxxxxxxx"
export  MAILGUN_API_DOMAIN="xxxxxx.com" 
export  MAILGUN_FROM="xxx@xxxxx.com"
export  MAILGUN_TO="yyyy@gmail.com"


export  MAILGUN_REGION="us|eu"          #optional, use "us" as default

acme.sh --set-notify  --notify-hook mailgun
```


## 2. Set notification for sendgrid.com

```sh
export SENDGRID_API_KEY="xxxxxxxxxx"
export SENDGRID_FROM="xxxx@cccc.com"
export SENDGRID_TO="xxxx@xxx.com"

acme.sh --set-notify  --notify-hook sendgrid
```


