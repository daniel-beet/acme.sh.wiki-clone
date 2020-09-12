
There is a CI workflow to test your dns api when you send PR to add new dns api.

This test must be passing before your PR can get merged.

### 1.  Let's assume the usage of your api is like bellow:

```
export  MyDnsKey1=myValue1
export  MyDnsKey2=myValue2

acme.sh  --issue  -d  myExample.com  --dns  dns_myapi  

```

### 2.  You need to set up the following "Github actions Secrets":

Open the menu: `"Settings"` -> `"Secrets"` -> `"New secret"`
** The secrets are only visible to yourself, nobody else can read the secrets.**

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
TestingDomain = myExample.com
```
```

TEST_DNS_SLEEP = 120
```

The `TEST_DNS_SLEEP` is the time (in seconds) to sleep to wait for your DNS records to propagate. 

Different DNS provider may require different propagate time,  please ask your DNS provider support for the time.

Usually, it's larger than `120` seconds.


 

