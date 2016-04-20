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
Install virtualenv & virtualenvwrapper
---
```shell
sudo yum -y install python-virtualenv
sudo yum -y install python-virtualenvwrapper
export WORKON_HOME=~/.virtualenvs
source /usr/bin/virtualenvwrapper.sh
```
Using virtualenvwrapper
---
```shell
mkvirtualenv myapp
workon myapp
pip install <package_name> # The <package_name> package files will be installed under ~/.virtualenvs/myapp.
pip install django
django-admin.py startproject myproject
cd myproject
python manage.py migrate
python manage.py runserver
```
Using virtualenv
---
```shell
# run scl-enabled shell and create the virtual environment
scl enable python33 bash
virtualenv foo
cd foo
source bin/activate
 
# test your virtualenv by installing Django and printing its version
pip install django
python -c "import django; print(django.__file__)"
 
# now just run "deactivate" to deactivate the environment
# in current shell session
deactivate
# or just "exit" the current shell, which both terminates
# the virtual environment and SCL-enabled shell
exit
```
Using requirements.txt
---
```shell
$ virtualenv --python=/path/to/python3 /path/to/arbitrary/directory
$ . /path/to/arbitrary/directory/bin/activate
$ pip install  --requirement /path/to/paperless/requirements.txt
```
always copy
---
> Always copy files rather than symlinking.

using virtualenv
---
```shell
# create a virtual environment
virtualenv -p python3 ~/.venv/pycraft # (or wherever)
# you may need to add execute permissions
chmod -R a+x ~/.venv
# activate
. ~/.venv/pycraft/bin/activate # on mac
. ~/.venv/pycraft/Scripts/activate # on windows
# deactivate (when you're done)
deactivate
```
