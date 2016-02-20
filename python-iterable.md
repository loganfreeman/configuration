build filter
---
```py
    def _build_filter(self, **kwargs):
        """build a single filter function used to match arbitrary object"""

        def object_filter(obj):
            for key, value in kwargs.items():
                # we replace dango-like lookup by dots, so attrgetter can do his job

                getter = utils.attrgetter(key)
                if hasattr(value, '__call__'):
                    # User passed a callable for a custom comparison
                    if not value(getter(obj)):
                        return False
                else:
                    if not getter(obj) == value:
                        return False
            return True

        return object_filter
```
