Use `--debug` parameter to output detailed debug info.

For example:

```
acme.sh  --issue  .........   --debug
```


To output more detailed info:

```
acme.sh  --issue  ..........    --debug 2
```

### Common Root Cause of issue:
#### Port 80 is blocked
If your ISP blocks port 80, any webroot based authentication will fail
You can test this by running this command: curl -IkL -m20 http://<your domain> from OUTSIDE your local network.

### Common Errors using DNS API:
#### Mistake 1: Clumsy fingers - newline in ~/.acme.sh/account.conf
If you type in the api key or private key and accidentally put in a newline or a typo, check and ensure the keys look right in ~/.acme.sh/account.conf

#### I still see my old keys (when moving from letsencrypt bot to .acme.sh)
Needed step - point nginx configuration to new acme based keys
If you still see the old keys being used, even after finally getting the dns based authentication to work.
You may need to comment out the previous keys from the letsencrypt bot, and point to the new folder:

> `# RSA certificate`
> 
> #ssl_certificate /etc/letsencrypt/live/<your domain>/fullchain.pem; # managed by Certbot
> 
> #ssl_certificate_key /etc/letsencrypt/live/<your domain>/privkey.pem; # managed by Certbot
>
> ssl_certificate <your home directory>/.acme.sh/<your domain>/fullchain.cer;
>
> ssl_certificate_key <your home directory>/.acme.sh/<your domain>/<your domain>.key;

#### Do I need to include the webroot `-w <my webroot>` for DNS?
No!  You'll end up back failing the port 80 access to your webroot folder if that was your issue.