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
bundle
---
***b.bundle(cb)***

Bundle the files and their dependencies into a single javascript file.

Return a readable stream with the javascript file contents or optionally specify a cb(err, buf) to get the buffered results.

***stream = sourceStream([filename])***

Creates a through stream which takes text as input, and emits a single vinyl file instance for streams down the pipeline to consume.

filename is a "pretend" filename to use for your file, which some streams might use to determine various factors such as the final filename of your file. It should be a string, and though recommended, using this argument is optional.

```js
function compileJS(entry) {

  var w = bundler(entry);

  if (!config.production) {
    w.on("update", function (e) {
      var updateStart = Date.now();

      bundleShare(w);
      console.log('file changed %s', e)
    });
  }

  // called on completion of build
  w.on('time', function (time) {
      if (!config.production) {
        console.log('Bundle updated in %s ms', time);
        browserSync.reload();
      }
    })
  .on('error', console.error.bind(console));

  return bundleShare(w);
}

function bundleShare(b) {
  return b.bundle()
    .on('error', function(err){
      console.log(chalk.red(err.toString()));
      this.end();
    })
    .pipe(source('react-d3.js'))
    .pipe(buffer())
    .pipe(plugins.sourcemaps.init({loadMaps: true}))
    .pipe(plugins.sourcemaps.write('./'))
    .pipe(gulp.dest('./build/public/js'));
}
```
