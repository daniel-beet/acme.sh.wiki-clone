Guide for developing a dns api for acme.sh

Let's assume your api name is `myapi`, and you will use your api like:

```sh
export MYAPI_Username=myname
export MYAPI_Password=mypass
acme.sh --issue -d example.com  --dns  dns_myapi

```

Here we go:

### 1. The cloudflare dns api is a recommended reference:

https://github.com/Neilpang/acme.sh/blob/master/dnsapi/dns_cf.sh

### 2. The script file name must be `myapi.sh`
The file name must be in this format: `yourApiName.sh`, in this example, it should be `myapi.sh`

### 3. The file can be placed in `acme.sh/` folder, or in `acme.sh/dnsapi/` subfolder.
If you want to contribute your script to `acme.sh` project,  it must be placed in `acme.sh/dnsapi/` folder.

If you just want to use your script on your machine, you can put it in `.acme.sh/` or `.acme.sh/dnsapi/` folders.
acme.sh searches the script files in either the  acme.sh home dir(`.acme.sh/`) or in the  `dnsapi` subfolder(`.acme.sh/dnsapi`).

### 4. The file shebang must be `sh` not `bash`
acme.sh is a `unix shell` script,  not just a `bash` script.

If you want to contribute your script, the shebang muse be: 
```sh
#!/usr/bin/env sh
```
After the installation, acme.sh could change the shebang to bash to get a better performance if you have bash on your machine.

Of cause, if you just use it on your own, it can be any valid shebang on your machine. It could be `sh` or `bash`, it's up to you.

### 5. There must be 2 functions in your script:

```
# Usage: add  _acme-challenge.www.domain.com   "XKrxpRBosdIKFzxW_CT3KLZNf6q0HG9i01zxXp5CPBs"
# Used to add txt record
dns_myapi_add() { }

# Usage: fulldomain txtvalue
# Used to remove the txt record after validation
dns_cf_rm() { }
```
Actually, the `dns_myapi_add()` is required, but `dns_cf_rm()` is optional.  You can just write the add function at the beginning for testing purpose, it's `highly recommended` to implement the rm function too. Otherwise your txt records will increase 1 every 2 months.

### 6. 




