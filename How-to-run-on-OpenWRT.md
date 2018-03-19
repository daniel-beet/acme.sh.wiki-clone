Setup and run acme.sh on your OpenWRT router and have https secured management.

*Also see [Step 5](https://github.com/Neilpang/acme.sh/wiki/How-to-run-on-OpenWRT#meta-step-5-use-a-fancy-setup-script-instead) for a handy setup script instead*

### Step 1: Install packages

`opkg install curl ca-certificates uhttpd-mod-tls openssl-util`

On LEDE 17.01.2 you will need

`opkg install curl ca-certificates ca-bundle luci-ssl`

### Step 2: Configure Web Server
Here we'll tell uhttpd to listen for https traffic and add two firewall rules to let that traffic through.

These commands use the OpenWRT [`uci` command](https://wiki.openwrt.org/doc/uci), a brilliant way to parse, get, set, and edit values and sections from config files. It makes scripting OpenWRT a breeze.

```
uci set uhttpd.main.redirect_https=1
uci set uhttpd.main.listen_https='[::]:443'
##add https firewall rule
uci set firewall.https=rule
uci set firewall.https.target=ACCEPT
uci set firewall.https.src=wan
uci set firewall.https.proto=tcp
uci set firewall.https.dest_port=443
uci set firewall.https.name='web configuration'
##add http firewall rule
uci set firewall.http=rule
uci set firewall.http.target=ACCEPT
uci set firewall.http.src=wan
uci set firewall.http.proto=tcp
uci set firewall.http.dest_port=80
uci set firewall.http.name='http web configuration'
uci commit
/etc/init.d/firewall restart
/etc/init.d/uhttpd restart
```

### Step 3: Configure acme.sh and get your certificate
On your router:

```
mkdir ~/.https
curl https://raw.githubusercontent.com/Neilpang/acme.sh/master/acme.sh > acme.sh
chmod a+x "acme.sh"
./acme.sh --install
cd /root/.acme.sh
DOMAIN=my.router.net ## this domain must actually point to your router
./acme.sh --issue -d $DOMAIN -w /www
```

Now if the certificate issue was successful we'll tell the web server to use our new certificate:

```
uci set uhttpd.main.key="$(pwd)/$DOMAIN/$DOMAIN.key"
uci set uhttpd.main.cert="$(pwd)/$DOMAIN/$DOMAIN.cer"
uci commit uhttpd
/etc/init.d/uhttpd restart
```

### Step 4: Run acme.sh automatically every day

Run `crontab -e` to edit your crontab (use something like `export EDITOR="/usr/bin/nano"` if vim isn't your style). Add:

`0 0 * * * /root/.https/acme.sh --cron --home /root/.https >>/root/.https/log.txt 2>&1`

### Meta-Step 5: Use a fancy setup script instead

See [this gist](https://gist.github.com/t413/3e616611299b22b17b08baa517d2d02c) for my `update.sh` that handles each step of this howto for you and also runs the cron update.. with style. (It automates opening port-80 traffic for verification then closes it again so http isn't even an option)



## Here is another guide which may help you:
https://forum.openwrt.org/viewtopic.php?id=65090


