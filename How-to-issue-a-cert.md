### 1. Single domain:

#### 1) Webroot mode:
```
acme.sh  --issue  -d aa.com  -w /home/wwwroot/aa.com
```

#### 2) Standalone mode:
```
acme.sh  --issue  -d aa.com  --standalone
```

#### 3) Dns manual mode:
```
acme.sh  --issue  -d aa.com  --dns
```

#### 4) Dns api mode:
Cloud flare api:
```
export CF_Key="sdfsdfsdfljlbjkljlkjsdfoiwje"

export CF_Email="xxxx@sss.com"

acme.sh  --issue  -d aa.com  --dns dns-cf
```

How to use dns api:  https://github.com/Neilpang/acme.sh/tree/master/dnsapi

#### 5) Apache mode:
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

#### 3) Dns manual mode:
```
acme.sh  --issue  -d aa.com  --dns  -d www.aa.com
```

#### 4) Dns api mode:
Cloud flare api:
```
export CF_Key="sdfsdfsdfljlbjkljlkjsdfoiwje"

export CF_Email="xxxx@sss.com"

acme.sh  --issue  -d aa.com  --dns dns-cf  -d www.aa.com
```


### 3. Multiple domains, SAN mode,  Hybrid mode

Issue a single cert including multiple domains.  Each domain uses a different validation method.


```
acme.sh  --issue  \
-d aa.com  -w /home/wwwroot/aa.com \
-d bb.com  -dns dns-cf \
-d cc.com  --apache \
-d dd.com  -w /home/wwwroot/dd.com
```





