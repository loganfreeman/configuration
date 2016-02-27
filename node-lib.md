fs
---
```js
var fs = require('fs');
var path = require('path');

function getFilesFor(dir) {
  return fs.readdirSync(path.join(__dirname, '/' + dir))
    .map(function(file) {
      let superBlock;
      if (fs.statSync(path.join(__dirname, dir + '/' + file)).isFile()) {
        return { file: file };
      }
      superBlock = file;
      return getFilesFor(dir + '/' + superBlock)
        .map(function(data) {
          return {
            file: superBlock + '/' + data.file,
            superBlock: superBlock
          };
        });
    })
    .reduce(function(files, file) {
      if (!Array.isArray(file)) {
        files.push(file);
        return files;
      }
      return files.concat(file);
    }, []);
}
```
