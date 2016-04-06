Welcome to the le wiki!

Here is the wiki page for le.sh


## 1. How to install
Install online:
```
curl https://raw.githubusercontent.com/Neilpang/le/master/le.sh | INSTALLONLINE=1  bash
```
Or,
```
wget -O -  https://raw.githubusercontent.com/Neilpang/le/master/le.sh | INSTALLONLINE=1  bash
```

Install from git:
```
git clone https://github.com/Neilpang/le.git
cd le
./le.sh install
```

If you want to use your email to register to LetsEncrypt, you can edit `~/.le/account.conf`
Uncomment and set `ACCOUNT_EMAIL`
```
ACCOUNT_EMAIL=aaa@aaa.com
```
Which is optional.  If you don't want to register with your email, just ignore this step.



## 2.  How to use in Proxmox ?

After installation,

1) Issuer a cert:
```
/root/.le/le.sh issue   no   <DOMAIN>
```
After issuing, the cert will be automatically every 80 days.

2) Install the cert to Proxmox:

```
/root/.le/le.sh  installcert   <DOMAIN>  /etc/pve/local/pveproxy-ssl.pem /etc/pve/local/pveproxy-ssl.key /etc/pve/local/pveproxy-ssl.pem "systemctl restart pveproxy"
```

Ok, it's done. Open the link: `https://<DOMAIN>:8006`


## 3. How to get pkcs12(pfx) format:

After you issue the cert, then you can use `toPkcs` command to convert the cert to pkcs12(pfx) format
```
le.sh  toPkcs  <domain> [pfx-password]
```

## 4. How to run on Windows with Cygwin.
1) Download cygwin installer: setup-x86.exe  or setup-x86_64.exe
from: https://cygwin.com/

2) In the installer, select: Net: `curl`, Net: `nc`  and  Admin: `cron` to install.

3) After install finished, you can open Cygwin window and use `curl` to install online.

4) In Cygwin, the cron job is not working by default. So, the issued cert will not be renew automatically.
If you want the cron job to work, you must enable the cron job:

https://stackoverflow.com/questions/707184/how-do-you-run-a-crontab-in-cygwin-on-windows

For short, just run:
```
cron-config
```

