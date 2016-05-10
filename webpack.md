webpack-dev-middleware
---
server.js
```js
var webpack = require('webpack')
var webpackDevMiddleware = require('webpack-dev-middleware')
var webpackHotMiddleware = require('webpack-hot-middleware')
var config = require('./webpack.config')

var app = new (require('express'))()
var port = 3000

var compiler = webpack(config)
app.use(webpackDevMiddleware(compiler, { noInfo: true, publicPath: config.output.publicPath }))
app.use(webpackHotMiddleware(compiler))

app.get("/", function(req, res) {
  res.sendFile(__dirname + '/index.html')
})

app.listen(port, function(error) {
  if (error) {
    console.error(error)
  } else {
    console.info("==> 🌎  Listening on port %s. Open up http://localhost:%s/ in your browser.", port, port)
  }
})
```
webpack.config.js
```js
var path = require('path')
var webpack = require('webpack')

module.exports = {
  devtool: 'cheap-module-eval-source-map',
  entry: [
    'webpack-hot-middleware/client',
    './index'
  ],
  output: {
    path: path.join(__dirname, 'dist'),
    filename: 'bundle.js',
    publicPath: '/static/'
  },
  plugins: [
    new webpack.optimize.OccurenceOrderPlugin(),
    new webpack.HotModuleReplacementPlugin()
  ],
  module: {
    loaders: [
      {
        test: /\.js$/,
        loaders: [ 'babel' ],
        exclude: /node_modules/,
        include: __dirname
      }
    ]
  }
}
```
context module API
---
```js
var req = require.context("./templates", true, /^\.\/.*\.jade$/);

var tableTemplate = req("./table.jade");
// tableTemplate === require("./templates/table.jade");

var tableTemplateId = req.resolve("./table.jade");
// tableTemplateId === require.resolve("./templates/table.jade");

req.keys();
// is ["./table.jade", "./table-row.jade", "./directory/folder.jade"]

req.id;
// is i. e. 42
function requireAll(requireContext) {
  return requireContext.keys().map(requireContext);
}
// requires and returns all modules that match

var modules = requireAll(require.context("./spec", true, /^\.\/.*\.js$/));
// is an array containing all the matching modules
```
Example usage
```js
const integrationContext = require.context('./integration', true, /\.(js|jsx)$/);
integrationContext.keys().forEach(integrationContext);
const unitContext = require.context('../src/', true, /\.spec\.(js|jsx)$/);
unitContext.keys().forEach(unitContext);
```
PostCSS for Webpack
---
```js
var precss       = require('precss');
var autoprefixer = require('autoprefixer');

module.exports = {
    module: {
        loaders: [
            {
                test:   /\.css$/,
                loader: "style-loader!css-loader!postcss-loader"
            }
        ]
    },
    postcss: function () {
        return [precss, autoprefixer];
    }
}
```
express
---
```js
var fs = require('fs')
var path = require('path')

var express = require('express')

var PUBLIC_PATH = path.resolve(__dirname, 'public')

var app = express()

try {
  var publicBuildPath = path.resolve(PUBLIC_PATH, 'build')
  if (fs.statSync(publicBuildPath)) {
    console.log('Using static build from ' + publicBuildPath)
  }
}
catch (err) {
  console.log('Using webpack-dev-middleware')
  var webpack = require('webpack')
  var webpackConfig = require('./webpack.config')
  var webpackCompiler = webpack(webpackConfig)
  app.use(require('webpack-dev-middleware')(webpackCompiler, {
    noInfo: true,
    publicPath: webpackConfig.output.publicPath
  }))
  app.use(require('webpack-hot-middleware')(webpackCompiler))
}

app.use(express.static(PUBLIC_PATH))

app.listen(3000, '0.0.0.0', function(err) {
  if (err) {
    console.error('Error starting dev server:')
    console.error(err.stack)
    process.exit(1)
  }
  console.log('dev server running on http://127.0.0.1:3000')
})
```
