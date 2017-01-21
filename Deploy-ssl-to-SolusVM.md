### 1. SolusVM master with nginx stack:

First of all, install acme.sh as described in the documentation.

#### 1) Issue your certificate:

acme.sh will try to validate your domain over http connection. That means the docroot is /usr/local/solusvm/www/.verification. Please check if you have this folder, else you can use /usr/local/solusvm/www as your docroot.

```
acme.sh --issue -d solusvm.yourdomain.com -w /usr/local/solusvm/www/.verification
```

Chenge 'solusvm.yourdomain.com' with your SolusVM master domain name. Remember you can add multiple domains (SANs) to your certificate using the -d option. Please check acme.sh wiki on how to do it.

#### 1) Install the issued certificate to your SolusVM master:
```
acme.sh --installcert -d solusvm.yourdomain.com --keypath /usr/local/svmstack/nginx/ssl/ssl.key \
--fullchainpath /usr/local/svmstack/nginx/ssl/ssl.crt --reloadcmd "service svmstack-nginx restart; \
/usr/local/svmstack/sshwebsocket/quit; /usr/local/svmstack/sshwebsocket/port_check; \
cd /usr/local/svmstack/nginx/ssl && cat ssl.key ssl.crt > ssl.pem"
```
This command will install the fullchain and the private key to /usr/local/svmstack/nginx/ssl/. After that, it will restart the web server, restart sshwebsocket (used for HTML5 console) and then generate the ssl.pem file needed for novnc websockify.

### 2. SolusVM master with lighttpd stack:
TODO