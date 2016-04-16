```js
  var fs  = require("fs");
  var Handlebars = require('handlebars');
  var path = require('path');

  var pkg = require(path.join(__dirname, 'package.json'));

  // replacement for node scripts/build.js
  var packageTemplate = fs.readFileSync(path.join(__dirname, 'dist/cjs/package.json')).toString();
  var template = Handlebars.compile(packageTemplate);
  var buildPackage = template({pkg: pkg});
  try {
    JSON.parse(buildPackage);
  } catch (err) {
    console.error('package.json parse error: ', err);
    process.exit(1);
  }

  fs.writeFile(path.join(__dirname, 'build', 'cjs', 'package.json'), buildPackage, function(err) {
  if (err) console.log(err);
    else console.log('CJS package.json file rendered');
    cb();
  });
```
