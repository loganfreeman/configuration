nvm
---
```shell
installNvm() {
  curl https://raw.githubusercontent.com/creationix/nvm/v0.13.1/install.sh | bash
  source ~/.bash_profile
}
```
pg on centos
---
```shell
yum -y install postgresql-libs postgresql-devel
rvmsudo gem install pg
```
