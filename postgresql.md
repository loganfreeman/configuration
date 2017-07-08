Mac
---
```
export PATH=/Library/PostgreSQL/9.5/bin:$PATH
rm -rf /usr/local/var/postgres && initdb /usr/local/var/postgres -E utf8
pg_ctl -D /usr/local/var/postgres -l /usr/local/var/log/postgres.log start
```

Centos installation
---
``` shell
yum install postgresql-server
service postgresql initdb
chkconfig postgresql on
```

Set up postgresql
---
```shell
# Postgresql
if ! command -v psql; then
    apt-get install -y postgresql-$PGSQL_VERSION libpq-dev
    cp /vagrant_data/pg_hba.conf /etc/postgresql/$PGSQL_VERSION/main/
    /etc/init.d/postgresql reload
fi
```

How to configure rails to use postgres
---
If you choose to use PostgreSQL, your config/database.yml will be customized to use PostgreSQL databases:
```
development:
  adapter: postgresql
  encoding: unicode
  database: blog_development
  pool: 5
  username: blog
  password:
```
Prepared Statements are enabled by default on PostgreSQL. You can be disable prepared statements by setting prepared_statements to false.
If enabled, Active Record will create up to 1000 prepared statements per database connection by default. To modify this behavior you can set statement_limit to a different value.
The more prepared statements in use: the more memory your database will require. If your PostgreSQL database is hitting memory limits, try lowering statement_limit or disabling prepared statements.
```
adapter: postgresql
prepared_statements: false
statement_limit: 200
```
How to Create Roles in PostgreSQL
---
```sql
CREATE ROLE demo_role;
ALTER ROLE demo_role WITH LOGIN;
# give the new role a password
\password demo_role
create database demo_role
GRANT permission_type ON table_name TO role_name; # type: UPDATE, INSERT, ALL, etc.
GRANT permission_type ON DATABASE database_name TO role_name;
REVOKE permission_type ON table_name FROM user_name;
```

```sql
CREATE ROLE redmine LOGIN ENCRYPTED PASSWORD 'my_password' NOINHERIT VALID UNTIL 'infinity';
CREATE DATABASE redmine WITH ENCODING='UTF8' OWNER=redmine;
```

The postgres Linux account, being associated with the Postgres administrative role, has access to some utilities to create users and databases.
```shell
sudo -i -u postgres
createuser --interactive
createdb test1
```
Postgresql backups
---
```shell
cat /etc/cron.d/backupfsn.sh
#!/bin/bash
backup_dir="/var/log/"
timeslot=`date +%u`
i=dbnametobackup
/usr/bin/pg_dump -U postgres $i | gzip > "$backup_dir/postgresql-$i-$timeslot-database.gz"
```
install pgsql dirver for php on Mac
---
```
curl -O http://pear.php.net/go-pear.phar
sudo php -d detect_unicode=0 go-pear.phar
pecl download pdo_pgsql
tar xzf PDO_PGSQL-1.0.2.tgz
cd PDO_PGSQL-1.0.2
phpize
./configure --with-pdo-pgsql=/path/to/your/PostgreSQL/installation
make && sudo make install
```
add extension to php.ini
```
extension=pdo_pgsql.so
extension_dir = "/usr/lib/php/extensions/no-debug-non-zts-20060613"
```
Enabling PostgreSQL support in PHP on Mac OS X
---

- Find your version of PHP: php -v.
- Download the version of PHP that matches yours: `curl -O http://us.php.net/distributions/php-5.3.3.tar.gz`.
- Extract the archive you downloaded: `tar -xzvf php-5.3.3.tar.gz`
- Change to the PostgreSQL's extension directory: `cd php-5.3.3/ext/pgsql/`
- Type `phpize`.
- Type `./configure`.
- Type `make`.
- Type `sudo make install`.
- Add the extension to you php.ini file by adding `extension=pgsql.so`. (You may already have done this)
- Restart Apache.
