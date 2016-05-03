protractor.conf.js
---
```js
var fs = require('fs');
var path = require('path');
var httpServer = require('http-server');

startHttpServer();

exports.config = {
  baseUrl: 'http://localhost:8080/',

  seleniumServerJar: findSeleniumJar(),

  specs: [
    'test/**/*.e2e.js'
  ],

  useAllAngular2AppRoots: true
};


function startHttpServer() {
  var server = httpServer.createServer();
  server.listen(8080, function () {
    console.log('HTTP server started at http://localhost:8080');
  });
}

function findSeleniumJar() {
  var dir = './node_modules/protractor/selenium';

  var files = fs.readdirSync(dir);
  var jar = files.filter(function (file) {
    return /\.jar$/.test(file);
  })[0];

  if (!jar) {
    console.error('Selenium Server JAR was not found. Run `./node_modules/.bin/webdriver-manager update`.');
    process.exit(1);
  }

  return path.resolve(dir, jar);
}
```
package.json
---
```js
  "scripts": {
    "postinstall": "webdriver-manager update",
    "start": "gulp",
    "build": "gulp build",
    "watch": "gulp watch",
    "clean": "gulp clean",
    "test": "npm run unit && npm run e2e",
    "unit": "karma start --single-run",
    "tdd": "karma start",
    "e2e": "npm run build && protractor"
  }
```
gulp
---
```js
import gulp from 'gulp';
import gutil, { PluginError } from 'gulp-util';
import source from 'vinyl-source-stream';
import buffer from 'vinyl-buffer';
import sourcemaps from 'gulp-sourcemaps';

import assign from 'object-assign';
import browserify from 'browserify';
import watchify from 'watchify';
import babelify from 'babelify';

import del from 'del';

gulp.task('copy', () => {
  return gulp.src(['src/index.html'])
    .pipe(gulp.dest('public'));
});

gulp.task('build', ['copy'], () => {
  const b = browserify('src/index.js', { debug: true })
    .transform(babelify);
  return bundle(b);
});

gulp.task('watch', () => {
  const b = browserify('src/index.js', assign({ debug: true }, watchify.args))
    .transform(babelify);
  const w = watchify(b)
    .on('update', () => bundle(w))
    .on('log', gutil.log);
  return bundle(w)
});

gulp.task('clean', () => {
  return del('public');
});

gulp.task('default', ['copy', 'watch']);

function bundle(b) {
  return b.bundle()
    .on('error', (e) => {
      console.error(e.stack);
    })
    .pipe(source('bundle.js'))
    .pipe(buffer())
    .pipe(sourcemaps.init({ loadMaps: true }))
    .pipe(sourcemaps.write('./'))
    .pipe(gulp.dest('public'));
}
```
