The location of the Apache configuration file
---
 - /etc/apache2/httpd.conf
 - /etc/apache2/apache2.conf
 - /etc/httpd/httpd.conf
 - /etc/httpd/conf/httpd.conf

Get a list of all virtual hosts which are defined in all apache configuration files
---
```shell
apachectl -S
```

Get the configuration of apache or httpd
---
```shell
apache -V
httpd -V
```

Start or stop apache
---
```shell
apachectl -k stop 
apachectl -k graceful-stop # graceful stop
apachectl -k graceful # graceful restart
apachectl -k restart
```

replace the user/group of apache
---
```shell
sed -i 's/User apache/User vagrant/i' /etc/httpd/conf/httpd.conf
sed -i 's/Group apache/Group vagrant/i' /etc/httpd/conf/httpd.conf
# with variable substitution
USERID="$1"
sed -i 's/User apache/User '${USERID}'/i' /etc/httpd/conf/httpd.conf
sed -i 's/Group apache/Group '${USERID}'/i' /etc/httpd/conf/httpd.conf
```
Apache user and group
---
```shell
groupadd web-content
usermod -a -G web-content alice
usermod -a -G web-content apache
chown -R alice:web-content /var/www/html
find /var/www/html -type f -exec chmod 640 {} \;
find /var/www/html -type d -exec chmod 750 {} \;
```
What we've done here is to set all files to `rw-r-----` and directories to `rwxr-x---`. Because the group "web-content" is applied to all the files and directories, `httpd` can read these files, but cannot write to them.

> A common gotcha for directories hosted outside of the default /var/www/ is that the Apache user doesn't just need permissions to the directory and subdirectories where the site is being hosted. Apache requires permissions to all the directories all the way up to the root of the file system where the site is hosted. Apache automatically gets permissions assigned to /var/www/ when it's installed, so if your host directory is directly underneath that then this doesn't apply to you. Edit: Daybreaker has reported that his Apache was installed without correct access permissions to the default directory.

> For example, you've got a development machine and your site's directory is:
```
/username/home/Dropbox/myamazingsite/
```
You may think you can get away with:
```
chgrp -R www-data /username/home/Dropbox/myamazingsite/
chmod -R 2750 /username/home/Dropbox/myamazingsite/
```
because this gives Apache permissions to access your site's directory? Well that's correct but it's not sufficient. Apache requires permissions all the way up the directory tree so what you need to do is:
```
chgrp -R www-data /username/
chmod -R 2750 /username/
```
`Setgid` is pretty much the exact same as setuid, but the binary runs with the privileges of the owner group rather than the user’s primary group privileges.

> You might have selinux enabled. Try
`getenforce`
If it shows "Enforcing", try
`setenforce 0`
should fix your issue

SSL
---
```
<VirtualHost *:443>
   Include conf/ssl/ssl-standard.conf

   SSLCertificateFile      /etc/httpd/conf/ssl/*.crt
   SSLCertificateKeyFile   /etc/httpd/conf/ssl/*.key
   SSLCertificateChainFile /etc/httpd/conf/ssl/intermediate-cert.crt
</VirtualHost>
```
ssl-standard.conf
```
# ssl-standard.conf
# Basic Apache SSL options

SSLEngine on
#   SSL Cipher Suite:
#   List the ciphers that the client is permitted to negotiate.
#   See the mod_ssl documentation for a complete list.

SSLProtocol All -SSLv2 -SSLv3
SSLHonorCipherOrder on
SSLCipherSuite EECDH:ECDH:HIGH:+SHA1:-aNULL:-eNULL:-DSS:-ADH:-3DES:-RC4

#   Export the SSL environment variables to scripts
<Files ~ "\.(cgi|pl|shtml|phtml|php3?)$">
   SSLOptions +StdEnvVars
</Files>

#   Protocol adjustments for broken clients
#SetEnvIf User-Agent ".*MSIE.*" \
SetEnvIf User-Agent "MSIE [2-5]" \
   nokeepalive ssl-unclean-shutdown \
   downgrade-1.0 force-response-1.0
```

setup Mac for php development
---
```shell
brew tap homebrew/dupes
brew tap homebrew/versions
brew tap homebrew/homebrew-php
brew install php56 --with-homebrew-curl --without-bz2 --without-apache --without-fpm --without-ldap --without-mysql
brew install mcrypt php56-mcrypt
curl -sS https://getcomposer.org/installer | php
sudo mv composer.phar /usr/local/bin/composer
```
