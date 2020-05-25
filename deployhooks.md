# Using deploy api

Before you can deploy your cert, you must [issue the cert first](https://github.com/Neilpang/acme.sh/wiki/How-to-issue-a-cert).

Here are the scripts to deploy the certs/key to the server/services.

## 1. Deploy the certs to your cpanel host

If you want to deploy using cpanel UAPI see 7.

(cpanel deploy hook is not finished yet, this is just an example.)



Then you can deploy now:

```sh
export DEPLOY_CPANEL_USER=myusername
export DEPLOY_CPANEL_PASSWORD=PASSWORD
acme.sh --deploy -d example.com --deploy-hook cpanel
```

## 2. Deploy ssl cert on kong proxy engine based on api

Before you can deploy your cert, you must [issue the cert first](https://github.com/Neilpang/acme.sh/wiki/How-to-issue-a-cert).
Currently supports Kong-v0.10.x.

```sh
acme.sh --deploy -d ftp.example.com --deploy-hook kong
```

## 3. Deploy the cert to remote server through SSH access

The ssh deploy plugin allows you to deploy certificates to a remote host
using SSH command to connect to the remote server.  The ssh plugin is invoked
with the following command...

```sh
acme.sh --deploy -d example.com --deploy-hook ssh
```
Prior to running this for the first time you must tell the plugin where
and how to deploy the certificates.  This is done by exporting the following
environment variables.  This is not required for subsequent runs as the
values are stored by acme.sh in the domain configuration files.

Required...
```
export DEPLOY_SSH_USER=username
```
Optional...
```
export DEPLOY_SSH_CMD=custom ssh command
export DEPLOY_SSH_SERVER=url or ip address of remote host
export DEPLOY_SSH_KEYFILE=filename for private key
export DEPLOY_SSH_CERTFILE=filename for certificate file
export DEPLOY_SSH_CAFILE=filename for intermediate CA file
export DEPLOY_SSH_FULLCHAIN=filename for fullchain file
export DEPLOY_SSH_REMOTE_CMD=command to execute on remote host
export DEPLOY_SSH_BACKUP=yes or no
```
Added in Acme release 2.8.6...
```
export DEPLOY_SSH_BACKUP_PATH=path on remote server to backup certificates
export DEPLOY_SSH_MULTI_CALL=yes or no
```
**DEPLOY_SSH_USER**
Username at the remote host that SSH will login with. Note that
SSH must be able to login to remote host without a password... SSH Keys
must have been exchanged with the remote host. Validate and test that you
can login to USER@URL from the host running acme.sh before using this script.

The USER@URL at the remote server must also have has permissions to write to
the target location of the certificate files and to execute any commands
(e.g. to stop/start services).

**DEPLOY_SSH_CMD**
You can customize the ssh command used to connect to the remote host. For example
if you need to connect to a specific port at the remote server you can set this
to, for example, "ssh -p 22" or to use `sshpass` to provide password inline
instead of exchanging ssh keys (this is not recommended, using keys is
more secure).  Defaults to "ssh -T"

**DEPLOY_SSH_SERVER**
URL or IP Address of the remote server.  If not provided then the domain
name provided on the acme.sh --deploy command line is used.
New in Acme release 2.8.7 this may be space separated list of servers to which exactly the
same deploy commands can be sent.

**DEPLOY_SSH_KEYFILE**
Target path and filename _on the remote server_ for the private key issued by LetsEncrypt.

**DEPLOY_SSH_CERTFILE**
Target path and filename _on the remote server_ for the certificate issued by LetsEncrypt.
If this is the same as the previous filename (for keyfile) then it is
appended to the same file.

**DEPLOY_SSH_CAFILE**
Target path and filename _on the remote server_ for the CA intermediate certificate issued by LetsEncrypt.
If this is the same as a previous filename (for keyfile or certfile) then
it is appended to the same file.

**DEPLOY_SSH_FULLCHAIN**
Target path and filename _on the remote server_ for the fullchain certificate issued by LetsEncrypt.
If this is the same as a previous filename (for keyfile, certfile or
cafile) then it is appended to the same file.

**DEPLOY_SSH_REMOTE_CMD**
Command to execute on the remote server after copying any certificates.  This
could be any additional command required for example to stop and restart
the service.

**DEPLOY_SSH_BACKUP**
Before writing a certificate file to the remote server the existing
certificate will be copied to a backup directory on the remote server.
By default these are placed in a hidden directory in the home directory of
the SSH user
```sh
~/.acme_ssh_deploy/[domain name]-backup-[timestamp]
```
Any backups older than 180 days will be deleted when new certificates
are deployed.  This defaults to "yes" set to "no" to disable backup.

**DEPLOY_SSH_BACKUP_PATH**
Path to directory _on the remote server_ into which to backup certificates
if DEPLOY_SSH_BACKUP is set to yes.  Defaults to ".acme_ssh_deploy" which
is a hidden directory in the home directory of the SSH user.

**DEPLOY_SSH_MULTI_CALL**
By default this plugin collects up all the required commands to be executed
on the remote server and sends them to the remote server in a single
SSH call.  This fails on some target servers if the command line buffer
is not long enough to hold all the data sent in SSH.  This is known to
affect servers using busybox.  By setting this value to "yes" the certificate
deployment process is split into multiple SSH calls to work around this problem.

### Examples using SSH deploy
The following example illustrates deploying certificates to a QNAP NAS
(tested with QTS version 4.2.3)

```sh
export DEPLOY_SSH_USER="admin"
export DEPLOY_SSH_KEYFILE="/etc/stunnel/stunnel.pem"
export DEPLOY_SSH_CERTFILE="/etc/stunnel/stunnel.pem"
export DEPLOY_SSH_CAFILE="/etc/stunnel/uca.pem"
export DEPLOY_SSH_REMOTE_CMD="/etc/init.d/stunnel.sh restart"

acme.sh --deploy -d qnap.example.com --deploy-hook ssh
```
Note how in this example both the private key and certificate point to
the same file.  This will result in the certificate being appended
to the same file as the private key... a common requirement of several
services.

The next example illustrates deploying certificates to regular linux server with certbot and nginx installed
```sh
export DEPLOY_SSH_USER="root"
export DEPLOY_SSH_SERVER="example.com"
export DEPLOY_SSH_KEYFILE="/etc/letsencrypt/archive/example.com/privkey2.pem"
export DEPLOY_SSH_FULLCHAIN="/etc/letsencrypt/archive/example.com/fullchain2.pem"
export DEPLOY_SSH_CAFILE="/etc/letsencrypt/archive/example.com/cert2.pem"
export DEPLOY_SSH_REMOTE_CMD="systemctl restart nginx"
acme.sh --deploy -d example.com --deploy-hook ssh
```

The next example illustrates deploying certificates to a Unifi
Controller (tested with version 5.12.72).

```sh
export DEPLOY_SSH_USER="root"
export DEPLOY_SSH_SERVER="unifi.example.com"
export DEPLOY_SSH_KEYFILE="/var/lib/unifi/unifi.example.com.key"
export DEPLOY_SSH_FULLCHAIN="/var/lib/unifi/unifi.example.com.cer"
export DEPLOY_SSH_REMOTE_CMD="DIR=/var/lib/unifi && FQDN=unifi.example.com \
 && openssl pkcs12 -export \
   -inkey $DIR/$FQDN.key -in $DIR/$FQDN.cer -out $DIR/$FQDN.p12 \
   -name ubnt -password pass:aircontrolenterprise \
 && keytool -delete -alias unifi -keystore $DIR/keystore \
   -deststorepass aircontrolenterprise \
 && keytool -importkeystore -deststorepass aircontrolenterprise \
   -destkeypass aircontrolenterprise \
   -destkeystore $DIR/keystore -srckeystore /$DIR/$FQDN.p12 \
   -srcstoretype PKCS12 -srcstorepass temppass -alias ubnt -noprompt \
 && chown -R unifi:unifi $DIR/keystore
 && service unifi restart"
export DEPLOY_SSH_MULTI_CALL="yes"

acme.sh --deploy -d unifi.example.com --deploy-hook ssh
```
In this example we execute several commands on the remote host
after the certificate files have been copied... to generate a pkcs12 file
compatible with Unifi, to import it into the Unifi keystore and then finally
to restart the service.

Note also that once the certificate is imported
into the keystore the individual certificate files are no longer
required. We could if we desired delete those files immediately. If we
do that then we should disable backup at the remote host (as there are
no files to backup -- they were erased during deployment). For example...
```sh
export DEPLOY_SSH_BACKUP=no
# modify the end of the remote command...
&& rm /var/lib/unifi/unifi.example.com.key \
      /var/lib/unifi/unifi.example.com.cer \
      /var/lib/unifi/unifi.example.com.p12 \
&& service unifi restart
```

The next example illustrates deploying certificates to VMware ESXi (tested with version 6.7u3).
Requires Acme 2.8.6 or later.

Note that by default ESXi hosts have ssh access disabled and VMware recommends
only enabling it if necessary for administrative purposes - displaying a
warning notice on the ESXi web interface.  You must enable ssh on ESXi and have
exchanged ssh keys for this deploy hook to work.

```sh
export DEPLOY_SSH_USER="root"
export DEPLOY_SSH_SERVER="vmwareesxi.example.com"
export DEPLOY_SSH_KEYFILE="/etc/vmware/ssl/rui.key"
export DEPLOY_SSH_FULLCHAIN="/etc/vmware/ssl/rui.crt"
export DEPLOY_SSH_REMOTE_CMD="/etc/init.d/hostd restart"
export DEPLOY_SSH_MULTI_CALL="yes"
acme.sh --deploy -d vmwareesxi.example.com --deploy-hook ssh
```

## 4. Deploy the cert to local vsftpd server

```sh
acme.sh --deploy -d ftp.example.com --deploy-hook vsftpd
```

The default vsftpd conf file is `/etc/vsftpd.conf`,  if your vsftpd conf is not in the default location, you can specify one:

```sh
export DEPLOY_VSFTPD_CONF="/etc/vsftpd.conf"

acme.sh --deploy -d ftp.example.com --deploy-hook vsftpd
```

The default command to restart vsftpd server is `service vsftpd restart`, if it doesn't work, you can specify one:

```sh
export DEPLOY_VSFTPD_RELOAD="/etc/init.d/vsftpd restart"

acme.sh --deploy -d ftp.example.com --deploy-hook vsftpd
```

## 5. Deploy the cert to local exim4 server

```sh
acme.sh --deploy -d ftp.example.com --deploy-hook exim4
```

The default exim4 conf file is `/etc/exim/exim.conf`,  if your exim4 conf is not in the default location, you can specify one:

```sh
export DEPLOY_EXIM4_CONF="/etc/exim4/exim4.conf.template"

acme.sh --deploy -d ftp.example.com --deploy-hook exim4
```

The default command to restart exim4 server is `service exim4 restart`, if it doesn't work, you can specify one:

```sh
export DEPLOY_EXIM4_RELOAD="/etc/init.d/exim4 restart"

acme.sh --deploy -d ftp.example.com --deploy-hook exim4
```

## 6. Deploy the cert to OSX Keychain

```sh
acme.sh --deploy -d ftp.example.com --deploy-hook keychain
```

## 7. Deploy to cpanel host using UAPI

This hook is using UAPI and works in cPanel & WHM version 56 or newer.
```
acme.sh  --deploy  -d example.com  --deploy-hook cpanel_uapi
```
DEPLOY_CPANEL_USER is required only if you run the script as root and it should contain cpanel username.
```sh
export DEPLOY_CPANEL_USER=username
acme.sh  --deploy  -d example.com  --deploy-hook cpanel_uapi
```
Please note, that the cpanel_uapi hook will deploy only the first domain when your certificate will automatically renew. Therefore you should issue a separate certificate for each domain. 

## 8. Deploy the cert to your FRITZ!Box router

You must specify the credentials that have administrative privileges on the FRITZ!Box in order to deploy the certificate, plus the URL of your FRITZ!Box, through the following environment variables:
```sh
$ export DEPLOY_FRITZBOX_USERNAME=my_username
$ export DEPLOY_FRITZBOX_PASSWORD=the_password
$ export DEPLOY_FRITZBOX_URL=https://fritzbox.example.com
```

After the first deployment, these values will be stored in your $HOME/.acme.sh/account.conf. You may now deploy the certificate like this:

```sh
acme.sh --deploy -d fritzbox.example.com --deploy-hook fritzbox
```

## 9. Deploy the cert to strongswan

```sh
acme.sh --deploy -d ftp.example.com --deploy-hook strongswan
```

## 10. Deploy the cert to HAProxy

You must specify the path where you want the concatenated key and certificate chain written.
```sh
export DEPLOY_HAPROXY_PEM_PATH=/etc/haproxy
```

You may optionally define the command to reload HAProxy. The value shown below will be used as the default if you don't set this environment variable.

```sh
export DEPLOY_HAPROXY_RELOAD="/usr/sbin/service haproxy restart"
```

You can then deploy the certificate as follows
```sh
acme.sh --deploy -d haproxy.example.com --deploy-hook haproxy
```

The path for the PEM file will be stored with the domain configuration and will be available when renewing, so that deploy will happen automatically when renewed.

## 11. Deploy your cert to Gitlab pages

You must define the API key and the informations for the project and Gitlab page you are updating the certificate for.

```sh
# The token can be created in your user settings under "Access Tokens"
export GITLAB_TOKEN="xxxxxxxxxxx"

# The project ID is displayed on the home page of the project
export GITLAB_PROJECT_ID=12345678

# The domain must match the one defined for the Gitlab page, without "https://"
export GITLAB_DOMAIN="www.mydomain.com"
```

You can then deploy the certificate as follows

```sh
acme.sh --deploy -d www.mydomain.com --deploy-hook gitlab
```

## 12. Deploy your cert to Hashicorp Vault

```sh
export VAULT_PREFIX="acme"
```

You can then deploy the certificate as follows

```sh
acme.sh --deploy -d www.mydomain.com --deploy-hook vault_cli
```

Your certs will be saved in Vault using this structure:

```sh
vault write "${VAULT_PREFIX}/${domain}/cert.pem"      value=@"..."
vault write "${VAULT_PREFIX}/${domain}/cert.key"      value=@"..."
vault write "${VAULT_PREFIX}/${domain}/chain.pem"     value=@"..."
vault write "${VAULT_PREFIX}/${domain}/fullchain.pem" value=@"..."
```

You might be using Fabio load balancer (which can get certs from
Vault). It needs a bit different structure of your certs in Vault. It
gets certs only from keys that were saved in `prefix/domain`, like this:

```bash
vault write <PREFIX>/www.domain.com cert=@cert.pem key=@key.pem
```

If you want to save certs in Vault this way just set "FABIO" env
variable to anything (ex: "1") before running `acme.sh`:

```sh
export FABIO="1"
```

## 13. Deploy your certificate to Qiniu.com

使用 acme.sh 部署到七牛之前，需要确保部署的域名已打开 HTTPS 功能，您可以访问[融合 CDN - 域名管理](https://portal.qiniu.com/cdn/domain) 设置。
另外还需要先导出 AK/SK 环境变量，您可以访问[密钥管理](https://portal.qiniu.com/user/key) 获得。

```sh
$ export QINIU_AK="foo"
$ export QINIU_SK="bar"
```

完成准备工作之后，您就可以通过下面的命令开始部署 SSL 证书到七牛上：

```sh
$ acme.sh --deploy -d example.com --deploy-hook qiniu
```

假如您部署的证书为泛域名证书，您还需要设置 `QINIU_CDN_DOMAIN` 变量，指定实际需要部署的域名(请注意泛域名前的点)：

```sh
$ export QINIU_CDN_DOMAIN=".cdn.example.com"
$ acme.sh --deploy -d example.com --deploy-hook qiniu
```

假如需要部署多个域名, 使用空格将域名分隔开来:

```sh
$ export QINIU_CDN_DOMAIN="cdn1.example.com cdn2.example.com"
$ acme.sh --deploy -d example.com --deploy-hook qiniu
```

### English version

You should create AccessKey/SecretKey pair in https://portal.qiniu.com/user/key 
before deploying your certificate, and please ensure you have enabled HTTPS for
your domain name. You can enable it in https://portal.qiniu.com/cdn/domain.

```sh
$ export QINIU_AK="foo"
$ export QINIU_SK="bar"
```

then you can deploy certificate by following command:

```sh
$ acme.sh --deploy -d example.com --deploy-hook qiniu
```

(Optional), If you are using wildcard certificate,
you may need export `QINIU_CDN_DOMAIN` to specify which domain
you want to update (please note the leading dot):

```sh
$ export QINIU_CDN_DOMAIN=".cdn.example.com"
$ acme.sh --deploy -d example.com --deploy-hook qiniu
```

If you want to deploy more than one domain, just use space to splite them:

```sh
$ export QINIU_CDN_DOMAIN="cdn1.example.com cdn2.example.com"
$ acme.sh --deploy -d example.com --deploy-hook qiniu
```

## 14. Deploy your cert on MyDevil.net

Once you have acme.sh installed and certificate issued (see info in [DNS API](https://github.com/Neilpang/acme.sh/wiki/dnsapi#66-use-mydevilnet)), you can install it by following command:

```sh
acme.sh --deploy --deploy-hook mydevil -d example.com
```

That will remove old certificate and install new one.

## 15. Deploy your cert to local mailcow server

You can install your certificates to a local [mailcow](https://github.com/mailcow/mailcow-dockerized/) instance. The
deploy hook will copy the certificates and reload the containers, that use the certificates (`postfix-mailcow`
`dovecot-mailcow` and `nginx-mailcow`).

```sh
$ export DEPLOY_MAILCOW_PATH="/path/to/mailcow"
$ acme.sh --deploy -d example.com --deploy-hook mailcow
```

The default command to restart is `docker-compose restart postfix-mailcow dovecot-mailcow nginx-mailcow`, if you want a
custom restart command, specify it by setting `DEPLOY_MAILCOW_RELOAD`:

```sh
$ export DEPLOY_MAILCOW_PATH="/path/to/mailcow"
$ export DEPLOY_MAILCOW_RELOAD="docker-compose restart"
$ acme.sh --deploy -d example.com --deploy-hook mailcow
```

## 16. Deploy the cert to G-Core CDN service

Deploy the cert to G-Core CDN service (https://gcorelabs.com/ru/) using the G-Core Labs API (https://docs.gcorelabs.com/cdn/).

Then you can deploy now:

```sh
export DEPLOY_GCORE_CDN_USERNAME=myusername
export DEPLOY_GCORE_CDN_PASSWORD=mypassword
acme.sh --deploy -d example.com --deploy-hook gcore_cdn
```
## 17. Deploy the cert to remote routeros

```sh
acme.sh --deploy -d ftp.example.com --deploy-hook routeros
```

Before you can deploy the certificate to router os, you need
to add the id_rsa.pub key to the routeros and assign a user
to that key.

The user need's to have the following policies enabled:
ssh, ftp, read, write, password and sensitive.

There are no need to enable ftp service for the script to work,
as they are transmitted over SCP, however ftp is needed to store
the files on the router.

Then you need to set the environment variables for the
deploy script to work.

```sh
export ROUTER_OS_USERNAME=certuser
export ROUTER_OS_HOST=router.example.com

acme.sh --deploy -d ftp.example.com --deploy-hook routeros
```

The deploy script will remove previously deployed certificates,
and it does this with an assumption on how RouterOS names imported
certificates, adding a "cer_0" suffix at the end. This is true for
versions 6.32 -> 6.41.3, but it is not guaranteed that it will be
true for future versions when upgrading.

If the router have other certificates with the same name as the one
beeing deployed, then this script will remove those certificates.

At the end of the script, the services that use those certificates
could be updated. Currently only the www-ssl service is beeing
updated, but more services could be added.

For instance:
```sh
export ROUTER_OS_ADDITIONAL_SERVICES="/ip service set api-ssl certificate=$_cdomain.cer_0"
```
returns 0 means success, otherwise error.

If there are any bugs for routeros hook, 

please report here: https://github.com/Neilpang/acme.sh/issues/2344

## 18. Deploy the cert into docker containers.

See:  
https://github.com/Neilpang/acme.sh/wiki/deploy-to-docker-containers

## 19. Deploy the cert into Palo Alto Networks Firewall.

In PAN-OS 9.1+ create a new admin role with API permissions to import and commit. Create a user that will only be used for the purpose of deploying certs. Assign this user to the role you created. 

For prior versions of PAN-OS the admin must have superuser access to upload the private key. 

```
export PANOS_USER="your_cert_user"
export PANOS_PASS="your_password"
export PANOS_HOST="10.0.0.1" // Replace with Firewall/Panorama Host
acme.sh --deploy -d example.com --deploy-hook panos --insecure
```

**Note:** after a successful deploy you can remove these environment variables as they will be stored by acme.sh. If the password for the user changes you will need to set the variables again. You can also remove --insecure if you deployed a cert and configured it as management cert.


## 20. Deploy the cert into Synology DSM

```sh
# export SYNO_Scheme="http" # Can be set to HTTPS, defaults to HTTP
# export SYNO_Hostname="localhost" # Specify if not using on localhost
# export SYNO_Port="5000" # Port of DSM WebUI, defaults to 5000 for HTTP and 5001 for HTTPS
export SYNO_Username="DSM_Admin_Username"
export SYNO_Password="DSM_Admin_Password"
export SYNO_Certificate="acme.sh certificate" # Description text in Control Panel -> Security -> Certificates
export SYNO_Create=1 # defaults to off, this setting is not saved.  By setting to 1 we create the certificate if it's not in DSM
acme.sh --deploy -d example.com --deploy-hook synology_dsm
```

Certificate should now show up in "Control Panel" -> "Security" -> "Certificates" and can be assigned to Services or set as the default certificate.

**With OTP (2-Factor-Authentication)**

Use your browser to sign in with the admin account you want to use. When entering the OTP code, check the "Save this device" checkbox and continue. Get the device ID from the cookie `did` (Left click on the lock to the left side of the URL -> Cookies and Copy the content of the `did` cookie). Set the environment variable to the cookie value:
```sh
export SYNO_DID=A1Bjk...
```