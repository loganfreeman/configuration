minimal setuptools
---
```python
from setuptools import setup, find_packages
setup(
    name = "HelloWorld",
    version = "0.1",
    packages = find_packages(),
)
```
find_package
---
> `find_packages()` takes a source directory and two lists of package name patterns to exclude and include. If omitted, the source directory defaults to the same directory as the setup script. Some projects use a src or lib directory as the root of their source tree, and those projects would of course use "src" or "lib" as the first argument to `find_packages()`.
