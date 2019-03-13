Welcome to the acme.sh wiki!

https://wiki.acme.sh

Here is the wiki page for acme.sh


## 1. How to install

https://github.com/Neilpang/acme.sh/wiki/How-to-install

## 2.  How to use in Proxmox ?

After installation,

1) Check if port 80 is opened on Proxmox host.

2) Issue a cert:
```
/root/.acme.sh/acme.sh  --issue  --standalone  -d <DOMAIN>
```
After issuing, the cert will be automatically renewed every 60 days.

3) Install the cert to Proxmox:

```
/root/.acme.sh/acme.sh  --installcert  -d <DOMAIN>  --certpath /etc/pve/local/pveproxy-ssl.pem --keypath /etc/pve/local/pveproxy-ssl.key  --capath  /etc/pve/local/pveproxy-ssl.pem  --reloadcmd  "systemctl restart pveproxy"
```

Ok, it's done. Open the link: `https://<DOMAIN>:8006`


## 3. How to get pkcs12(pfx) format:

After you issue the cert, you can use the `toPkcs` command to convert the cert to pkcs12(pfx) format
```
acme.sh  --toPkcs  -d <domain>  [--password pfx-password]
```

## 4. How to run on Windows with Cygwin.
1) Download cygwin installer: setup-x86.exe  or setup-x86_64.exe
from: https://cygwin.com/

2) In the installer, select: Net: `curl` and Net: `socat` to install.

3) After install finished, you can open the Cygwin window and use `curl` to install `acme.sh`online:
[https://github.com/Neilpang/le/wiki#1-how-to-install](https://github.com/Neilpang/le/wiki#1-how-to-install)



