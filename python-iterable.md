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
attribute getter
---
```py
class IterableAttr(object):

    def __init__(self, iterable, key):
        self._items = [item[key] for item in iterable]

    def __eq__(self, other):
        return other in self._items

    def __getitem__(self, key):
        return self.__class__(self._items, key)


def attrgetter(*items):

    if any(not isinstance(item, str) for item in items):
        raise TypeError('attribute name must be a string')
    if len(items) == 1:
        attr = items[0]
        def g(obj):
            return resolve_attr(obj, attr)
    else:
        def g(obj):
            return tuple(resolve_attr(obj, attr) for attr in items)
    return g

def resolve_attr(obj, attr):
    """A custom attrgetter that operates both on dictionaries and objects"""
    attr = attr.replace('__', '.') # replace lookups
    for name in attr.split("."):
        try:
            obj = getattr(obj, name)
        except AttributeError:
            try:
                try:
                    obj = obj[name]
                except TypeError:
                    obj = IterableAttr(obj, name)
            except (KeyError, TypeError):
                raise ValueError('Object {0} has no attribute or key "{1}"'.format(obj, name))
    return obj
```
