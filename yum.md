yum manual install
---
RPM package repository: http://dl.fedoraproject.org/pub/epel/7/x86_64/r/
```shell
curl -O http://dl.fedoraproject.org/pub/epel/7/x86_64/r/rubygem-rack-1.5.2-4.el7.noarch.rpm
rpm -Uvh rubygem-rack*rpm
yum install rubygem-rack
```
qmake
---
```
rpm -ql qt-devel | grep qmake # check qmake is in $PATH
yum install qt-devel # if not install it
```
