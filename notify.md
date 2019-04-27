# Set notification


```sh
acme.sh --set-notify  [--notify-hook mailgun]

acme.sh --set-notify  [--notify-level 2]
acme.sh --set-notify  [--notify-mode 0]
```


## 1. Set notification for mailgun.com

```sh
export  MAILGUN_API_KEY="xxxxxxxx"
export  MAILGUN_APIDOMAIN="xxxxxx.com" 
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


