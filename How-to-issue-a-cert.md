### 1. Single domain:

#### 1) Webroot mode:
```
acme.sh  --issue  -d aa.com  -w /home/wwwroot/aa.com
```

#### 2) Standalone mode:
```
acme.sh  --issue  -d aa.com  --standalone
```
If you are using a non-standard `80` port behind a reverse proxy or load balancer , you can use `--httpport` to specify your port:

```
acme.sh  --issue  -d aa.com  --standalone --httpport 88
```

#### 3) Standalone tls mode:
```
acme.sh  --issue  -d aa.com  --tls
```

If you are using a non-standard `443` port behind a reverse proxy or load balancer , you can use `--tlsport` to specify your port:

```
acme.sh  --issue  -d aa.com  --tls  --tlsport 8443
```


#### 4) Dns manual mode:
```
acme.sh  --issue  -d aa.com  --dns
```

#### 5) Dns api mode:
Cloud flare api:
```
export CF_Key="sdfsdfsdfljlbjkljlkjsdfoiwje"

export CF_Email="xxxx@sss.com"

acme.sh  --issue  -d aa.com  --dns dns_cf
```

How to use dns api:  https://github.com/Neilpang/acme.sh/tree/master/dnsapi

#### 6) Apache mode:
```
acme.sh  --issue  -d aa.com  --apache
```


### 2. Multiple domains, SAN mode

Issue a single cert including multiple domains.  All the domains use the same validation method:

#### 1) Webroot mode:
You must point `aa.com` and `www.aa.com` to the same web root folder `/home/wwwroot/aa.com`
```
acme.sh  --issue  -d aa.com  -w /home/wwwroot/aa.com   -d www.aa.com
```

#### 2) Standalone mode:
```
acme.sh  --issue  -d aa.com  --standalone  -d www.aa.com 
```

#### 3) Standalone tls mode:
```
acme.sh  --issue  -d aa.com  --tls  -d www.aa.com 
```

#### 4) Dns manual mode:
```
acme.sh  --issue  -d aa.com  --dns  -d www.aa.com
```

#### 5) Dns api mode:
Cloud flare api:
```
export CF_Key="sdfsdfsdfljlbjkljlkjsdfoiwje"

export CF_Email="xxxx@sss.com"

acme.sh  --issue  -d aa.com  --dns dns_cf  -d www.aa.com
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

Or:

```
acme.sh  --issue  \
-d aa.com  --standalone \
-d bb.com  --dns dns_cf \
-d cc.com  --tls
```



