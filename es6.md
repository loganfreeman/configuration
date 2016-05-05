extend
---
```js
function extend(dest, src1, src2) {
  var props = Object.keys(src1)
  for (var i = 0, l = props.length; i < l; i++) {
    dest[props[i]] = src1[props[i]]
  }
  if (src2) {
    props = Object.keys(src2)
    for (i = 0, l = props.length; i < l; i++) {
      dest[props[i]] = src2[props[i]]
    }
  }
  return dest
}

module.exports = extend
```
iterator interface
---
```js
interface IteratorResult {
  done: boolean;
  value: any;
}
interface Iterator {
  next(): IteratorResult;
}
interface Iterable {
  [Symbol.iterator](): Iterator
}
```
generator interface
---
```js
interface Generator extends Iterator {
    next(value?: any): IteratorResult;
    throw(exception: any);
}
```
A function declared as `function*` returns a Generator instance. 
```js
var fibonacci = {
  [Symbol.iterator]: function*() {
    var pre = 0, cur = 1;
    for (;;) {
      var temp = pre;
      pre = cur;
      cur += temp;
      yield cur;
    }
  }
}

for (var n of fibonacci) {
  // truncate the sequence at 1000
  if (n > 1000)
    break;
  console.log(n);
}
```
