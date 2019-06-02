Deploy the cert/key into a docker container.

There are 3 cases that acme.sh can deploy the certs into containers.

1. acme.sh is installed in the docker host machine, it deploys the certs into a container on the machine.
2. You are running `neilpang/acme.sh` container, that means acme.sh is running in a container, it can also deploy certs to another container on the same machine.
3. acme.sh is running on a machine, it deploys certs to a container running on another docker host.

Lets explain one by one:

## 1. Deploy certs from docker host to a container

acme.sh is installed on the docker host, it first issues a cert, then you may want to deploy the cert/key into a container.

#### 1. Please set a label on the container, the label will later be used to find the container.

```sh
docker run --rm -it -d  --label=sh.acme.autoload.domain=example.com   nginx:latest
```

#### 2. Remember the label value above, we can deploy now:

```sh
# The label value to find the container
export DEPLOY_DOCKER_CONTAINER_LABEL=sh.acme.autoload.domain=example.com

# The target file path in the container.
# The files will be copied to the position in the container.
export DEPLOY_DOCKER_CONTAINER_KEY_FILE="/etc/nginx/ssl/example.com/key.pem"
export DEPLOY_DOCKER_CONTAINER_CERT_FILE="/etc/nginx/ssl/example.com/cert.pem"
export DEPLOY_DOCKER_CONTAINER_CA_FILE="/etc/nginx/ssl/example.com/ca.pem"
export DEPLOY_DOCKER_CONTAINER_FULLCHAIN_FILE="/etc/nginx/ssl/example.com/full.pem"

# The command to reload the service in the container.
export DEPLOY_DOCKER_CONTAINER_RELOAD_CMD="service nginx force-reload"

acme.sh --deploy --deploy-hook docker -d example.com

```

 
## 2. Deploy certs from a container to another container

Let's use `neilpang/acme.sh` image as an example,  actually, you can use acme.sh in any container.

#### 1. Ok, same as above, first run the target container with a label:

```sh
docker run --rm -it -d  --label=sh.acme.autoload.domain=example.com   nginx:latest
```

#### 2. Run acme.sh in a container

For more details see: https://github.com/Neilpang/acme.sh/wiki/Run-acme.sh-in-docker#3-run-acmesh-as-a-docker-daemon

Let's run acme.sh as a daemon, a difference with the above link is that we mount docker daemon socket `/var/run/docker.sock` in to the container.

```sh
docker run --rm  -itd  \
  -v "$(pwd)/out":/acme.sh  \
  --net=host \
  --name=acme.sh \
  -v /var/run/docker.sock:/var/run/docker.sock \
  neilpang/acme.sh daemon
```

#### 3. Let's issue a cert first:

```sh
docker  exec \
    -e CF_Email=xxx@exmaple.com \
    -e CF_Key=xxxxxxxxxx  \
    acme.sh --issue -d example.com  --dns dns_cf
```

#### 4. Let's deploy the cert now:

```sh
docker  exec \
    -e DEPLOY_DOCKER_CONTAINER_LABEL=sh.acme.autoload.domain=example.com \
    -e DEPLOY_DOCKER_CONTAINER_KEY_FILE=/etc/nginx/ssl/example.com/key.pem \
    -e DEPLOY_DOCKER_CONTAINER_CERT_FILE="/etc/nginx/ssl/example.com/cert.pem" \
    -e DEPLOY_DOCKER_CONTAINER_CA_FILE="/etc/nginx/ssl/example.com/ca.pem" \
    -e DEPLOY_DOCKER_CONTAINER_FULLCHAIN_FILE="/etc/nginx/ssl/example.com/full.pem" \
    -e DEPLOY_DOCKER_CONTAINER_RELOAD_CMD="service nginx force-reload" \
    acme.sh --deploy -d example.com  --deploy-hook docker
```


#### 5. All together, docker compose example:

```
version: '3.4'
services:
  web:
    image: nginx
    container_name: nginx
    labels:
      - sh.acme.autoload.domain=example.com

  acme.sh:
    image: neilpang/acme.sh
    container_name: acme.sh    
    command: daemon
    volumes:
      - ./acmeout:/acme.sh
      - /var/run/docker.sock:/var/run/docker.sock 
    environment:
      - DEPLOY_DOCKER_CONTAINER_LABEL=sh.acme.autoload.domain=example.com
      - DEPLOY_DOCKER_CONTAINER_KEY_FILE=/etc/nginx/ssl/example.com/key.pem
      - DEPLOY_DOCKER_CONTAINER_CERT_FILE="/etc/nginx/ssl/example.com/cert.pem"
      - DEPLOY_DOCKER_CONTAINER_CA_FILE="/etc/nginx/ssl/example.com/ca.pem"
      - DEPLOY_DOCKER_CONTAINER_FULLCHAIN_FILE="/etc/nginx/ssl/example.com/full.pem"
      - DEPLOY_DOCKER_CONTAINER_RELOAD_CMD="service nginx force-reload"
  
```


## 3. Deploy certs to a container in a remote docker host

TODO:  this feature is not implemented yet.
If you want this feature, please create an issue, and let me know.








