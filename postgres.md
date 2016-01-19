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
Prepared Statements are enabled by default on PostgreSQL. You can be disable prepared statements by setting prepared_statements to false:
```
prepared_statements: false
```
