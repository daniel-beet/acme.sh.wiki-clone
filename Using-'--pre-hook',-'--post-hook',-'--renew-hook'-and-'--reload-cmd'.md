Those hooks are only work with `--issue` or `--install-cert` command.

```sh
acme.sh --install-cert -d example.com  --pre-hook "echo this is pre hook"  --post-hook "echo this is post hook." 
```
