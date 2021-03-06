
There is a CI workflow `DNS.yml` to test your dns api when you send PR to add new dns api.

This test must be passing before your PR can get merged.

However, as a limitation of github actions,  the workflow `DNS.yml` can only run in your Forked repo when you push code.

## So, please enable Github Actions on your Forked repo first, and then push code to your repo, it will run there.


### 1.  Let's assume the usage of your api is like below:

```
export  MyDnsKey1=myValue1
export  MyDnsKey2=myValue2

acme.sh  --issue  -d  mytest.myExample.com  --dns  dns_myapi  

```

### 2.  You need to set up the following "Github actions Secrets":

Open the menu: `"Settings"` -> `"Secrets"` -> `"New secret"`


**The secrets are only visible to yourself, nobody else can read the secrets.**


Set the following secrets:

```
TokenName1 =  MyDnsKey1
```
```
TokenValue1 = myValue1
```
```
TokenName2 =  MyDnsKey2
```
```
TokenValue2 = myValue2
```
```

TEST_DNS = dns_myapi  
```
```
TestingDomain = mytest.myExample.com
```
```

TEST_DNS_SLEEP = 120
```

The `TEST_DNS_SLEEP` is the time (in seconds) to sleep to wait for your DNS records to propagate. 

Different DNS providers may require different propagate time,  please ask your DNS provider support for the time.

Usually, it's larger than `120` seconds.


 
# How to get a Solaris server

If you need a solaris shell to debug your script,  please see this project: https://github.com/vmactions/shell-solaris


# How to get a FreeBSD server

If you need a freebsd shell to debug your script,  please see this project: https://github.com/vmactions/shell-freebsd



