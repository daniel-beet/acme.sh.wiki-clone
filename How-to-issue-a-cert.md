### 1. Single domain:

#### 1) Webroot mode:
If you already have a web server running, you should use webroot mode.  you only need write access to the web root folder.

```sh
acme.sh  --issue  -d example.com  -w /home/wwwroot/example.com
```

#### 2) Standalone mode:

If you don't have a web server,  maybe you are on a smtp or ftp server, the 80 port is free. you can use standalone mode.
acme.sh has a builtin standalone webserver, it can listen at 80 port to issue the cert.

```sh
acme.sh  --issue  -d example.com  --standalone
```
If you are using a non-standard `80` port behind a reverse proxy or load balancer , you can use `--httpport` to specify your port:

```sh
acme.sh  --issue  -d example.com  --standalone --httpport 88
```


#### 3) DNS API mode:

Yes, if your nameservice provider has an api, we can use the api to automatically add the txt record for you. your cert will be automatically issued and renewed.

Cloud flare api:
```sh
export CF_Key="sdfsdfsdfljlbjkljlkjsdfoiwje"

export CF_Email="xxxx@sss.com"

acme.sh  --issue  -d example.com  --dns dns_cf
```

How to use dns api:  https://github.com/Neilpang/acme.sh/tree/master/dnsapi

#### 4) DNS manual mode:

You can also issue a cert by adding a TXT record to your domain.

```sh
acme.sh  --issue  -d example.com  --dns
```

This will tell you to the DNS record to add. Once done, re-run with `--renew` to get the signed certificate:

```sh
acme.sh  --renew  -d example.com  --dns
```

**Take care,  this is dns manual mode, it can not be renewed automatically.  you will have to add a new txt record to your domain by your hand when you renew your cert.**



#### 5) Apache mode:

If your website is running apache server, acme.sh can use apache server to issue cert. And acme.sh will restore your apache conf after the cert is issued,  don't worry.

```sh
acme.sh  --issue  -d example.com  --apache
```

#### 6) Nginx mode:

If your website is running nginx server, acme.sh can use nginx server to issue cert. And acme.sh will restore your nginx conf after the cert is issued,  don't worry.

```
acme.sh  --issue  -d example.com  --nginx
```

### 2. Multiple domains, SAN mode

Issue a single cert including multiple domains.  All the domains use the same validation method:

#### 1) Webroot mode:
You must point `example.com` and `www.example.com` to the same web root folder `/home/wwwroot/example.com`
```
acme.sh  --issue  -d example.com  -w /home/wwwroot/example.com   -d www.example.com
```

#### 2) Standalone mode:
```
acme.sh  --issue  -d example.com  --standalone  -d www.example.com 
```

#### 3) Dns api mode:
Cloud flare api:
```
export CF_Key="sdfsdfsdfljlbjkljlkjsdfoiwje"

export CF_Email="xxxx@example.com"

acme.sh  --issue  -d example.com  --dns dns_cf  -d www.example.com
```

#### 4) Dns manual mode:
```
acme.sh  --issue  -d example.com  --dns  -d www.example.com
```

### 3. Multiple domains, SAN mode,  Hybrid mode

Issue a single cert including multiple domains.  Each domain uses a different validation method.


```
acme.sh  --issue  \
-d aa.com  -w /home/wwwroot/aa.com \
-d bb.com  --dns dns_cf \
-d cc.com  --apache \
-d dd.com  -w /home/wwwroot/dd.com
```



