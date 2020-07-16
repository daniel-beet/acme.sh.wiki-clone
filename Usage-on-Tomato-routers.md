This article describes using a router with Linux-based [Tomato firmware](https://en.wikipedia.org/wiki/Tomato_(firmware)) to run name-based HTTPS [reverse proxies](https://en.wikipedia.org/wiki/Reverse_proxy) with [Let's Encrypt](https://letsencrypt.org/) certificates, using [acme.sh](https://github.com/acmesh-official/acme.sh), providing encrypted access to home or small business LAN services from outside (untrusted) networks, such as your mobile devices.

Traffic to HTTPS port(s) (the usual 443 or whatever you use) in your public IP address will be forwarded to plain HTTP services on your LAN hosts with your Tomato router functioning as a reverse proxy. This way you can have multiple (sub)domains in a single public port pointed to several LAN servers with Tomato handling all the HTTPS stuff, which is not possible with simple port forwarding. A configuration example is provided.

Much of the setup is done through SSH, but you'll also need Tomato's web admin, marked in this guide as **Menu→Submenu**.

### Prerequisites
- A router with USB ports running [FreshTomato](https://www.linksysinfo.org/index.php?threads/fork-freshtomato-arm.74117/) or another recent Tomato fork with a fully featured OpenSSL and web server. A fast CPU and large NVRAM are recommended. There's an [unconfirmed report](https://github.com/acmesh-official/acme.sh/issues/1581#issuecomment-651678412) of MIPS-based routers having problems.
- Unless you happen to have a static public IP, you need a dynamic DNS (DDNS) service configured in Tomato. Some [DNS services](https://community.letsencrypt.org/t/dns-providers-who-easily-integrate-with-lets-encrypt-dns-validation/86438) also provide API control, enabling [DNS mode](https://github.com/acmesh-official/acme.sh/wiki/dnsapi) for acme.sh. You can point additional regular CNAME records to the DDNS hostname, so not all your hostnames need to be dynamic. In this guide _tomato.example.com_ and _www.tomato.example.com_ are used as examples.
- At least one plain HTTP web service or site running on either a LAN host or Tomato itself. It's a good idea to assign static IP addresses for servers (**Basic→Static DHCP/ARP/IPT**).

If you're going to [issue certificates](https://github.com/acmesh-official/acme.sh/wiki/How-to-issue-a-cert) using webroot mode, Tomato's web server must be running in port 80, so make sure your service provider doesn't block that port and that the web admin service is not using the same port.


**Standalone modes won't work**, as there's no `socat` in Tomato (without [entware](https://github.com/Entware/Entware/wiki)).


### Installing
Format a USB HDD or flash drive as ext4 (or ext2 if you don't need journaling) and name the partition as you wish. For this guide the partition was named "flash", so Tomato auto-mounts it to `/tmp/mnt/flash`. **Don't forget to change every path mentioned in this guide to match the name you choose.**

You could use Tomato's [JFFS partition](http://tomatousb.org/doc:jffs) instead of an external drive, but firmware upgrades need JFFS disabled, so it's rather inconvenient.

SSH to your Tomato and paste these commands to download and extract acme.sh:
```sh
cd /tmp/mnt/flash
wget https://github.com/acmesh-official/acme.sh/archive/master.zip
unzip master.zip
rm master.zip
cd acme.sh-master
chmod +x acme.sh
```
You're now ready to install. Change the email address before running this install command:
```sh
./acme.sh --install --nocron --home /tmp/mnt/flash/acme.sh \
--accountemail "your.email@example.com" --useragent "Tomato router"
```
Finally remove the installer directory: `cd .. && rm -Rf acme.sh-master`

The installer wrote a line to the `.profile` file in the root user's home directory. Tomato keeps this directory on a RAM disk that won't survive reboots, so you need to make this permanent by adding this command to **Administration→Scripts→Init**:
```sh
echo '. "/tmp/mnt/flash/acme.sh/acme.sh.env"' >> /tmp/home/root/.profile
```
Save the settings. Close the current SSH session and start a new one to activate the change.

Now go to **Administration→Scheduler**. Scheduled commands ignore the `.profile` file, so you need to provide the full path to acme.sh and set the directory options. Put this line in one of the custom command fields and set it to run daily, preferrably at a time when there's least traffic:
```sh
/tmp/mnt/flash/acme.sh/acme.sh --cron --home /tmp/mnt/flash/acme.sh --config-home /tmp/mnt/flash/acme.sh/conf
```
Save the scheduler settings.


### Configuring Tomato's web server
If you'll only use DNS mode, you don't need to set the port and path; they're for acme.sh's webroot mode.

Go to **Web Server→Basic Settings** and set it up like this:
- Check **Enable Server on Start** and **Allow Remote Access**
- **Run As**: Nobody (running as root is generally a bad idea)
- **Web Server Port**: 80
- **Server Root Path**: `/tmp/mnt/flash/www`

Save the settings and then create the directory for webroot challenges:
```sh
mkdir -p /tmp/mnt/flash/www/.well-known/acme-challenge
```


### Issuing certificates
There's nothing Tomato-specific about this, except that you can only use webroot or DNS mode. To avoid tripping failure limits, it's best to do a dry run first by adding the `--test` flag to the command. If the dry run works, your DNS and router configurations are OK.

Webroot mode:
```sh
acme.sh --issue -d tomato.example.com -d www.tomato.example.com -w /tmp/mnt/flash/www
```
DNS mode (see [the guide](https://github.com/acmesh-official/acme.sh/wiki/dnsapi)):
```sh
acme.sh --issue -d tomato.example.com -d www.tomato.example.com --dns dns_xxxx
```


### Installing certificates
Create a directory for your new certificate and install it there:
```sh
mkdir -p /tmp/mnt/flash/cert/tomato.example.com

acme.sh --install-cert -d tomato.example.com \
--fullchain-file /tmp/mnt/flash/cert/tomato.example.com/fullchain.pem \
--key-file /tmp/mnt/flash/cert/tomato.example.com/key.pem \
--reloadcmd "service enginex restart"
```
Note that Tomato has a funny typo, internally calling nginx "enginex".

Since nginx runs as user "nobody" you need to make the chain and key files readable by it. Change their owner group to nobody and allow group read permissions:
```
chown root:nobody /tmp/mnt/flash/cert/tomato.example.com/*
chmod 0640 /tmp/mnt/flash/cert/tomato.example.com/*
```
Tomato is a single-user system, so you don't need to worry about file permissions much. Just don't put any vulnerable PHP code on the web server; leaking private keys and altering NVRAM would be pretty bad.


### Adding a reverse proxy
Modify the below example to match your new hostname(s), certificate path and LAN server IP and port (on the `proxy_pass` line) and add it to the **HTTP Section** field in **Web Server→Advanced Settings**. This is a minimalist reverse proxy example, so read some guides to learn about more options, especially security-related.

If the server is Tomato itself, set `proxy_pass` to _http\://127.0.0.1:80_ (match the port number with web server setting).

You can add as many proxy server configurations as you wish, but note that they take up precious NVRAM, unless you move the whole nginx configuration to a file, disabling GUI settings. In many cases you can leave out the `proxy_set_header` lines, as they only provide connection info for logging etc.
```nginx
server {
  listen 443 ssl;
  server_name tomato.example.com www.tomato.example.com;
  ssl_certificate /tmp/mnt/flash/cert/tomato.example.com/fullchain.pem;
  ssl_certificate_key /tmp/mnt/flash/cert/tomato.example.com/key.pem;
  location / {
    proxy_set_header Host $http_host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_pass http://192.168.0.3:8080;
    proxy_redirect off;
  }
}
```
Save the settings and wait for Tomato to restart nginx. You should now be able to access the LAN HTTP server from outside through _https\://tomato.example.com/_


### Notes
- While testing this stuff I came across some situations where Tomato's RAM disk ran out of space, causing failures writing configuration files. I'm not sure what causes it, but you can check with `df -h /tmp`, and a reboot fixes it.
- Not all HTTP services are proxy-compatible. For example, absolute URLs can be troublesome, although you can fix them with [http_sub](https://nginx.org/en/docs/http/ngx_http_sub_module.html) filters.
- This should be obvious, but people can be surprisingly dumb: **Don't create public proxy connections to LAN devices/services without proper password protection.**