## Stateless Mode

Configure your webserver to respond statelessly to challenges for a given account key. This requires nothing more than a one-time web server configuration change and no "moving parts".

1. First get your account key thumbprint:
    ```
    root@ed:~# acme.sh --register-account
    [Mon Feb  6 21:40:18 CST 2017] Registering account
    [Mon Feb  6 21:40:19 CST 2017] Already registered
    [Mon Feb  6 21:40:21 CST 2017] Update success.
    [Mon Feb  6 21:40:21 CST 2017] ACCOUNT_THUMBPRINT='6fXAG9VyG0IahirPEU2ZerUtItW2DHzDzD9wZaEKpqd'
    ```
    Remember the thumbprint in the last line:
    `
    6fXAG9VyG0IahirPEU2ZerUtItW2DHzDzD9wZaEKpqd
    `
1. Configure the web server to return the account key thumbprint:
    ### NGINX
    Add something similar to your `nginx.conf`:
    ```
    http {
    ...
      server {
      ...
        location ~ ^/\.well-known/acme-challenge/([-_a-zA-Z0-9]+)$ {
          default_type text/plain;
          return 200 "$1.6fXAG9VyG0IahirPEU2ZerUtItW2DHzDzD9wZaEKpqd";
        }
      ...
      }
    }
    ```
    ### APACHE
    Add something similar to your `httpd.conf`:
    ```
    LoadModule php7_module libexec/apache2/libphp7.so
    ...
    <VirtualHost *:80>
        ...
        <IfModule php7_module>
            AddType application/x-httpd-php .php
            AddType application/x-httpd-php-source .phps
            <IfModule dir_module>
                DirectoryIndex index.html index.php
            </IfModule>
        </IfModule>
        ...
        <Directory "/PATH/TO/WWW/ROOT/.well-known/acme-challenge/">
            RewriteEngine On
            RewriteRule "^[-_a-zA-Z0-9]+$" "index.php"
        </Directory>
        ...
    </VirtualHost>
    ...
    ```
    Then add `/PATH/TO/WWW/ROOT/.well-known/acme-challenge/index.php`:
    ```php
    <?php
    header("Content-Type: text/plain");
    $token = array_pop(explode('/',$_SERVER['REQUEST_URI']));
    echo "$token.6fXAG9VyG0IahirPEU2ZerUtItW2DHzDzD9wZaEKpqd";
    ?>
    ```
    _NOTE that this approach uses PHP but something similar could be done with CGI, Perl, Ruby or pretty much any other server-side language._
3. Ok, you can issue cert now.

  ```
acme.sh --issue -d example.com  --stateless
```


