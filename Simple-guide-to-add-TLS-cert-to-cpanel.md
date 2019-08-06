# How to use acme.sh with cPanel for automatically renewing Let's Encrypt SSL 
This guide will demonstrate how to use acme.sh with a cPanel account to setup automatically renewing Let's Encrypt certificates.

**Prerequisites**: SSH access to your cPanel account is required. Contact your host to find out whether this is available. Sometimes they will enable it on request.

In the example commands below, make the following substitutions:

| Variable | Substitute With | Example Value |
|----------|-----------------|---------------|
| `_EXAMPLE.COM_` | The domain name you want an SSL certificate for. | `example.com` |
| `_CPANEL_USERNAME_` | The username you use to login to cPanel | `user123` |
| `_SSH_PORT_` | The port number you use to connect to SSH (ask your cPanel provider) | `22` |
| `_SSH_ADDRESS_` | The server address you use to connect to SSH (ask your cPanel provider) | `cpanel-123.example-host.com` |


***

## 1. Setting up acme.sh in cPanel.

Login to your cPanel account via SSH:

    ssh -l _CPANEL_USERNAME_ -p _SSH_PORT_  _SSH_ADDRESS_

Install acme.sh with the following command:

    curl https://get.acme.sh | sh

Log-off and login to SSH again, or run the following command:

    source ~/.bashrc

Register a Let's Encrypt account with your email, so you can be notified of any renewal issues:

    acme.sh --register-account --accountemail email@example.com

And confirm that acme.sh has setup a cron job for automatic renewals:

    crontab -l | grep acme.sh
    
    # The above command should output something like the below:
    10 0 * * * "/home/_CPANEL_USERNAME_/.acme.sh"/acme.sh --cron --home "/home/_CPANEL_USERNAME_/.acme.sh" > /dev/null

***

## 2. Issuing a test certificate for your domain.
We will use the [webroot](https://github.com/Neilpang/acme.sh/wiki/How-to-issue-a-cert#1-webroot-mode) method, which requires you to enter the document root of your domain. The webroot for your main domain is `~/public_html`, but addon domains and subdomains will be located in other directories.

<details>
<summary>(Click to see how you can locate the webroot for a domain using the uapi command)</summary>

    uapi DomainInfo single_domain_data domain=_EXAMPLE.COM_ | grep documentroot

</details>

Try issue a test certificate now:

    acme.sh --issue --webroot ~/public_html -d _EXAMPLE.COM_ --staging

If this domain has alias/parked domains, include those with additional `-d` parameters, such as:

    acme.sh --issue --webroot ~/public_html -d example.com -d www.example.com --staging

Ensure that this step is successful. If you encountered an error, ensure that the webroot is correct, or try to run acme.sh with `--debug 2` for further information.


## 3. Issuing a real certificate for your domain.
Use the same parameters as for your test certificate, except replace `--staging` with `--force`:

    acme.sh --issue --webroot ~/public_html -d _EXAMPLE.COM_ --force

This re-issues the certificate as a real, trusted SSL certificate, rather than a test one from the [staging environment](https://letsencrypt.org/docs/staging-environment/).

***

## 4. Installing your certificate to your cPanel account.

After issuing the real certificate, we need to tell acme.sh how to install it to our domain, so it can automatically perform that task at every renewal.

    acme.sh --deploy --deploy-hook cpanel_uapi --domain _EXAMPLE.COM_

This should result in a success message:

    [Tue Aug  6 03:56:25 EDT 2019] Certificate successfully deployed
    [Tue Aug  6 03:56:25 EDT 2019] Success

You should now be able to visit your domain in your browser and see that it is protected by a Let's Encrypt certificate.

**Please note, this will only deploy to a single virtualhost in your cPanel account. For other addon and subdomains, you should create separate certificates.**.

***

## 5. Redirecting HTTP to HTTPS for your domain (Optional).
To automatically redirect insecure HTTP traffic to HTTPS, please enable *Force HTTPS Redirection* in cPanel: https://blog.cpanel.com/force-https-redirection/

***
