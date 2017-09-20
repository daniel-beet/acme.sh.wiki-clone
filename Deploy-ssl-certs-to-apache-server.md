## 1. run acme.sh to copy the certificates to the correct location on the disk

### 1.1) create a sensible directory to store your apache certificates
I chose /etc/apache2/2.2/ssl

```
mkdir -p /etc/apache2/2.2/ssl
```

### 1.2) run acme.sh
A few notes:
* the parameters are stored in the .acme.sh configuration file, so get it right for your system as this file is read when the cron job runs
* "reloadcmd" is dependent on your operating system, system V Linux systems use the command "service apache2 force-reload", Solaris based systems use "svcadm restart apache22" or similar

```
acme.sh --install-cert -d online.domain.com \
--cert-file /etc/apache2/2.2/ssl/online.domain.com-cert.pem \
--key-file /etc/apache2/2.2/ssl/online.domain.com-key.pem \
--fullchain-file /etc/apache2/2.2/ssl/letsencrypt.pem \
--reloadcmd "service apache2 force-reload"
```

## 2. Set up your httpd.conf

There are so many ways to do this, it would take a long list to write every variant, however the specific codes you will need to set in your httpd.conf (or ssl.conf, or httpd-ssl.conf) are:

```
SSLCertificateFile /etc/apache2/2.2/ssl/online.domain.com-cert.pem
SSLCertificateKeyFile /etc/apache2/2.2/ssl/online.domain.com-key.pem
SSLCertificateChainFile "/etc/apache2/2.2/ssl/letsencrypt.pem"

SSLCACertificatePath "/etc/apache2/2.2/ssl/"
SSLCACertificateFile "/etc/apache2/2.2/ssl/letsencrypt.pem"
```
