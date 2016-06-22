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
test symblink
---
```js
function testSymlink (cb) {
    var testPath = path.join(tempBase, 'symlink-test')
    var testFile = path.join(testPath, 'test')
    var testLink = path.join(testPath, 'testlink')
    series([
      function (cb) {
        fs.outputFile(testFile, '', cb)
      },
      function (cb) {
        fs.symlink(testFile, testLink, cb)
      }
    ], function (err) {
      var result = !err
      fs.remove(testPath, function () {
        cb(result) // ignore errors on cleanup
      })
    })
  }
```
console
---
```js
class PackagerConsole {

    constructor() {
        this.logs = [];
    }

    _log(...messages) {
        this.logs.push(...messages);
    }

    override() {
        this.consoleError = console.error;

        // need to bind because somehow when _log() is called this refers to console
        console.error = this._log.bind(this);
    }

    restore() {
        console.error = this.consoleError;
    }

    playback() {
        console.log(this.logs.join(' '));
    }
}

export default PackagerConsole;
```
