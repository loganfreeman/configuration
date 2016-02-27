browser-sync and nodemon
---
```js
var reload = require('browser-sync').reload;
gulp.task('serve', ['build-manifest'], function(cb) {
  var called = false;
  nodemon({
    script: paths.server,
    ext: '.jsx .js .json',
    ignore: paths.serverIgnore,
    exec: path.join(__dirname, 'node_modules/.bin/babel-node'),
    env: {
      'NODE_ENV': process.env.NODE_ENV || 'development',
      'DEBUG': process.env.DEBUG || 'freecc:*'
    }
  })
    .on('start', function() {
      if (!called) {
        called = true;
        setTimeout(function() {
          cb();
        }, reloadDelay);
      }
    })
    .on('restart', function(files) {
      if (files) {
        debug('Files that changes: ', files);
      }
      setTimeout(function() {
        debug('Restarting browsers');
        reload();
      }, reloadDelay);
    });
});
```
