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
[Link](https://mallinson.ca/osx-web-development/)
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

setup dnsmasq
---
```shell
brew install dnsmasq
cd $(brew --prefix)
mkdir -p etc
echo 'address=/.dev/127.0.0.1' > etc/dnsmasq.conf
sudo cp -v $(brew --prefix dnsmasq)/homebrew.mxcl.dnsmasq.plist /Library/LaunchDaemons
sudo launchctl load -w /Library/LaunchDaemons/homebrew.mxcl.dnsmasq.plist
sudo mkdir /etc/resolver
sudo bash -c 'echo "nameserver 127.0.0.1" > /etc/resolver/dev'
```
dnsmasp
---
This configuration allows you to use the URL `http://client1.dev` on your local machine with your site files stored in `/www/sites/client1/wwwroot`. It also allows you to use the service xip.io to use the URL `http://client1.[LOCAL IP].xip.io` to access your sites from another machine (or device) on your local network. So, if your machine’s local IP address (not your public IP address), is `192.168.1.10`, the URL for a site might be `http://client1.192.168.1.10.xip.io`. You can find your local IP address in your network preferences.

`Xip.io` is offered for free by Basecamp and provides one of the simplest services on the internet. They run a simple DNS server that redirects all traffic to `*.xip.io` back to the IP address indicated by the subdomain.

Now you don’t need to update config files every time you add a new site. Simply add the necessary folders to your “sites” directory, and the site will work locally with its own subdomain.
```
<Directory "/www">
  Options Indexes MultiViews FollowSymLinks
  AllowOverride All
  Order allow,deny
  Allow from all
</Directory>

<Virtualhost *:80>
  VirtualDocumentRoot "/www/home/wwwroot"
  ServerName home.dev
  UseCanonicalName Off
</Virtualhost>

<Virtualhost *:80>
  VirtualDocumentRoot "/www/sites/%1/public"
  ServerName sites.dev
  ServerAlias *.dev
  UseCanonicalName Off
</Virtualhost>

<Virtualhost *:80>
  VirtualDocumentRoot "/www/sites/%-7+/wwwroot"
  ServerName xip
  ServerAlias *.xip.io
  UseCanonicalName Off
</Virtualhost>
```

upload your code the server
---
```shell
cd /path/to/ninja/code
chmod -R 755 storage
sudo chown -R www-data:www-data storage public/logo
```
multiple php
---
```shell
brew tap homebrew/dupes
brew tap homebrew/versions
brew tap homebrew/homebrew-php
brew install php54
brew unlink php54
brew install php55
brew unlink php55
brew install php56
brew unlink php56
brew install php70
```

PHP Switcher Script
---
```shell
mkdir -p ~/bin/
curl -L https://raw.githubusercontent.com/conradkleinespel/sphp-osx/master/sphp > ~/bin/sphp
chmod +x ~/bin/sphp
```
Add this to `httpd.conf`
```
# Brew PHP LoadModule for `sphp` switcher
LoadModule php5_module /usr/local/lib/libphp5.so
#LoadModule php7_module /usr/local/lib/libphp7.so
```
