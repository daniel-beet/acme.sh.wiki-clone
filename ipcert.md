Just use ip as the `-d` parameter:

```
acme.sh  --issue  -d 1.1.1.1  --w /etc/apachet/xxxxxx
```

As the rfc 8738 requires: https://tools.ietf.org/html/rfc8738

If the ip address can not be validated by dns methods. Only  http/webroot  and  alpn methods are allowded.




