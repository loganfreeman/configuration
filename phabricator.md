Installtion 
---
```shell
cd /var/www/sites
git clone https://github.com/phacility/phabricator.git
git clone https://github.com/phacility/libphutil.git
git clone https://github.com/phacility/arcanist.git
```

*Setup VirtualHost*
```

<Virtualhost *:80>
  VirtualDocumentRoot "/var/www/sites/dolibarr-3.9.1/htdocs"
  ServerName www.dolibarr.app
  UseCanonicalName Off
</Virtualhost>

<Virtualhost *:80>
  DocumentRoot "/var/www/sites/phabricator/webroot"
  ServerName www.webroot.app
  UseCanonicalName Off

  RewriteEngine on
  RewriteRule ^/rsrc/(.*)     -                       [L,QSA]
  RewriteRule ^/favicon.ico   -                       [L,QSA]
  RewriteRule ^(.*)$          /index.php?__path__=$1  [B,L,QSA]

  <Directory "/var/www/sites/phabricator/webroot">
    Require all granted
  </Directory>
</Virtualhost>
```

*Setup database configuration*

- First, write an `exampleconfig.conf.php` file here
```
phabricator/conf/custom/exampleconfig.conf.php
```
Its contents should look like this:
```
<?php

return array(
  'mysql.host' => 'localhost',
  'mysql.user' => 'root',
  'mysql.pass' => 'hunter2trustno1',
);
```
- Selecting a Configuration File
To select a configuration file, write the name of the file to `phabricator/conf/local/ENVIRONMENT`
```shell
echo custom/exampleconfig > conf/local/ENVIRONMENT
```

- Run the database migration script
```shell
./bin/storage upgrade
```
