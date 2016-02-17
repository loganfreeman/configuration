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
