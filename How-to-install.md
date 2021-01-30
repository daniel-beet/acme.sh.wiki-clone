_`# Update your Linux repo with latest CA bundle and patches from System Update else some issues will occur when generating your free SSL. Once Completed then begin the below procedure`_

## 1. Install from web: https://get.acme.sh

Install https://github.com/acmesh-official/acme.sh

```
curl https://get.acme.sh | sh -s email=my@example.com

```

or

```
wget -O -  https://get.acme.sh | sh -s email=my@example.com
```

## 2. Or, install from GitHub:

```
curl https://raw.githubusercontent.com/acmesh-official/acme.sh/master/acme.sh | sh -s -- --install-online -m  my@example.com
```

or:
```
wget -O -  https://raw.githubusercontent.com/acmesh-official/acme.sh/master/acme.sh | sh -s -- --install-online -m  my@example.com
```


## 3. Or, git clone and install:

```
git clone https://github.com/acmesh-official/acme.sh.git
cd acme.sh
./acme.sh --install -m  my@example.com
```


## 4. Advanced installation

```
git clone https://github.com/Neilpang/acme.sh.git
cd acme.sh
./acme.sh --install  \
--home ~/myacme \
--config-home ~/myacme/data \
--cert-home  ~/mycerts \
--m  "my@example.com" \
--accountkey  ~/myaccount.key \
--accountconf ~/myaccount.conf \
--useragent  "this is my client."
```

You don't need to set them all, just set those ones you care about.

Explanations :

- `--home` is a customized dir to install `acme.sh` in.  By default, it installs into `~/.acme.sh` 
- `--config-home` is a writable folder, acme.sh will write all the files(including cert/keys, configs) there. By default, it's in `--home`
- `--cert-home` is a customized dir to save the certs you issue.  By default, it's saved in `--config-home`.
- `--m` is the email used to register an account to Let's Encrypt,  you will receive a renewal notice email here. Default is empty.
- `--accountkey` is the file saving your account private key.  By default, it's saved in `--config-home`.
- `--user-agent`  is the user-agent header value used to send to Let's Encrypt.


