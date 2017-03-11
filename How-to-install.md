## 1. Install from web: https://get.acme.sh

Install https://github.com/Neilpang/acme.sh

```
curl https://get.acme.sh | sh

```

or

```
wget -O -  https://get.acme.sh | sh
```

## 2. Or, install from Git hub:

```
curl https://raw.githubusercontent.com/Neilpang/acme.sh/master/acme.sh | INSTALLONLINE=1  sh
```

or:
```
wget -O -  https://raw.githubusercontent.com/Neilpang/acme.sh/master/acme.sh | INSTALLONLINE=1  sh
```


## 3. Or, git clone and install:

```
git clone https://github.com/Neilpang/acme.sh.git
cd acme.sh
./acme.sh --install
```


## 4. Advanced installation

```
git clone https://github.com/Neilpang/acme.sh.git
cd acme.sh
./acme.sh --install  \
--home ~/myacme \
--config-home ~/myacme/data \
--certhome  ~/mycerts \
--accountemail  "hi@acme.sh" \
--accountkey  ~/myaccount.key \
--accountconf ~/myaccount.conf \
--useragent  "this is my client."
```

You don't need to set them all, just set those ones you care about.

Explanations :

- `--home` is a customized dir to install `acme.sh` in.  By default, it installs into `~/.acme.sh` 
- `--config-home` is a writable folder, acme.sh will write all the files(including cert/keys, configs) there. By default, it's in `--home`
- `--certhome` is a customized dir to save the certs you issue.  By default, it's saved in `--config-home`.
- `--accountemail` is the email used to register account to Let's Encrypt,  you will receive renewal notice email here. Default is empty.
- `--accountkey` is the file saving your account private key.  By default it's saved in `--config-home`.
- `--useragent`  is the user-agent header value used to send to Let's Encrypt.


