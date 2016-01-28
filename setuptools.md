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

Modules
---
> Python modules are one of the main abstraction layers available and probably the most natural one. Abstraction layers allow separating code into parts holding related data and functionality.
> As soon as you use import statements you use modules. These can be either built-in modules such as os and sys, third-party modules you have installed in your environment, or your project’s internal modules.
> Concretely, the import modu statement will look for the proper file, which is modu.py in the same directory as the caller if it exists. If it is not found, the Python interpreter will search for modu.py in the “path” recursively and raise an ImportError exception if it is not found.
> Once modu.py is found, the Python interpreter will execute the module in an isolated scope. Any top-level statement in modu.py will be executed, including other imports if any. Function and class definitions are stored in the module’s dictionary.

__A file modu.py in the directory pack/ is imported with the statement import pack.modu. __

Packages
---
> Any directory with an __init__.py file is considered a Python package. 
> 
