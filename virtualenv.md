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
export WORKON_HOME=~/.virtualenvs
source /usr/bin/virtualenvwrapper.sh
mkvirtualenv myapp
workon myapp
pip install <package_name> # The <package_name> package files will be installed under ~/.virtualenvs/myapp.
pip install django
django-admin.py startproject myproject
cd myproject
python manage.py migrate
python manage.py runserver
```
