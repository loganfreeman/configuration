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
