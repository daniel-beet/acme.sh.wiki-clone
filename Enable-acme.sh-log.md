##  1. You can use `--log` parameter in any command to enable log file.  
Once enabled, the log will take effect for any operations in future.

Example:  install and enable log.
```
acme.sh --install    --log
```

If you forget to enable log when installing, you can enable log by any command.
Exampl: enable log when issue cert:

```
acme.sh  --issue  ....   --log
```

## 2. Set the log file path.

The default log file is in `~/.acme.sh/acme.sh.log`

And you can specify a log file path 
```
acme.sh  --issue .....   --log  "/path/to/mylog.log"
```

## 3. You can also specify log level.
The default log level is `1`,  it will output the log info same as `--debug`
This is enough for most cases. You can also specify log level.

set log level to `2`
```
acme.sh  --issue  .....  --log     --log-level 2
```

