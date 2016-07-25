Setup and run acme.sh on your OpenWRT router and have https secured management.

### Step 1: Install packages

`opkg install curl ca-certificates px5g uhttpd-mod-tls`

### Step 2: Configure Web Server
Here we'll tell uhttpd to listen for https traffic and add two firewall rules to let that traffic through.
The HTTP port 80 rule will be disabled by default

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
uci set firewall.https=rule
uci set firewall.https.target=ACCEPT
uci set firewall.https.src=wan
uci set firewall.https.proto=tcp
uci set firewall.https.dest_port=80
uci set firewall.https.name='http web configuration'
uci set firewall.http.enabled=0
uci commit
/etc/init.d/firewall restart
/etc/init.d/uhttpd restart
```
