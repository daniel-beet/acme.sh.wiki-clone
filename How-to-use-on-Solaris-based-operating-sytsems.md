I ran this on Illumos, with Apache22

## 1. get acme.sh

[Install acme.sh](How-to-install)

## 2. Make sure you have wget and GNU sed installed

```
pkg install gnu-sed wget
```

## 3. Set up your environment
* your path needs to include GNU sed before "Sun" sed, and include the path to "apachectl"
* you need to set "ACME_DIR" to be somewhere at least chmod 755 readable by the Apache web server (i.e. don't use /root/acme because the /root directory is not read/exec by "other".  The default ACME_DIR is set to be the automounted "/home/.acme" directory. (Neil suggests possibly using /tmp/.acme)

```
export PATH ; PATH=/usr/gnu/bin:/usr/bin:/usr/sbin:/sbin:/usr/apache2/2.2/bin
export ACME_DIR ; ACME_DIR=/tmp/.acme
```

## 4. Run the acme.sh
* Please don't specify the "-w" option, this will only cause confusion
* if you are using Proxy forwarding, like I was, or any other similar configuration; you will need to modify the Apache configuration to allow "/.well-known" to pass through
* I never had any luck with curl, so I force the use of wget. YMMV
* if you are going to play with this a lot, you will want to add "--test" to the command, which will use the "staging area" of letencrypt.com, and/or --debug to check that it is working.  If you do use the "staging area"; when you come to do this for real you will need to add "--force" to the command

```
./acme.sh --issue --apache -d online.domain.com --use-wget
```

## 5. adding to cron
the simple command to add this to cron is:
```
./acme.sh --install-cronjob
```
however you might well need to copy/move the "acme.sh" command into ~/.acme.sh for this to pick up the location correctly.