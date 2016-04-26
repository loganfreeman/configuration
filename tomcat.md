tomcat datasource
---
```
    <Resource name="jdbc/openspecimen" auth="Container" type="javax.sql.DataSource"
             maxTotal="100" maxIdle="30" maxWaitMillis="10000"
             username="root" password="pass22" driverClassName="com.mysql.jdbc.Driver"
             url="jdbc:mysql://localhost:3306/osdb"/>
```
