# Using TLS-ALPN without downtime

acme.sh added support for TLS-ALPN on 2018-12-28.

This feature allows domain validation to be performed over port 443, useful when port 80 is not accessible. However, the feature requires any existing webservers on that port to be shut down so that acme.sh can listen on port 443.

This article outlines some ways it is possible to configure webservers to work transparently with acme.sh's TLS-ALPN support without having to stop and start your webserver.

## Compatibility

| Webserver | Status | Caveats |
|-----------|--------|---------|
| Apache httpd | Not yet possible | - | 
| nginx | Supported | Requires [ngx_stream_ssl_preread_module](http://nginx.org/en/docs/stream/ngx_stream_ssl_preread_module.html) to be compiled. e.g. on Ubuntu 18.04, included in the `nginx-full` package. |
| haproxy | haproxy>=1.9.1 | - 

## Instructions

### nginx

With nginx, what we do is create a TLS-ALPN load balancer within nginx on port 443, and re-assign all existing HTTPS virtual hosts within nginx to another port.

When a TLS-ALPN connection comes in, it is routed to acme.sh, otherwise, the connection is routed to the HTTPS virtual hosts.


1\. Verify that nginx is compiled with the required module:

    $ nginx -V 2>&1 | grep -o ssl_preread
    ssl_preread


2\. Re-assign ALL port 443 virtual hosts to another port

If you have existing HTTPS virtual hosts listening on port 443, you will need to re-assign each HTTPS server to an alternate port (such as 8443), so that we can put the ALPN load balancer on port 443.

For example

```nginx
server {
    server_name example.org;
    listen 443;
    # ...
}
```

would become

```nginx
server {
    server_name example.org;
    listen 8443;
    # ...
}
```

3\. Add the ALPN load balancer

In `/etc/nginx.conf`, at the end of the file, add (substituting `8443` for the port chosen in step 2):

```nginx
stream {
  map $ssl_preread_alpn_protocols $tls_port {
    ~\bacme-tls/1\b 10443;
    default 8443;
  }
  server {
    listen 443;
    listen [::]:443;
    proxy_pass 127.0.0.1:$tls_port;
    ssl_preread on;
  }
}
```

4\. Make sure the configuration works and reload.

    $ sudo nginx -t
    $ sudo systemctl reload nginx

5\. Try to issue a certificate (substituting `example.org` for the domain you want on your certificate).

    $ sudo acme.sh --issue --alpn --tlsport 10443 -d example.org

### haproxy

With haproxy, what we have to do is run an ALPN load balancer frontend in TCP mode on port 443, and re-assign all HTTPS frontends to an alternate port.

When a TLS-ALPN connection for ACME comes in, it will be routed to acme.sh, otherwise, the connection is forwarded to the normal HTTPS frontend.

1\. Verify that haproxy is at least version 1.9.1:

    $ haproxy -v
    HA-Proxy version 1.9.2 2019/01/16 - https://haproxy.org/

2\. In the haproxy configuration, as well as re-assigning your existing HTTPS frontend to port 8443, you will need to add:

  1. `fe_alpn` - a TCP frontend on 443 to load balance ALPN
  2. `bk_acmesh` - A backend to send requests to acme.sh
  3. `bk_https` - A backend to send requests to your regular HTTPS frontend

```haproxy
# New
frontend fe_alpn
  mode tcp
  bind :443
  tcp-request inspect-delay 5s
  tcp-request content accept if { req_ssl_hello_type 1 }
  use_backend bk_acmesh if { req.ssl_alpn acme-tls/1 }
  default_backend bk_https

# New
backend bk_acmesh
  server acmesh 127.0.0.1:10443

# New
backend bk_https
  server https 127.0.0.1:8443

# Existing, changed from 80 -> 8443
frontend fe_https
  mode http
  bind :8443 ssl crt /etc/ssl/haproxy.pem
  # ...
```

3\. Make sure the configuration works and reload:

    $ sudo haproxy -c -f /etc/haproxy.cfg
    $ sudo systemctl reload haproxy

4\. Try to issue a certificate (substituting `example.org` for the domain you want on your certificate).

    $ sudo acme.sh --issue --alpn --tlsport 10443 -d example.org