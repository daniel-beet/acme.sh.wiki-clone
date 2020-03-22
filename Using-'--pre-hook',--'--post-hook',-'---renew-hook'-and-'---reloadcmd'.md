Those hooks are only accepted by the `--issue` command, but will be saved and apply to `--renew` or `--cron` commands as well. As such it can be a good way to do things (like close and re-open a server, or notify of updates) that need to happen only when issuance is actually attempted.

```sh
acme.sh --issue -d example.com  \
        --pre-hook "echo this is pre hook that happens before attempting to issue a certificate."  \
        --post-hook "echo this is post hook that happens after attempting to issue a certificate."  \
        --renew-hook "echo this will be called when certs are successfully renewed."  .......
```

Note: to reload the web server when installing certificates, there is a related command:
```sh
acme.sh --install-cert \
        ..... \
        --reloadcmd "echo this runs after successfully installing certificates."
```

Keep in mind that when running `--cron`, any newly-renewed certificates will automatically be installed, and the reloadcmd will be run. Consequently, if you are using `--pre-hook` and `--post-hook` to stop and restart a web server, you probably don't want to also reload it in reloadcmd. Since `--install-cert` is typically only run once, you can just do this by hand.