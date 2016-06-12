FreeBSD embedded systems like nas4free, FreeNAS etc. usually don't have curl and wget installed. The [fetch(1)](http://www.freebsd.org/cgi/man.cgi?fetch(1)) utility can't replace them, because it doesn't support POST and PUT requests. So I used this workaround to get curl running on this platform.

Full story in Issue [#194](https://github.com/Neilpang/acme.sh/issues/194).

## Requirements

You need:

* a persistent storage (to save some files)
* your FreeBSD version and architecture (e.g. FreeBSD 10, x86-64)

## 1. Find curl and ca-root-nss packages

You need to get the curl binary and the ca-root-nss.crt containing trusted certificate authorities.

Search for the packages in the download archives: [http://distcache.freebsd.org/](http://distcache.freebsd.org/)

E.g., currently these would be for FreeBSD 10 x86-64:

* http://distcache.freebsd.org/freebsd:10:x86:64/latest/All/curl-7.49.0.txz
* http://distcache.freebsd.org/freebsd:10:x86:64/latest/All/ca_root_nss-3.22.2.txz

## 2. Download and extract

Replace the URLs found in step 1 above.

```
$ cd <path on your persistent storage> # e.g. nas4free: somewhere in /mnt/pool0
$ fetch -o curl.txz http://distcache.freebsd.org/freebsd:10:x86:64/latest/All/curl-7.49.0.txz
$ tar xvf curl.txz /usr/local/bin/curl
$ mv usr/local/bin/curl .
$ rm curl.txz
$ fetch -o carootnss.txz http://distcache.freebsd.org/freebsd:10:x86:64/latest/All/ca_root_nss-3.22.2.txz
$ tar xvf carootnss.txz /usr/local/share/certs/ca-root-nss.crt
$ mv /usr/local/share/certs/ca-root-nss.crt .
$ rm carootnss.txz
$ # rm -r usr # be sure you're not in / ;-)
```

## 3. Configure your shell

Your shell needs to know how to use the new curl binary. Create a file `_shell_profile` in same directory as above:

`_shell_profile`:
```
export CURL_CA_BUNDLE=<path on your persistent storage>/ca-root-nss.crt
export PATH=$PATH:<path on your persistent storage>
```

Load it in your current shell session:

```
$ . _shell_profile
```

## 4. Install acme.sh

Now download and install acme.sh using the [advanced configuration](https://github.com/Neilpang/acme.sh/wiki/How-to-install#4-advanced-installation).

```
./acme.sh --install --home <path on your persistent storage>
```

You can now use it as usual.

## 5. Certificate renewal with cronjob

Usually, acme.sh can't create the automatic cronjob for certificate renewal on those platforms. I use a script like this:

`acme-renew.sh`:
```
. <path on your persistent storage>/_shell_profile
acme.sh --cron --home <path on your persistent storage>
```

Don't forget to `chmod +x acme-renew.sh`.

Now find out how to create the cronjob on your system. For nas4free, you can do it in the web interface under **System -> Advanced -> Cron**. I scheduled it for running on the 1st of every month.