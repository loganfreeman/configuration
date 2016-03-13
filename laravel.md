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
composer global require "laravel/installer"
export PATH=~/.composer/vendor/bin:$PATH
laravel new /var/www/laravel/
# composer create-project laravel/laravel /var/www/laravel/ 4.1
php5enmod mcrypt
service php5-fpm restart
service nginx restart
```
get all commands that are scheduled
---
```php
    /**
     * Get all commands that are scheduled
     *
     * @return \Indatus\Dispatcher\Scheduling\ScheduledCommandInterface[]
     */
    public function getScheduledCommands()
    {
        $scheduledCommands = [];
        foreach ($this->console->all() as $command) {
            if ($command instanceof ScheduledCommandInterface) {
                $scheduledCommands[] = $command;
            }
        }

        return $scheduledCommands;
    }
```
mcrypt
---
```shell
sudo apt-get install -y mcrypt php5-mcrypt
php -m | grep mcrypt # check if mcrypt module is loaded:
# if nothing shows:
php5enmod mcrypt
```

```
php artisan key:generate
```
the default cipher is
```
'cipher' => 'AES-256-CBC'
```
change it to
```
'cipher' => MCRYPT_RIJNDAEL_128
```
