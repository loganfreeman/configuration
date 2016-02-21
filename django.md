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
