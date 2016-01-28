implicit context or side effect
---
> using functions and procedures with as few implicit contexts and side-effects as possible. A function’s implicit context is made up of any of the global variables or items in the persistence layer that are accessed from within the function. Side-effects are the changes that a function makes to its implicit context. If a function saves or deletes data in a global variable or in the persistence layer, it is said to have a side-effect.

Carefully isolating functions with context and side-effects from functions with logic (called pure functions) allow the following benefits:

- Pure functions are deterministic: given a fixed input, the output will always be the same.
- Pure functions are much easier to change or replace if they need to be refactored or optimized.
- Pure functions are easier to test with unit-tests: There is less need for complex context setup and data cleaning afterwards.
- Pure functions are easier to manipulate, decorate, and pass around.

Context Managers
---
```python
class CustomOpen(object):
    def __init__(self, filename):
      self.file = open(filename)

    def __enter__(self):
        return self.file

    def __exit__(self, ctx_type, ctx_value, ctx_traceback):
        self.file.close()

with CustomOpen('file') as f:
    contents = f.read()
```

```python
from contextlib import contextmanager

@contextmanager
def custom_open(filename):
    f = open(filename)
    yield f
    f.close()

with custom_open('file') as f:
    contents = f.read()
```
