browserify
---
Returns a new browserify instance.
```js
function bundler(entry) {
  var babelify = require('babelify'),
      watchify = require('watchify'),
      browserify = require('browserify');

  var globalShim = require('browserify-global-shim').configure({
    "react": "React",
    "d3": "d3"
  });
  var opts = {
          entries: entry // Only need initial file, browserify finds the deps
          , standalone: 'rd3' // enable the build to have UMD and expose window.rsc if no module system is used
          , extensions: [ '.jsx', '.js' ]
          , fullPaths: false
        };
  if (!config.production) {
    //opts.entries = watchEntry // building the demo
    opts.debug = true // Gives us sourcemapping
    opts.cache = {}; // Requirement of watchify
    opts.packageCache = {}; // Requirement of watchify
    opts.fullPaths = true; // Requirement of watchify
  }

  var bundler = browserify(opts);

  bundler
    .external(["react", "d3"]) // this informs browserify that when you see require("react") or require("d3") it will be available, trust me
    .transform(babelify) // We want to convert JSX to normal javascript
    .transform(globalShim) // replace require('react') and require('d3') with (window.React) and (window.d3)
    ;

  return config.production ? bundler : watchify(bundler);
}
```
