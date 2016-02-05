set up nginx for hhvm
---
```shell
#!/usr/bin/env bash

mkdir /etc/nginx/ssl 2>/dev/null

PATH_SSL="/etc/nginx/ssl"
PATH_KEY="${PATH_SSL}/${1}.key"
PATH_CSR="${PATH_SSL}/${1}.csr"
PATH_CRT="${PATH_SSL}/${1}.crt"

if [ ! -f $PATH_KEY ] || [ ! -f $PATH_CSR ] || [ ! -f $PATH_CRT ]
then
  openssl genrsa -out "$PATH_KEY" 2048 2>/dev/null
  openssl req -new -key "$PATH_KEY" -out "$PATH_CSR" -subj "/CN=$1/O=Vagrant/C=UK" 2>/dev/null
  openssl x509 -req -days 365 -in "$PATH_CSR" -signkey "$PATH_KEY" -out "$PATH_CRT" 2>/dev/null
fi

block="server {
    listen ${3:-80};
    listen ${4:-443} ssl;
    server_name $1;
    root \"$2\";
    index index.html index.htm index.php;
    charset utf-8;
    location / {
        try_files \$uri \$uri/ /index.php?\$query_string;
    }
    location = /favicon.ico { access_log off; log_not_found off; }
    location = /robots.txt  { access_log off; log_not_found off; }
    access_log off;
    error_log  /var/log/nginx/$1-error.log error;
    sendfile off;
    location ~ \.php$ {
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass 127.0.0.1:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME \$document_root\$fastcgi_script_name;
        include fastcgi_params;
    }
    location ~ /\.ht {
        deny all;
    }
    ssl_certificate     /etc/nginx/ssl/$1.crt;
    ssl_certificate_key /etc/nginx/ssl/$1.key;
}
"

echo "$block" > "/etc/nginx/sites-available/$1"
ln -fs "/etc/nginx/sites-available/$1" "/etc/nginx/sites-enabled/$1"
service nginx restart
service php7.0-fpm restart
service hhvm restart
```
maintenance page
---
```ruby
server {
  passenger_enabled on;

  server_name yoursite.com;
  ...

  if (-f $document_root/system/maintenance.html) {
    rewrite ^(.*)$ /system/maintenance.html break;
  }

}
```
capistrano task
```ruby
# maintenance page
namespace :deploy do
  namespace :web do
    desc <<-DESC
      Present a maintenance page to visitors.
        $ cap deploy:web:disable REASON="a hardware upgrade" UNTIL="12pm Central Time"
    DESC

    task :disable do
      on roles(:web) do
        execute "rm #{shared_path}/system/maintenance.html" rescue nil

        require 'erb'
        reason = ENV['REASON']
        deadline = ENV['UNTIL']
        template = File.read('app/views/admin/maintenance.html.haml')
        #page = ERB.new(template).result(binding)
        content = ERB.new(template).result(binding)

        path = "public/system/maintenance.html"
        File.open(path, "w") { |f| f.write content }

        upload! path, "#{shared_path}/public/system/maintenance.html", :mode => 0644
      end

    end

    task :enable do
      on roles(:web) do
        execute "rm #{shared_path}/public/system/maintenance.html"
      end
    end

  end
end
```
