Reset MySQL root Password
---
```shell
# first find mysql path
which mysql  # /usr/local/bin/mysql
python -c "import os; print os.path.realpath('/usr/local/bin/mysql')"
# then stop mysql service
sudo /usr/local/mysql/support-files/mysql.server stop
# then start in safe mode
sudo /usr/local/mysql/bin/mysqld_safe --skip-grant-tables # this should be kept open
# in another terminal
mysql -u root
UPDATE mysql.user SET Password=PASSWORD('pass') WHERE User='root';
FLUSH PRIVILEGES;  
\q
```

if you are using MySQL 5.7 then you have to do this instead of the commands stated above. Because in MySQL 5.7, the password field in mysql.user table is removed, now the field name is 'authentication_string'.

```sql
UPDATE mysql.user SET authentication_string=PASSWORD('my-new-password') WHERE User='root';  
FLUSH PRIVILEGES;  
\q
```
Now again restart the mysql server
```shell
sudo /usr/local/mysql/support-files/mysql.server start
```

create database
---
```sql
create database database_name;
```

create user and grant privilidges
---
```mysql
CREATE USER 'newuser'@'localhost' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON * . * TO 'newuser'@'localhost'; # The asterisks in this command refer to the database and table (respectively)
FLUSH PRIVILEGES;
```
Automated MySQL Database Backup
---
Want to backup your MySQL databases to another machine on a nightly basis? 

Then create a `/etc/cron.daily/mysqlbackup` job like this:
```shell
#!/bin/sh
mysqldump --compress -u root -p$pw -h $currenthost --add-drop-table --extended-insert
--quote-names --databases db1 db2| mysql -u root -p$pw -h $remotehost
```
or backup to a file
```shell
#!/bin/sh
mysqldump -u root -p$pw -h $currenthost --add-drop-table --extended-insert --quote-names --databases db1 db2 > /var/log/mysql.backup.$(date +"%Y%m%d").sql
```
