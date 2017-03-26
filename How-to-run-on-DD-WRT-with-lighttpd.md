This guide is written for a Kong build of DD-WRT, but should work with any that has access to lighttpd. You will need a JFFS partition and need to use command line tools via ssh/telnet.

1. **Install opkg and get CA certificates.**
    To install opkg, you need a /opt partition. The easiest way (and totally adequate for the goal of running lighttpd) is to mount /opt from a jffs partition, which is what we do here. First, enable JFFS from Administration->Management. The first time you use it you'll want to check the box to Clean internal flash storage. Once it's running (you may need to reboot), use a terminal to `mkdir /jffs/opt`, and mount it to the /opt directory using `mount --bind /jffs/opt /opt`. This line will also need to go into your startup script under Administration->Commands. Second, install opkg by running `bootstrap`. Third, install CA certificates which permit use of HTTPS from DD-WRT. Run `opkg update; opkg install ca-certificates`. Certificates are installed into /opt/etc/ssl/certs.

2. **Install acme.sh onto the JFFS.**
    Somewhat arbitrarily, I chose to put it, and related configuration, in /jffs/usr/ssl. To do this:
    ```bash
    mkdir /jffs/usr/ssl
    cd /jffs/usr/ssl
    curl --capath /opt/etc/ssl/certs https://raw.githubusercontent.com/Neilpang/acme.sh/master/acme.sh > acme.sh
    chmod a+x acme.sh
    ```
    Note that curl is using your newly installed root certificates to get acme.sh, as acme.sh will do to get your TLS certificates.

3. **Get a trusted certificate issued from LetsEncrypt.org for your domain(s).** In order to do this they need to authenticate that you control the domain in question. The simplest general way at present is to use a TLS service run by acme.sh. There are many other possible approaches, but some may not work easily on DD-WRT, either due to lack of support, or the fact that the DD-WRT web GUI normally uses the default HTTP port. DNS approaches could be good if your DNS provider is supported. Finally, [lighttpd authentication may be supported in future](https://github.com/Neilpang/acme.sh/issues/687). Refer to the acme.sh documentation for other approaches or more complicated domain setups.

    To issue yourself a certificate for the domain assigned to dd-wrt:
    ```sh
    ./acme.sh --issue --tls -d [ddwrtdomain] --home /jffs/usr/ssl --ca-path /opt/etc/ssl/certs \
              --pre-hook "stopservice lighttpd" --post-hook "startservice lighttpd"
    ```
    Note: Be sure to replace [ddwrtdomain] with your domain name. To test your configuration, always add the `--test` parameter, to avoid being locked out by letsencrypt.

4. **Configure lighttpd to use the certificates provided by acme/letsencrypt.** To do this you will need to modify the default lighttpd.conf used by DD-WRT. The simplest way to do this is to copy the default configuration to /jffs/etc (`mkdir /jffs/etc; cp /tmp/lighttpd.conf /jffs/etc`), and then modify it (placed in that directory, it will override the default settings). Then modify /tmp/lighttpd.conf (using vi), so that the SSL section looks like this:
    ```
    $SERVER["socket"] == ":443" {
    ssl.engine	= "enable"
    ssl.pemfile	= "/jffs/etc/lighttpd_ssl/hostkey.pem"
    ssl.ca-file	= "/jffs/etc/lighttpd_ssl/fullchain.crt"
    }
    ```
    If you want to just run an HTTPS server, without any HTTP server, you can simply put a `#` in front of the first and last lines there and change the server.port line to `server.port = 443`. Do not remove your HTTP port from the web GUI, as this will cause lighttpd to malfunction.

5. **Install your new certificates to the place lighttpd will find them.**
    ```sh
    ./acme.sh --install-cert -d [ddwrtdomain] --home /jffs/usr/ssl \
              --cert-file /jffs/etc/lighttpd_ssl/host.crt \
              --key-file  /jffs/etc/lighttpd_ssl/host.key \
              --fullchain-file /jffs/etc/lighttpd_ssl/fullchain.crt \
              --reloadcmd  "cat /jffs/etc/lighttpd_ssl/host.crt /jffs/etc/lighttpd_ssl/host.key > /jffs/etc/lighttpd_ssl/hostkey.pem"

    #restart the service to load the new configuration and certificates
    stopservice lighttpd; startservice lighttpd
    ```
    Lighttpd wants the SSL certificate and key in the same file; we use the reloadcmd to place them there. We use pre- and post- hooks to actually restart the service; this is necessary so that they will function properly when run from cron.

6. **Set up a cron job to update certificate automatically before it expires.** Under Administration/Management, add a line under Additional Cron Jobs:
    ```
    # sundays @4:05am, renew/install SSL certificates if necessary (restarting lighttpd)
    5 4 * * 0 root /jffs/usr/ssl/acme.sh --cron --home /jffs/usr/ssl >>/jffs/usr/ssl/cronlog.txt 2>&1
    ```
    LetsEncrypt recommends running daily although this script only runs weekly (but since LetsEncrypt certs last 90 days and will renew every 60, I don't see why it needs to run daily). It will only stop/restart lighttpd if a certificate may need to be re-issued, and will automatically issue and install it according to the settings you used in steps 3 and 5 above.

Voila! Your server is using a trusted certificate that will auto-renew.