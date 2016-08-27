From v2.4.4, acme.sh support to issue a cert from an existing csr.

There are 2 commands related:

### 1. Display the content of the csr:

```
acme.sh  --showcsr  --csr  /path/to/mycsr.csr
```

It shows the subject and domain names in the csr.

### 2. Issue a cert from the csr.

```
acme.sh  --signcsr   --csr  /path/to/mycsr.csr  -w /path/to/webroot/
```

The first parameter is the csr file,  all the other parameters are same as `--issue` command.

For exmaple,  you can specify the sepearate  webroot folders for each domain in the csr.

```
acme.sh  --signcsr  --csr  /path/to/mycsr/csr  -w /wwwroot/aa.com  -w /wwwroot/www.aa.com  -w /wwwroot/bb.com
```

Another example, use dns mode:

```
acme.sh  --signcsr  --csr /path/to/mycsr/csr  --dns  dns_cf
```


The parameters are same as `--issue` command.

See : https://github.com/Neilpang/acme.sh/wiki/How-to-issue-a-cert



