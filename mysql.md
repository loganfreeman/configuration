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

```shell
#!/bin/sh

# List of databases to be backed up separated by space
dblist="db_name_1 db_name_2 db_name_3 etc."

# Directory for backups
backupdir=/root/mysql_dumps

# Number of versions to keep
numversions=4

# Full path for MySQL hotcopy command
# Please put credentials into /root/.my.cnf
#hotcopycmd=/usr/bin/mysqlhotcopy
hotcopycmd="/usr/bin/mysqldump --lock-tables --databases"

# Create directory if needed
mkdir -p "$backupdir"
if [ ! -d "$backupdir" ]; then
   echo "Invalid directory: $backupdir"
   exit 1
fi

# Hotcopy begins here
echo "Dumping MySQL Databases..."
RC=0
for database in $dblist; do
   echo
   echo "Dumping $database ..."
   mv "$backupdir/$database.gz" "$backupdir/$database.0.gz" 2> /dev/null
   $hotcopycmd $database | gzip > "$backupdir/$database.gz"

   RC=$?
   if [ $RC -gt 0 ]; then
     continue;
   fi

   # Rollover the backup directories
   rm -fr "$backupdir/$database.$numversions.gz" 2> /dev/null
   i=$numversions
   while [ $i -gt 0 ]; do
     mv "$backupdir/$database.`expr $i - 1`.gz" "$backupdir/$database.$i.gz" 2> /dev/null
     i=`expr $i - 1`
   done
done

if [ $RC -gt 0 ]; then
   echo "MySQL Dump failed!"
   exit $RC
else
   # Hotcopy is complete. List the backup versions!
   ls -l "$backupdir"
   echo "MySQL Dump is complete!"
fi
exit 0
```
`.my.cnf` – mysql user & password
---
This one is for lazy ones! If you are paranoid about security, do not use this.

Create file `~/.my.cnf` and add following lines in it and replace mysqluser & mysqlpass values.
```
[client]
user=mysqluser
password=mysqlpass
```
For safety, make this file readable to you only by running chmod 0600 ~/.my.cnf

Next time you run mysql commands mysql, mysqlcheck, mysqdump, etc; they will pick username & password from this file if you do not provide them as argument (-u and -p). It can save your time.
