Setup and run acme.sh on your OpenWRT router and have https secured management.

### Step 1: Install packages

`opkg install luci-ssl-openssl acme luci-app-acme`

If you want to use DNS-based certificate verificiation, also install the DNS providers:

`opkg install acme-dnsapi`

### Step 2: Configure Web Server
Here we'll tell uhttpd redirect to https

These commands use the OpenWRT [`uci` command](https://wiki.openwrt.org/doc/uci), a brilliant way to parse, get, set, and edit values and sections from config files. It makes scripting OpenWRT a breeze.

```
uci set uhttpd.main.redirect_https=1
uci commit
/etc/init.d/uhttpd restart
```

### Step 3: Configure acme.sh and get your certificate
On your router:

Navigate to `Services -> ACME certs` in LUCI and configure your certificate details. Make sure to select 'Use for uhttpd', and 'Enabled' for your configured certificate. If you prefer to use the command line, simply edit `/etc/config/acme`, and run `/etc/init.d/acme start` afterwards.

### Step 4: Configure Firewall
Open or Forward LUCI Port for external access. (use Webinterface)
