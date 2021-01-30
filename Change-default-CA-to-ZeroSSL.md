
As for now, if no `server` is provided, or you have not `--set-default-ca` yet, acme.sh uses letsencrypt as the default CA.

https://github.com/acmesh-official/acme.sh/wiki/Server


Starting from `August-1st 2021`, acme.sh will release v3.0, in which the default CA will use [ZeroSSL](https://github.com/acmesh-official/acme.sh/wiki/ZeroSSL.com-CA) instead.


This change will only affect the newly created(issued) certs after `August-1st` (with v3.0),  any pre-existing certs will still be renewed automatically aginst the current CA.


Q&A:

1. As an existing user, what do I need to do?

    Generally, nothing needs to do.  (If auto-upgrade is enabled, acme.sh can upgrade itself). 
    No matter acme.sh is upgraded to v3.0 or not, your existing certs will be renewed as before, against the same CA it's currently using.

2. Will I still be able to use letsencrypt then?

    Yes, of cause. you are still free to use any supported CA with providing `--server` parameter.
    ```
    acme.sh  --issue  -d example.com --dns dns_cf    --server  letsencrypt
    ```
3. What if I don't like this change? I want to stick to letsencrypt?
    
    Yes, sure. You can `--set-default-ca` now or any time you like. Then acme.sh will always use the default ca you set:
    ```
    acme.sh --set-default-ca  --server  letsencrypt
    ```
    If you set the default CA, acme.sh will respect your choice first. It will always use this default ca in the future, no matter in `v2.*`, `v3.*` or any future `v4.*`.

    **acme.sh always respects your choice first, and will never make any changes to your files without your permissions.**

4. My current cert is using letsencrypt, Will it be changed when renewed then?
  
    No, and never. Don't worry. when your cert is renewed, it will use the current CA, not the default CA.

5. As a new user after `August-1st 2021`(v3.0), what will it look like to me?

    You can install acme.sh as normal, nothing is changed.
    
    You can also issue certs as normal [See how to issue a cert](https://github.com/acmesh-official/acme.sh/wiki/How-to-issue-a-cert):
    ```
    acme.sh --issue  -d example.com  --dns dns_cf
    ```

    The cert will be issued with the defualt CA [ZeroSSL](https://github.com/acmesh-official/acme.sh/wiki/ZeroSSL.com-CA)

    You can also try with letsencrypt:
    ```
    acme.sh --issue  -d example.com --dns dns_cf  --server letsencrypt
    ```



