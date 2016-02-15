PIP_DOWNLOAD_CACHE
---
Added support for an environmental variable `$PIP_DOWNLOAD_CACHE` which will cache package downloads, so future installations won’t require large downloads. Network access is still required, but just some downloads will be avoided when using this.

To take advantage of this, I've added the following to my ~/.bash_profile:

`export PIP_DOWNLOAD_CACHE=$HOME/.pip_download_cache`
```shell
# install our common Python packages in a temporary virtual env so that they'll get cached
if [[ ! -e /home/vagrant/.pip_download_cache ]]; then
    su - vagrant -c "mkdir -p /home/vagrant/.pip_download_cache && \
        virtualenv /home/vagrant/yayforcaching && \
        PIP_DOWNLOAD_CACHE=/home/vagrant/.pip_download_cache /home/vagrant/yayforcaching/bin/pip install -r /vagrant_data/common_requirements.txt && \
        rm -rf /home/vagrant/yayforcaching"
fi
```
How to install pip on Centos7
---
```shell
rpm -iUvh http://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-5.noarch.rpm
yum -y update
yum -y install python-pip
pip -V
```
How to install virtualenv on Centos
---
```shell
sudo rpm -Uvh http://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-2.noarch.rpm
sudo yum -y install python-virtualenv
sudo yum -y install python-virtualenvwrapper
export WORKON_HOME=~/.virtualenvs
source /usr/bin/virtualenvwrapper.sh
```
The `~/.virtualenvs` directory is where the above virtual environments will reside. This directory path was chosen because the EPEL python-virtualenvwrapper package will set this path globally for all users in `/etc/profile.d/virtualenvwrapper.sh`

Your first virtual environment can now be created.
```shell
mkvirtualenv myapp
```
You can leave the virtual environment at any time with the following command.
```shell
deactivate
```
The following will return to, or "activate", the virtual environment that was previously created.
```shell
workon myapp
pip install django
```
The Django package files will be installed under `~/.virtualenvs/myapp`. Now you can create Django project.
```shell
django-admin.py startproject myproject
cd myproject
python manage.py migrate
python manage.py runserver
```
