Installing an SSL Certificate
---
1. Run the script as root or with root privilege or using sudo
2. You create a private key for your server.  Only you should ever see this key (a file). 
3. You create a certificate signing request (CSR) using this private key.   The CSR includes information about you or your organization and about the domain you want to protect with SSL. 
4. The certifying authority creates a certificate for you based on this CSR.  This certificate will only work when your private key is present.   And your browser will only accept the certificate (without warnings) if the certificate was signed by a certifying authority recognized by your browser. 
5. Your browser will negotiate a secure (i.e. encrypted) connection to your server.  Anyone between your browser and the web server who intercepts this traffic will be unable to decrypt the content (well, that is the theory).

```shell
wget -q -O ./prepcert.sh http://proj.ri.mu/prepcert.sh ; bash ./prepcert.sh
```
At end of the script it will suggest an apache directive that can be added on your existing apache config file. The output will be something like this
```
You will need to add this to your SSL-enabled VirtualHost:
################### START HERE#########################
SSLEngine On
SSLCertificateFile /etc/httpd/conf/ssl.crt/mycooldomain.com.2048.crt-10102012-1410
SSLCertificateKeyFile /etc/httpd/conf/ssl.key/mycooldomain.com.2048.key-10102012-1410
################### END HERE #########################
```
To see the contents of the certificate, run:
```shell
openssl x509 -in $httpdconfdir/ssl.crt/$domainname.2048.crt -text  | head -n 12 
```
When you go to browse the https page, do you get a warning about the certificate issuer not being recognised?  Then you may need to tell Apache about your SSL certificate issuer's certificate.  They should provide you this file.  Upload it to the /etc/httpd/conf/ssl.crt directory. Then add an option like:
```
SSLCACertificateFile /etc/httpd/conf/ssl.crt/ComodoSecurityServicesCA.crt
```
after your SSLCertificateFile directive.

Some people prefer to pay for 1 certificate and they have several domain names aliased to the one domain. This can bring up warnings on browsers that the cert doesnt match the domain. There is an easy fix for this using an apache rewrite rule to redirect all those domains to the 'default' domain.

In the apache config where you have configured your port 80 Virtualhost add the following rules
```
RewriteEngine On
RewriteCond %{HTTP_HOST}   !^$
RewriteCond   %{HTTP_HOST}  !^domain.com [NC]
RewriteRule ^/(.*)         http://domain.com/$1 [L,R=301]
```
