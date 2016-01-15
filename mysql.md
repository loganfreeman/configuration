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
