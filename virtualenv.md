virtualenv global setup
---
```shell
if ! command -v pip; then
    easy_install -U pip
fi
if [[ ! -f /usr/local/bin/virtualenv ]]; then
    easy_install virtualenv virtualenvwrapper stevedore virtualenv-clone
fi
```
Install virtualenv
---
```shell
sudo yum -y install python-virtualenv
sudo yum -y install python-virtualenvwrapper
```
