Serving static files during development
---
```py
from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [
    # ... the rest of your URLconf goes here ...
] + static(settings.STATIC_URL, document_root=settings.STATIC_ROOT)
```
serve static files in production
---
```shell
./manage.py collectstatic
```

nginx setting
```
            location /static/ {
                    # root /var/www/app/static/;
                    alias /var/www/app/static/;
                    autoindex off;
            }

```
