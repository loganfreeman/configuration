string to class
---
```py
def to_class(class_str):
    if not class_str:
        return None

    module_bits = class_str.split('.')
    module_path, class_name = '.'.join(module_bits[:-1]), module_bits[-1]
    module = importlib.import_module(module_path)
    return getattr(module, class_name, None)
```
multiprocessing
---
```py
    def compile(self, paths)
        def _compile(input_path)
            # implementation of _compile
            
        try:
            import multiprocessing
            from concurrent import futures
        except ImportError:
            return list(map(_compile, paths))
        else:
            with futures.ThreadPoolExecutor(max_workers=multiprocessing.cpu_count()) as executor:
                return list(executor.map(_compile, paths))
```
cross platform file opening
---
```py
import os, sys, subprocess

def open_file(filename):
    if sys.platform == "win32":
        os.startfile(filename)
    else:
        opener ="open" if sys.platform == "darwin" else "xdg-open"
        subprocess.call([opener, filename])
```
