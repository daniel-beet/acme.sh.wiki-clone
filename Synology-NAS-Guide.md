# HTTPS certificates for your Synology NAS using acme.sh

Since Synology introduced [Let's Encrypt](https://letsencrypt.org/), many of us benefit from free SSL. 

On the other hand, many of us don't want to expose port 80/443 to the Internet, including opening ports on the router. The alternative is to use the DNS-01 protocol. Sadly the Synology implementation of Let's Encrypt currently (1-Jan-2017) only supports the HTTP-01 method which requires exposing port 80 to the Internet. Also, if the domain of your NAS has an IPv6 AAAA record set, the Synology implementation of Let's Encrypt will fail.

But we can access the NAS via SSH and configure it to renew certs instead of using the web dashboard.

The following guide will use the DNS-01 protocol using the [Cloudflare API](https://api.cloudflare.com/), where I host my domain. However, [since acme.sh supports many DNS services](https://github.com/Neilpang/acme.sh/tree/master/dnsapi), you can also choose the one you like.

With the [Synology DSM deployhook](https://github.com/acmesh-official/acme.sh/wiki/deployhooks#20-deploy-the-cert-into-synology-dsm) included in 2.8.6, it is no longer required to run acme.sh on your Synology device to rotate the certificate. acme.sh just needs to be run on something that has access to the DSM's administrative interface. Additionally, the previous deployment methods can be drastically simplified with the following instructions.

## Installation of acme.sh

```sh
$ sudo -i
$ wget https://github.com/Neilpang/acme.sh/archive/master.tar.gz
$ tar xvf master.tar.gz
$ cd acme.sh-master/
$ ./acme.sh --install --nocron --home /usr/local/share/acme.sh --accountemail "email@gmailcom"
```

## Configuring DNS

For CloudFlare, we will set two environment variables that acme.sh (specifically, the `dns_cf` script from the `dnsapi` subdirectory) will read to set the DNS record. You can get your CloudFlare [API key here](https://dash.cloudflare.com/profile).

```sh
export CF_Key="MY_SECRET_KEY_SUCH_SECRET"
export CF_Email="myemail@example.com"
```

If you generated an API Token, instead of using your global account key, set CF_Token instead.

```sh
export CF_Token="MY_SECRET_TOKEN_SUCH_SECRET"
export CF_Email="myemail@example.com"
```

In case you use another DNS service, check the `dnsapi` directory. Instructions for many DNS providers are already included. You can also find instructions on how to add another DNS service there, although that requires some software development skills.

## Creating the certificate
Now it's time to create the certificate for your domain:

```sh
$ cd /usr/local/share/acme.sh
$ export CERT_DOMAIN="your-domain.tld"
$ export CERT_DNS="dns_cf"
$ ./acme.sh --issue -d "$CERT_DOMAIN" --dns "$CERT_DNS"
```

## Deploy the default certificate
We will use the [Synology DSM deployhook](https://github.com/acmesh-official/acme.sh/wiki/deployhooks#20-deploy-the-cert-into-synology-dsm) to deploy our certificate.  This will override the default certificate, in the next section you can see how to create new certificates to be used for other services.

```sh
$ cd /usr/local/share/acme.sh
# Single quotes prevents some escaping issues if your password or username contains certain special characters
$ export SYNO_Username='Admin_Username'
$ export SYNO_Password='Admin_Password!123'
# You must specify SYNO_Certificate, for the default certificate, we use an empty string
$ export SYNO_Certificate=""
$ ./acme.sh --deploy -d "$CERT_DOMAIN" --deploy-hook synology_dsm
```

### Deploying additional certificates

By specifying a different `SYNO_Certificate` and (optionally) `SYNO_Create`, we can deploy multiple certificates to the DSM.  These commands assume you are still working in the same terminal and have exported all other necessary variables described above.

```sh
$ ./acme.sh --issue -d "subdomain.$CERT_DOMAIN" --dns "$CERT_DNS"
# SYNO_Certificate is the description shown under Security -> Certificates in the DSM Control Panel
$ export SYNO_Certificate="A different certificate"
$ export SYNO_Create=1 # Says to create the certificate if it doesn't exist
$ ./acme.sh --deploy -d "subdomain.$CERT_DOMAIN" --deploy-hook synology_dsm
```

## Configuring Certificate Renewal
To auto renew the certificates in the future, you need to configure a task in the task scheduler. It is not advised to set this up as a custom cronjob (as was previously described in this wiki page) as the DSM security advisor will tell you that you have a critical warning regarding unknown cronjob(s).

In DSM control panel, open the 'Task Scheduler' and create a new scheduled task for a user-defined script.  

* General Setting: Task - Update default Cert. User - root
* Schedule: Setup a weekly renewal. For example, 11:00 am every saturday.
* Task setting: User-defined-script:

```sh
# renew certificates 
/usr/local/share/acme.sh/acme.sh --cron --home /usr/local/share/acme.sh/
```

## Fix a broken environment after Synology DSM upgrade

```sh
$ cd /usr/local/share/acme.sh
$ ./acme.sh --force --upgrade --nocron --home /usr/local/share/acme.sh
```

or manually add below line into /root/.profile

```sh
. "/usr/local/share/acme.sh/acme.sh.env"
```