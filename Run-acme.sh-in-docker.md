# acme.sh 💕 docker

As one of the big docker fans, I understand that we hate to install anything on a docker host, even if it's just copying a shell script. 

So, Here "acme.sh in docker" comes.

1. Based on **alpine**, only 5MB size. 
1. Either run as executable or run as daemon
1. Support all the command line parameters.



# 1. Say "Hello World"

```sh
docker run --rm neilpang/acme.sh
```

# 2. Use as an executable:

```sh
docker run --rm  -it  \
  -v "$(pwd)/out":/acme.sh  \
  --net=host \
  neilpang/acme.sh  --issue -d example.com  --standalone
```

You can use **any commands** that **acme.sh** supports here, other examples:


```sh
#revoke a cert
docker run --rm  -it  \
  -v "$(pwd)/out":/acme.sh  \
  --net=host \
  neilpang/acme.sh  --revoke -d example.com
```

```sh
#use dns mode
docker run --rm  -it  \
  -v "$(pwd)/out":/acme.sh  \
  neilpang/acme.sh  --issue -d example.com --dns
```

```sh
#run cron job
docker run --rm  -it  \
  -v "$(pwd)/out":/acme.sh  \
  --net=host \
  neilpang/acme.sh  --cron
```

Anyway, you can just invoke **neilpang/acme.sh** image as if it were a real shell script.


# 3. Run acme.sh as a docker daemon.

### 1. Running acme.sh as a docker daemon, so that it can handle the renewal cronjob automatically.

```sh
docker run --rm  -itd  \
  -v "$(pwd)/out":/acme.sh  \
  --net=host \
  --name=acme.sh \
  neilpang/acme.sh daemon
```

### 2. Then you can just use `docker exec` to execute any acme.sh commands.

```sh
docker  exec  acme.sh --help
```

```sh
docker  exec  acme.sh --issue -d example.com  --standalone
```

Yes, again, You can use **any commands** that **acme.sh** supports here.







