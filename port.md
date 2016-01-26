Address already in use
---
```shell
On Linux/Unix run
$>  netstat -plant
$> # or
$> sudo lsof -i:80

On Windows run
$>  netstat -ano

On Mac OS X / FreeBSD run
$> netstat -Wan |grep 80
$> # or, to get the pid
$> sudo lsof -i:80
```
