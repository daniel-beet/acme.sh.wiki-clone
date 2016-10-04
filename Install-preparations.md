# 1. Ubuntu:

```
apt-get install openssl cron  netcat-openbsd  curl
```

Or install `netcat`.
```
apt-get install netcat
```


# 2. CentOS

```
yum -q -y install  openssl  crontabs  netcat-openbsd curl
```
For centos 5:

```
yum -q -y install openssl vixie-cron netcat-openbsd curl
```

Or install `nc`
```
yum -q -y install nc
```

# 3. alpine

The default `nc` tool doesn't support `-4` , `-6` and local address, So, please install `netcat-openbsd` to support all the options.

```
apk --no-cache add -f openssl curl netcat-openbsd
```


# 4. kalilinux

The default `netcat` tool doesn't support `-4` , `-6` and local address, So, please install `netcat-openbsd` to support all the options.

```
apt-get -qqy install  openssl cron  netcat-openbsd curl
```

