typings install
---
```shell
./node_modules/.bin/typings install <module_name> --save --ambient
```
Where Do The Type Definitions Install?
---
Typings are compiled and written into the typings/ directory alongside typings.json (by default). The structure looks like this:
```
typings/{globals,modules}/<dependency>/index.d.ts
typings/index.d.ts
```
to get both "main" and "browser" typings, a la Typings 0.x:
```
{
  "resolution": {
    "main": "typings/main",
    "browser": "typings/browser"
  }
}
```
