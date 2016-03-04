accumalate the results of running node, once done, call callback
---
```js
'use strict';

var spawn  = require('child_process').spawn;
var path   = require('path');


function run(filename, callback) {
  var mochify = spawn('node', [path.resolve(__dirname, filename)], {
    cwd : path.resolve(__dirname, 'api')
  });

  var stdout = '';
  var handle = function (data) {
    stdout += data;
  };
  mochify.stdout.on('data', handle);
  mochify.stderr.on('data', handle);

  mochify.on('close', function (code) {
    callback(code, stdout);
  });
}

module.exports = run;
```
