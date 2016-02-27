support multiple configuration paths
---
```node
    // We support multiple configuration paths as follows:
    //
    // 1. Use the project 'config' folder, if it exists.
    // 2. "actionhero --config=PATH1 --config=PATH2 --config=PATH3,PATH4"
    // 3. "ACTIONHERO_CONFIG=PATH1,PATH2 npm start"
    //
    // Note that if --config or ACTIONHERO_CONFIG are used, they _overwrite_ the use of the default "config" folder. If
    // you wish to use both, you need to re-specify "config", e.g. "--config=config,local-config". Also, note that
    // specifying multiple --config options on the command line does exactly the same thing as using one parameter with
    // comma separators, however the environment variable method only supports the comma-delimited syntax.
    var configPaths = [];

    function addConfigPath(pathToCheck, alreadySplit) {
      if (typeof pathToCheck === 'string') {
        if (!alreadySplit) {
          addConfigPath(pathToCheck.split(','), true);
        }
        else {
          if (pathToCheck.charAt(0) !== '/') pathToCheck = path.resolve(api.projectRoot, pathToCheck);
          if (fs.existsSync(pathToCheck)) {
            configPaths.push(pathToCheck);
          }
        }
      } else if (util.isArray(pathToCheck)) {
        pathToCheck.map(function(entry) {
          addConfigPath(entry, alreadySplit);
        });
      }
    }

    [argv.config, process.env.ACTIONHERO_CONFIG].map(function(entry) { addConfigPath(entry, false); });
  ```
get .js files recursively in a directory
---
```node
    // get all .js files in a directory
    api.utils.recursiveDirectoryGlob = function(dir, extension, followLinkFiles){
      var results = [];

      if(!extension){ extension = '.js'; }
      if(!followLinkFiles){ followLinkFiles = true; }

      extension = extension.replace('.','');
      if(dir[dir.length - 1] !== path.sep){ dir += path.sep; }

      if(fs.existsSync(dir)){
        fs.readdirSync(dir).forEach( function(file) {
          var fullFilePath = path.normalize(dir + file);
          if(file[0] !== '.'){ // ignore 'system' files
            var stats = fs.statSync(fullFilePath);
            var child;
            if(stats.isDirectory()){
              child = api.utils.recursiveDirectoryGlob(fullFilePath, extension, followLinkFiles);
              child.forEach(function(c){ results.push(c); });
            } else if(stats.isSymbolicLink()){
              var realPath = fs.readlinkSync(fullFilePath);
              child = api.utils.recursiveDirectoryGlob(realPath, extension, followLinkFiles);
              child.forEach(function(c){ results.push(c); });
            } else if(stats.isFile()){
              var fileParts = file.split('.');
              var ext = fileParts[(fileParts.length - 1)];
              // real file match
              if(ext === extension){ results.push(fullFilePath); }
              // linkfile traversal
              if(ext === 'link' && followLinkFiles === true){
                var linkedPath = api.utils.sourceRelativeLinkPath(fullFilePath, api.config.general.paths.plugin);
                if(linkedPath){
                  child = api.utils.recursiveDirectoryGlob(linkedPath, extension, followLinkFiles);
                  child.forEach(function(c){ results.push(c); });
                }else{
                  api.log(['cannot find linked refrence to `%s`', file], 'warning');
                }
              }
            }
          }
        });
      }

      return results.sort();
    };
```
multiple NodeJS processes for TCP/HTTP serving
---
[Multi-node](https://github.com/kriszyp/multi-node) provides launching of multiple NodeJS processes for TCP/HTTP serving.
```js
var server = require("http").createServer(function(request, response){
        ... standard node request handler ...
    });
var nodes = require("multi-node").listen({
        port: 80, 
        nodes: 4
    }, server);
var allStreams = [];
nodes.addListener("node", function(stream){
    stream.addListener("data", function(data){
        ... receiving data from this other node process ...
    });
    allStreams.push(stream);
});

function notifyOtherProcesses(message){
    allStreams.forEach(function(stream){
        stream.write(message);
    });
}

nodes.addListener("node", function(stream){
    stream = require("multi-node").frameStream(stream);
    stream.addListener("message", function(data){
        ... receiving string, object, or other value from the other node process ...
    });
    stream.send({foo:"bar"});
});
```
daemonize
---
```js
function daemonize() {
  if (process.env.IS_DAEMONIC) return;

  var spawn = require('child_process').spawn
    , argv = process.argv.slice()
    , code;

  argv = argv.map(function(arg) {
    arg = arg.replace(/(["$\\])/g, '\\$1');
    return '"' + arg + '"';
  }).join(' ');

  code = '(IS_DAEMONIC=1 setsid ' + argv + ' > /dev/null 2>& 1 &)';
  spawn('/bin/sh', ['-c', code]).on('exit', function(code) {
    process.exit(code || 0);
  });

  stop();
}
```
killall
---
```js
function killall() {
  var spawn = require('child_process').spawn;

  var options = {
    cwd: process.cwd(),
    env: process.env,
    setsid: false,
    customFds: [0, 1, 2]
  };

  spawn('/bin/sh',
    ['-c', 'kill $(ps ax | grep -v grep | grep tty.js | awk \'{print $1}\')'],
    options);

  stop();
}
```
detaching a long-running process and redirecting its output to a file:
---
```js
const fs = require('fs');
const spawn = require('child_process').spawn;
const out = fs.openSync('./out.log', 'a');
const err = fs.openSync('./out.log', 'a');

const child = spawn('prg', [], {
 detached: true,
 stdio: [ 'ignore', out, err ]
});

child.unref();
```
pipe
---
```js
Stream.prototype.pipe = function(dest, options) {
  var src = this
    , ondata
    , onerror
    , onend;

  function unbind() {
    src.removeListener('data', ondata);
    src.removeListener('error', onerror);
    src.removeListener('end', onend);
    dest.removeListener('error', onerror);
    dest.removeListener('close', unbind);
  }

  src.on('data', ondata = function(data) {
    dest.write(data);
  });

  src.on('error', onerror = function(err) {
    unbind();
    if (!this.listeners('error').length) {
      throw err;
    }
  });

  src.on('end', onend = function() {
    dest.end();
    unbind();
  });

  dest.on('error', onerror);
  dest.on('close', unbind);

  dest.emit('pipe', src);

  return dest;
};
```
