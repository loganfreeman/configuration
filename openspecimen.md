```
ps aux | grep catalina | head -n 1 | cut -d' ' -f2 | xargs kill -9
cd /home/openspecimen/deploy/tomcat-as/bin && ./catalina.sh start
```
