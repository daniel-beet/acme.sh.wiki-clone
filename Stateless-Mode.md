## Stateless Mode

Configure your webserver to respond statelessly to challenges for a given account key. This requires nothing more than a one-time web server configuration change and no "moving parts".

1. First get your account key thumbprint:

```
root@ed:~# acme.sh --register-account
[Mon Feb  6 21:40:18 CST 2017] Registering account
[Mon Feb  6 21:40:19 CST 2017] Already registered
[Mon Feb  6 21:40:21 CST 2017] Update success.
[Mon Feb  6 21:40:21 CST 2017] ACCOUNT_THUMBPRINT='6fXAG9VyG0IahirPEU2ZerUtItW2DHzDzD9wZaEKpqd'
```

Remember the the thumbprint in the last line:
`
6fXAG9VyG0IahirPEU2ZerUtItW2DHzDzD9wZaEKpqd
`

2. Configure the nginx server to return the account key thumbprint:

```
http {
...
  server {

...

    location ~ "^/\.well-known/acme-challenge/([-_a-zA-Z0-9]+)$" {
      default_type text/plain;
      return 200 "$1.6fXAG9VyG0IahirPEU2ZerUtItW2DHzDzD9wZaEKpqd";
    }

...

  }
}

```


3. Ok, you can issue cert now.

```
acme.sh --issue -d example.com  --stateless
```

