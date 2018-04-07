Setup and run acme.sh on your OpenWRT router and have https secured management.

### Step 1: Install packages

`opkg install luci-ssl-openssl`

### Step 2: Configure Web Server
Here we'll tell uhttpd not redirect to https

These commands use the OpenWRT [`uci` command](https://wiki.openwrt.org/doc/uci), a brilliant way to parse, get, set, and edit values and sections from config files. It makes scripting OpenWRT a breeze.

```
uci set uhttpd.main.redirect_https=0
uci commit
/etc/init.d/uhttpd restart
```

### Step 3: Configure acme.sh and get your certificate
On your router:

```
curl https://raw.githubusercontent.com/Neilpang/acme.sh/master/acme.sh > acme.sh
chmod a+x "acme.sh"
./acme.sh --install
cd /root/.acme.sh
DOMAIN=my.router.net ## this domain must actually point to your router
iptables -I input_rule -p tcp --dport 80 -j ACCEPT -m comment --comment ACME
./acme.sh --issue -d $DOMAIN -w /www
iptables -D input_rule -p tcp --dport 80 -j ACCEPT -m comment --comment ACME
```

Now if the certificate issue was successful we'll tell the web server to use our new certificate:

```
uci set uhttpd.main.key="$(pwd)/$DOMAIN/$DOMAIN.key"
uci set uhttpd.main.cert="$(pwd)/$DOMAIN/$DOMAIN.cer"
uci commit uhttpd
/etc/init.d/uhttpd restart
```

### Step 4: Modify crontab (automatic renew)

Run `crontab -e` to edit your crontab (use something like `export EDITOR="/usr/bin/nano"` if vim isn't your style). Edit to:

`0 0 * * * "/root/.acme.sh"/acme.sh --cron --home "/root/.acme.sh" --pre-hook "iptables -I input_rule -p tcp --dport 80 -j ACCEPT -m comment --comment ACME" --post-hook "iptables -D input_rule -p tcp --dport 80 -j ACCEPT -m comment --comment ACME" --reloadcmd "/etc/init.d/uhttpd restart" >> /root/.acme.sh/log.txt 2>&1`


### Step 5: Configure Firewall
Open or Forward LUCI Port for external access. (use Webinterface)



## Other stuff which may help you:
https://forum.openwrt.org/viewtopic.php?id=65090

https://gist.github.com/t413/3e616611299b22b17b08baa517d2d02c

