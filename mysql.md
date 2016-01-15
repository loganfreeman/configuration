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
# SET PASSWORD FOR 'root'@'localhost' = PASSWORD('pass');
FLUSH PRIVILEGES;  
\q
```
