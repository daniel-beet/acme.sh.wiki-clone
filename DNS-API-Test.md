
There is a CI workflow to test your dns api when you send PR to add new dns api.

This test must be passing before your PR can get merged.

### 1.  Let's assume the usage of your api is like bellow:

```
export  MyDnsKey1=myValue1
export  MyDnsKey2=myValue2

acme.sh  --issue  -d  myExample.com  --dns  dns_myapi  

```

### 2.  You need to set up the following "Github actions Secrets":

Open the follow `"Settings"` -> `"Secrets"` -> `"New secret"`

```
TokenName1 =  MyDnsKey1
TokenValue1 = myValue1

TokenName2 =  MyDnsKey2
TokenValue2 = myValue2


TEST_DNS = dns_myapi  
TestingDomain = myExample.com

TEST_DNS_SLEEP = 120
```


 

