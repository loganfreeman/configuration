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
