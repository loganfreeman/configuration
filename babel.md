babel 6 presets
---
```shell
npm install babel-preset-es2015 babel-preset-react
babel --presets es2015,react --watch src/ --out-dir lib/
```
.babelrc
```
{
  “presets”: [“es2015”]
}
```
browsify
---
```shell
npm install --save react react-dom babelify babel-preset-react
browserify -t [ babelify ] main.js -o bundle.js
```
webpack
---
```shell
npm install --save react react-dom babel-preset-react babel-loader babel-core
webpack main.js bundle.js --module-bind 'js=babel-loader'
```
