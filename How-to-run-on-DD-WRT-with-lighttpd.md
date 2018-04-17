This guide is written for a Kong build of DD-WRT, but should work with any that has access to lighttpd. You will need a JFFS partition and need to use command line tools via ssh/telnet.

1. **Install opkg and get CA certificates.**
    To install opkg, you need a /opt partition. The easiest way (and totally adequate for the goal of running lighttpd) is to mount /opt from a jffs partition, which is what we do here. First, enable JFFS from Administration->Management. The first time you use it you'll want to check the box to Clean internal flash storage. Once it's running (you may need to reboot), use a terminal to `mkdir /jffs/opt`, and mount it to the /opt directory using `mount --bind /jffs/opt /opt`. This line will also need to go into your startup script under Administration->Commands. 

    Second, install opkg - either by running `bootstrap`, or (preferred if you need more apps) by [installing entware](https://www.dd-wrt.com/wiki/index.php/Installing_Entware). 
    
    Third, install CA certificates which permit use of HTTPS from DD-WRT, as well as the networking tool socat. Run `opkg update; opkg install ca-certificates socat`. Certificates are installed into `/opt/etc/ssl/certs`.

2. **Install acme.sh onto the JFFS.**
    Somewhat arbitrarily, I chose to put it, and related configuration, in `/jffs/usr/ssl`. To do this:
    ```bash
    mkdir /jffs/usr/ssl
    cd /jffs/usr/ssl
    curl --capath /opt/etc/ssl/certs https://raw.githubusercontent.com/Neilpang/acme.sh/master/acme.sh > acme.sh
    chmod a+x acme.sh
    ```
    Note that curl is using your newly installed root certificates to get acme.sh, as acme.sh will do to get your TLS certificates.

3. **Get a trusted certificate issued from LetsEncrypt.org for your domain(s).** In order to do this they need to authenticate that you control the domain in question. The simplest general way at present is to use <del>a TLS</del> an HTTP service run by acme.sh. [The TLS method is not currently supported by LetsEncrypt](https://community.letsencrypt.org/t/important-what-you-need-to-know-about-tls-sni-validation-issues/50811), so we need to use the HTTP port (80). This requires temporarily stopping the DD-WRT gui server and opening up the HTTP port on the firewall (assuming it's not already open).  Alternatively, DNS approaches could be better if your DNS provider is supported. Refer to the acme.sh documentation for other approaches or more compliifcated domain setups. Finally, [lighttpd authentication may be supported in future](https://github.com/Neilpang/acme.sh/issues/687).

    To issue yourself a certificate for the domain assigned to dd-wrt, using a temporary HTTP server:
    ```bash
    ./acme.sh --issue --standalone -d [ddwrtdomain] \
              --home /jffs/usr/ssl --ca-path /opt/etc/ssl/certs \
              --pre-hook "stopservice httpd && iptables -I INPUT -p tcp --dport http -j ACCEPT" \
              --post-hook "startservice httpd && iptables -D INPUT -p tcp --dport http -j ACCEPT"
    ```
    Note: **Be sure to replace [ddwrtdomain] with your domain name.** To test your configuration, always add the `--test` parameter, to avoid being locked out by letsencrypt. Note also that we're assuming that it is httpd, not lighttpd that is serving http requests by default (otherwise stopservice/startservice lighttpd, not httpd, and remove that command from step 5; you will also need to manually restart lighttpd once after that step to effectuate changes). If you use a complicated firewall setup, you may need to modify the iptables commands to open the http port.
    Finally, note that letsencrypt will not issue certificates for certain "public domains" which are not registered as such. In particular, this currently precludes the 85,000 domains served by `freedns.afraid.org` from being issued certificates.

4. **Configure lighttpd to use the certificates provided by acme/letsencrypt.** To do this you will need to modify the default lighttpd.conf used by DD-WRT. The simplest way to do this is to copy the default configuration to /jffs/etc (`mkdir /jffs/etc; cp /tmp/lighttpd.conf /jffs/etc`), and then modify it (placed in that directory, it will override the default settings). Then modify /jffs/etc/lighttpd.conf (using [vi](http://www.mcsr.olemiss.edu/seminars/BASIC%20VI%20TUTORIAL.pdf)), so that the SSL section looks like this:
    ```
    $SERVER["socket"] == ":443" {
    ssl.engine	= "enable"
    ssl.pemfile	= "/jffs/etc/lighttpd_ssl/hostkey.pem"
    ssl.ca-file	= "/jffs/etc/lighttpd_ssl/fullchain.crt"
    }
    ```
    If you want to just run an HTTPS server, without any HTTP server, you can simply put a `#` in front of the first and last lines there and change the server.port line to `server.port = 443`. Do not remove your HTTP port from the web GUI, as this will cause lighttpd to malfunction.

5. **Install your new certificates to the place lighttpd will find them.**
    ```bash
    ./acme.sh --install-cert -d [ddwrtdomain] --home /jffs/usr/ssl \
              --cert-file /jffs/etc/lighttpd_ssl/host.crt \
              --key-file  /jffs/etc/lighttpd_ssl/host.key \
              --fullchain-file /jffs/etc/lighttpd_ssl/fullchain.crt \
              --reloadcmd  "cat /jffs/etc/lighttpd_ssl/host.crt /jffs/etc/lighttpd_ssl/host.key > /jffs/etc/lighttpd_ssl/hostkey.pem; stopservice lighttpd; startservice lighttpd"
    ```
    Again, **replace [ddwrtdomain] with your domain name.** Lighttpd wants the SSL certificate and key in the same file; we use the reloadcmd to place them there and restart lighttpd. We use pre- and post- hooks to restart httpd; these commands are necessary so that they will function properly when run from cron.

6. **Set up a cron job to update certificate automatically before it expires.** Under Administration/Management, add a line under Additional Cron Jobs:
    ```
    # sundays @4:05am, renew/install SSL certificates if necessary (restarting httpd & lighttpd in that case)
    5 4 * * 0 root /jffs/usr/ssl/acme.sh --cron --home /jffs/usr/ssl >>/jffs/usr/ssl/cron.log 2>&1
    ```
    It will only stop/restart lighttpd if a certificate may need to be re-issued, and will automatically issue and install it according to the settings you used in steps 3 and 5 above. LetsEncrypt recommends running daily although this script only runs weekly (since LetsEncrypt certs currently last 90 days and will renew at most every 60, I don't see why it needs to run daily). 

Voila! Your server is using a trusted certificate that will auto-renew.