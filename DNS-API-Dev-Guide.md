Guide for developing a dns api for acme.sh

Let's assume your api name is `myapi`, and you will use your api like:

```sh
export MYAPI_Username=myname
export MYAPI_Password=mypass
acme.sh --issue -d example.com  --dns  dns_myapi

```

Here we go:

#### 1. The cloudflare dns api is a recommended reference:

https://github.com/Neilpang/acme.sh/blob/master/dnsapi/dns_cf.sh

#### 2. The script file name must be `myapi.sh`
The file name must be in this format: `yourApiName.sh`, in this example, it should be `myapi.sh`

#### 3. The file can be placed in `acme.sh/` folder, or in `acme.sh/dnsapi/` subfolder.
If you want to contribute your script to `acme.sh` project,  it must be placed in `acme.sh/dnsapi/` folder.

If you just want to use your script on your machine, you can put it in `.acme.sh/` or `.acme.sh/dnsapi/` folders.
acme.sh searches the script files in either the  acme.sh home dir(`.acme.sh/`) or in the  `dnsapi` subfolder(`.acme.sh/dnsapi`).

#### 4. 



