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
--certhome  ~/mycerts \
--accountemail  "hi@acme.sh" \
--accountkey  ~/myaccount.key \
--accountconf ~/myaccount.conf \
--useragent  "this is my client."
```

Explanations :

- `--home` is a customized dir to install `acme.sh` in.  By default, it installs into `~/.acme.sh` 
- `--certhome` is a customized dir to save the certs you issue.  By default, it saves he certs in `--home`.
- `--accountemail` is the email used to register account to Let's Encrypt,  you will receive renewal notice email here. Default is empty.
- `--accountkey` is the file saving your account private key.  By default it's saved in `--home`.
- `--useragent`  is the user-agent header value used to send to Let's Encrypt.

