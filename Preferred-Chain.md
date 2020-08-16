Using `--preferred-chain` to select alternate chain.

If the ACME CA provides multiple cert chain, you can use `--preferred-chain` to select one. Otherwise, it will get the default chain.

1. For Letsencrpt.org Staging Server:

    There are 2 chains provided:

    |   Name            |  Default  |
    |-------------------|-----------|
    |   Fake LE Root X1 | Yes       |
    |   Fake LE Root X2 |   No     |

    You select the ca like:
```
acme.sh  --issue -d example.com .....     --preferred-chain  "Fake LE Root X2"
```

2. For Letsencrypt.org Production server:

    There are 2 chains provided:

    |   Name            |  Default  |
    |-------------------|-----------|
    |   DST Root CA X3 | Yes       |
    |   ISRG Root X1 |   No     |

    You select the ca like:
```
acme.sh  --issue -d example.com .....     --preferred-chain  "ISRG Root X1"
```



