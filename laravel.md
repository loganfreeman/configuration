Installation
---
```shell
apt-get install nginx php5-fpm php5-cli php5-mcrypt git
mkdir -p /var/www/laravel
nano /etc/nginx/sites-available/default
```
The configuration should look like below:
```
server {
        listen   80 default_server;

        root /var/www/laravel/public/;
        index index.php index.html index.htm;

        location / {
             try_files $uri $uri/ /index.php$is_args$args;
        }

        # pass the PHP scripts to FastCGI server listening on /var/run/php5-fpm.sock
        location ~ \.php$ {
                try_files $uri /index.php =404;
                fastcgi_pass unix:/var/run/php5-fpm.sock;
                fastcgi_index index.php;
                fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
                include fastcgi_params;
        }
}
```
`/etc/php5/fpm/pool.d/www.conf`
```
listen = /var/run/php5-fpm.sock
```

```shell
curl -sS https://getcomposer.org/installer | php
mv composer.phar /usr/local/bin/composer
composer create-project laravel/laravel /var/www/laravel/ 4.1
php5enmod mcrypt
service php5-fpm restart
service nginx restart
```
