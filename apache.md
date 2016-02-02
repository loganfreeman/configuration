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
