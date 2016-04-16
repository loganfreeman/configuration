```js
var gulp = require('gulp');
var sourcemaps = require('gulp-sourcemaps');
var react = require('gulp-react');

gulp.task('default', function () {
    return gulp.src('template.jsx')
        .pipe(sourcemaps.init())
        .pipe(react())
        .pipe(sourcemaps.write('.'))
        .pipe(gulp.dest('dist'));
});
```


```js
gulp.task('lint', function () {
  var jshint = require('gulp-jshint');
  var react = require('gulp-react');
  var stylish = require('jshint-stylish');

  var jshintConfig = {
    "browserify": true,
    "expr": true,
    "esnext": true,
    "eqnull": true,
    "globals": {
      "describe": false,
      "it": false,
      "before": false,
      "beforeEach": false,
      "after": false,
      "afterEach": false
    }
  }

  return gulp.src(['src/**/*.js', 'src/**/*.jsx'])
        .pipe(react({harmony: true}))
        .pipe(jshint(jshintConfig))
        .pipe(jshint.reporter(stylish))
        ;
});
```
