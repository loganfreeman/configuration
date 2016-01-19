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
CREATE ROLE new_role_name;
```
The postgres Linux account, being associated with the Postgres administrative role, has access to some utilities to create users and databases.
```shell
sudo -i -u postgres
createuser --interactive
createdb test1
```
