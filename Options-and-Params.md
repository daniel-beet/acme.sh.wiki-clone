
***
##Usage:  
acme.sh  command ...[parameters]....  

***

##Commands:  
  **--help, -h**               Show this help message.  
  **--version, -v**            Show version info.  
  **--install**                Install acme.sh to your system.  
  **--uninstall**              Uninstall acme.sh, and uninstall the cron job.  
  **--upgrade**                Upgrade acme.sh to the latest code from https://github.com/Neilpang/acme.sh .  
  **--issue**                  Issue a cert.  
  **--signcsr**                Issue a cert from an existing csr.  
  **--deploy**                 Deploy the cert to your server.  
  **--installcert**            Install the issued cert to apache/nginx or any other server.  
  **--renew, -r**              Renew a cert.  
  **--renewAll**               Renew all the certs.  
  **--remove**                 Remove renewal configuration for a cert.  
  **--revoke**                 Revoke a cert.  
  **--list**                   List all the certs.  
  **--showcsr**                Show the content of a csr.  
  **--installcronjob**         Install the cron job to renew certs, you don't need to call this. The 'install' command can automatically install the cron job.  
  **--uninstallcronjob**       Uninstall the cron job. The 'uninstall' command can do this automatically.  
  **--cron**                   Run cron job to renew all the certs.  
  **--toPkcs**                 Export the certificate and key to a pfx file.  
  **--updateaccount**          Update account info.  
  **--registeraccount**        Register account key.  
  **--createAccountKey, -cak** Create an account private key, professional use.  
  **--createDomainKey, -cdk**  Create an domain private key, professional use.  
  **--createCSR, -ccsr**       Create CSR , professional use.  
  **--deactivate**             Deactivate the domain authz, professional use.  

***

##Parameters:  
  **--domain, -d   domain.tld**         Specifies a domain, used to issue, renew or revoke etc.  
  **--force, -f**                       Used to force to install or force to renew a cert immediately.  
  **--staging, --test**                 Use staging server, just for test.  
  **--debug**                           Output debug info.  
    
  **--webroot, -w  /path/to/webroot**   Specifies the web root folder for web root mode.  
  **--standalone**                      Use standalone mode.  
  **--tls**                             Use standalone tls mode.  
  **--apache**                          Use apache mode.  
  **--dns [dns_cf|dns_dp|dns_cx|/path/to/api/file]**   Use dns mode or dns api.  
  **--dnssleep  [120]**                  The time in seconds to wait for all the txt records to take effect in dns api mode. Default 120 seconds.  
  
  **--keylength, -k [2048]**            Specifies the domain key length: 2048, 3072, 4096, 8192 or ec-256, ec-384.  
  **--accountkeylength, -ak [2048]**    Specifies the account key length.  
  **--log    [/path/to/logfile]**       Specifies the log file. The default is: "/root/.acme.sh/acme.sh.log" if you don't give a file path here.  
  **--log-level 1|2**                   Specifies the log level, default is 1.  

**These parameters are to install the cert to nginx/apache or anyother server after issue/renew a cert:**  
  
  **--cert-file /path/to/real/cert/file**  After issue/renew, the cert will be copied to this path.  
  **--key-file /path/to/real/key/file**  After issue/renew, the key will be copied to this path.  
  **--ca-file /path/to/real/ca/file**    After issue/renew, the intermediate cert will be copied to this path.  
  **--fullchain-file /path/to/fullchain/file** After issue/renew, the fullchain cert will be copied to this path.  
  
  **--reloadcmd "service nginx reload"** After issue/renew, it's used to reload the server.  

  **--accountconf**                     Specifies a customized account config file.  
  **--home**                            Specifies the home dir for acme.sh. If you use it with '--install', then you'll need to use it every time you use acme.sh.  
  **--certhome**                        Specifies the home dir to save all the certs, only valid for '--install' command.  
  **--useragent**                       Specifies the user agent string. it will be saved for future use too.  
  **--accountemail**                    Specifies the account email for registering, Only valid for the '--install' and '--updateaccount' commands.  
  **--accountkey**                      Specifies the account key path, Only valid for the '--install' command.  
  **--days**                            Specifies the days to renew the cert when using '--issue' command. The max value is 60 days.  
  **--httpport**                        Specifies the standalone listening port. Only valid if the server is behind a reverse proxy or load balancer.  
  **--tlsport**                         Specifies the standalone tls listening port. Only valid if the server is behind a reverse proxy or load balancer.  
  **--local-address**                   Specifies the standalone/tls server listening address, in case you have multiple ip addresses.  
  **--listraw**                         Only used for '--list' command, list the certs in raw format.  
  **--stopRenewOnError, -se**           Only valid for '--renewall' command. Stop if one cert has error in renewal.  
  **--insecure**                        Do not check the server certificate, in some devices, the api server's certificate may not be trusted.  
  **--ca-path**                         Specifies directory containing CA certificates in PEM format, used by wget or  curl to verify API server's certificate.  
  **--ca-bundle**                       Specifies the CA certificate bundle file used by wget or curl to verify API server's certificate.  
  **--nocron**                          Only valid for '--install' command, which means: do not install the default cron job. In this case, the certs will not be renewed automatically.  
  **--ecc**                             Specifies to use the ECC cert. Valid for '--installcert', '--renew', '--revoke', '--toPkcs' and '--createCSR'  
  **--csr**                             Specifies the input csr.  
  **--pre-hook**                        Command to be run before obtaining any certificates.  
  **--post-hook**                       Command to be run after attempting to obtain/renew certificates, no matter if the obtain/renew succeeded or failed.
  **--renew-hook**                      Command to be run once for each successfully renewed certificate.  
  **--deploy-hook**                     The hook file to deploy cert  
  **--ocsp-must-staple, --ocsp**        Generate ocsp must Staple extension.  
  **--auto-upgrade   [0|1]**            Valid for '--upgrade' command, indicating whether to upgrade automatically in future.  
  **--listen-v4**                       Force standalone/tls server to listen at ipv4.  
  **--listen-v6**                       Force standalone/tls server to listen at ipv6.



