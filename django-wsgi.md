wsgi
---
```shell
virtualenv uwsgi-tutorial
cd uwsgi-tutorial
source bin/activate
pip install Django
django-admin.py startproject mysite
cd mysite
pip install uwsgi
uwsgi --http :8000 --module mysite.wsgi
```
sample wsgi file
---
```py
"""
WSGI config for mysite project.

It exposes the WSGI callable as a module-level variable named ``application``.

For more information on this file, see
https://docs.djangoproject.com/en/1.9/howto/deployment/wsgi/
"""

import os

from django.core.wsgi import get_wsgi_application

os.environ.setdefault("DJANGO_SETTINGS_MODULE", "mysite.settings")

application = get_wsgi_application()

```
nginx
---
```shell
mkvirtualenv firstsite
pip install django
django-admin.py startproject firstsite
cd firstsite
./manage.py migrate
./manage.py createsuperuser
echo "STATIC_ROOT = os.path.join(BASE_DIR, \"static/\")" >> firstsite/settings.py
./manage.py collectstatic
sudo nano /etc/nginx/sites-available/firstsite
```
`/etc/nginx/sites-available/firstsite` content
```
server {
    listen 80;
    server_name firstsite.com www.firstsite.com;

    location = /favicon.ico { access_log off; log_not_found off; }
    location /static/ {
        root /home/user/firstsite;
    }

    location / {
        include         uwsgi_params;
        uwsgi_pass      unix:/home/user/firstsite/firstsite.sock;
    }
}
```
passenger_wsgi.py
---
```py
 import sys, os
 cwd = os.getcwd()
 sys.path.append(cwd)
 sys.path.append(cwd + '/project')  #You must add your project here or 500

 #Switch to new python
 #You may try to replace $HOME with your actual path
 if sys.version < "2.7.3": os.execl("$HOME/<site>/env/bin/python",
     "python2.7.3", *sys.argv)

 sys.path.insert(0,'$HOME/.virtualenvs/<site>/bin')
 sys.path.insert(0,'$HOME/.virtualenvs/<site>/lib/python2.7/site-packages/django')
 sys.path.insert(0,'$HOME/.virtualenvs/<site>/lib/python2.7/site-packages')

 os.environ['DJANGO_SETTINGS_MODULE'] = "project.settings"
 import django.core.handlers.wsgi
 application = django.core.handlers.wsgi.WSGIHandler()
```
