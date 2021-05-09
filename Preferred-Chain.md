Using `--preferred-chain` to select alternate chain.

If the ACME CA provides multiple cert chain, you can use `--preferred-chain` to select one. Otherwise, it will get the default chain.

1. For letsencrypt.org Staging Server:

    There are 2 chains provided:

    |   Name            |  Default  |
    |-------------------|-----------|
    |   Fake LE Root X1 | Yes       |
    |   Fake LE Root X2 |   No     |

    You select the ca like:
```
acme.sh  --issue -d example.com .....  --test    --preferred-chain  "Fake LE Root X2"
```

You can also use part of the name:

```
acme.sh  --issue -d example.com .....  --test    --preferred-chain  "X2"
```

It's also case-insensitive:

```
acme.sh  --issue -d example.com .....  --test    --preferred-chain  "x2"
```


2. For Letsencrypt.org Production server:

    There are 2 chains provided:

    |   Name            |  Default  |
    |-------------------|-----------|
    |   DST Root CA X3 | Yes       |
    |   ISRG Root X1 |   No     |

    You select the ca like:
```
acme.sh  --issue -d example.com .....  --server letsencrypt   --preferred-chain  "ISRG Root X1"
```


You can also use part of the name:

```
acme.sh  --issue -d example.com .....  --server letsencrypt   --preferred-chain  "ISRG"
```

It's also case-insensitive:

```
acme.sh  --issue -d example.com .....  --server letsencrypt    --preferred-chain  "isrg"
```


