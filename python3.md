paperless
---
```shell
git clone https://github.com/danielquinn/paperless.git 
cd paperless
virtualenv --python=/path/to/python3 .
. bin/activate
pip install --requirement requirement.txt
```
Install python3 on centos
---
```shell
wget https://www.python.org/ftp/python/3.4.3/Python-3.4.3.tar.xz
tar xf Python-3.* 
cd Python-3.* 
./configure
make
make install
make altinstall # don't overwrite default installation of python
```
using yum
```shell
yum -y install scl-utils
rpm -Uvh https://www.softwarecollections.org/en/scls/rhscl/python33/epel-7-x86_64/download/rhscl-python33-epel-7-x86_64.noarch.rpm
yum -y install python33 # python version 3.3
scl enable python33 bash
python -V # 3.3
easy_install pip
```
