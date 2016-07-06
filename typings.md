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
factory pattern
---
```typescript

interface Block {
// omitted
}

interface BlockConstructor {
  new(index: number): Block;
}

abstract class BaseBlock implements Block {
  // omitted
  static factory(meta: BoardMeta): BlockConstructor {
    let blockType = class RuntimeBlock extends BaseBlock {};

    Object.defineProperty(blockType.prototype, 'meta', {
      configurable: false,
      enumerable: false,
      value: meta,
      writable: false
    });

    return blockType;
  }
}
```
