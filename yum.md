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
How to install RHEL EPEL repository on Centos 7.x or RHEL 7.x
---
```shell
# method 1 (recommended)
sudo yum remove epel-release # remove old version
sudo yum install epel-release
# method 2 (manually)
cd /tmp
wget https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
ls *.rpm
yum -Uvh epel-release*rpm
sudo yum install epel-release-7.noarch.rpm
# list your new repo
sudo yum repolist | grep epel
# To list all available packages under a repo called epel, enter:
sudo yum --disablerepo="*" --enablerepo="epel" list available | grep 'qtwebkit'
```
To install qtwebkit from epel
---
```shell
yum install qtwebkit-devel # will work with the dash in between qt and webkit, but not if you are using chef
ln -s /usr/lib64/qt4/bin/qmake /usr/bin/qmake # or some other method to get qmake in your path like `export QMAKE`
```
