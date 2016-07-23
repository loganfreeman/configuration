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
nodejs scripts
---
```js
#!/usr/bin/env node
var path = require('path');
var spawn = require('cross-spawn');
var script = process.argv[2];
var args = process.argv.slice(3);

switch (script) {
case 'build':
case 'start':
case 'eject':
  spawn(
    'node',
    [path.resolve(__dirname, '..', 'scripts', script)].concat(args),
    {stdio: 'inherit'}
  );
  break;
default:
  console.log('Unknown script "' + script + '".');
  console.log('Perhaps you need to update react-scripts?');
  break;
}
```
nodejs global-cli
---
```js
#!/usr/bin/env node

/**
 * Copyright (c) 2015-present, Facebook, Inc.
 * All rights reserved.
 *
 * This source code is licensed under the BSD-style license found in the
 * LICENSE file in the root directory of this source tree. An additional grant
 * of patent rights can be found in the PATENTS file in the same directory.
 */

// ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
//   /!\ DO NOT MODIFY THIS FILE /!\
// ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
//
// create-react-app is installed globally on people's computers. This means
// that it is extremely difficult to have them upgrade the version and
// because there's only one global version installed, it is very prone to
// breaking changes.
//
// The only job of create-react-app is to init the repository and then
// forward all the commands to the local version of create-react-app.
//
// If you need to add a new command, please add it to the scripts/ folder.
//
// The only reason to modify this file is to add more warnings and
// troubleshooting information for the `create-react-app` command.
//
// Do not make breaking changes! We absolutely don't want to have to
// tell people to update their global version of create-react-app.
//
// ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
//   /!\ DO NOT MODIFY THIS FILE /!\
// ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

'use strict';

var fs = require('fs');
var path = require('path');
var spawn = require('cross-spawn');
var chalk = require('chalk');
var semver = require('semver');
var argv = require('minimist')(process.argv.slice(2));

/**
 * Arguments:
 *   --version - to print current version
 *   --verbose - to print logs while init
 *   --scripts-version <alternative package>
 *     Example of valid values:
 *     - a specific npm version: "0.22.0-rc1"
 *     - a .tgz archive from any npm repo: "https://registry.npmjs.org/react-scripts/-/react-scripts-0.20.0.tgz"
 *     - a package prepared with `npm pack`: "/Users/home/vjeux/create-react-app/react-scripts-0.22.0.tgz"
 */
var commands = argv._;
if (commands.length === 0) {
  console.error(
    'Usage: create-react-app <project-directory> [--verbose]'
  );
  process.exit(1);
}

if (argv.version) {
  console.log('create-react-app version: ' + require('./package.json').version);
  process.exit();
}

createApp(commands[0], argv.verbose, argv['scripts-version']);

function createApp(name, verbose, version) {
  if (fs.existsSync(name)) {
    console.log('The directory `' + name + '` already exists. Aborting.');
    process.exit(1);
  }

  var root = path.resolve(name);
  var appName = path.basename(root);

  console.log(
    'Creating a new React app in ' + root + '.'
  );
  console.log();

  fs.mkdirSync(root);

  var packageJson = {
    name: appName,
    version: '0.0.1',
    private: true,
  };
  fs.writeFileSync(path.join(root, 'package.json'), JSON.stringify(packageJson));
  process.chdir(root);

  console.log('Installing packages. This might take a couple minutes.');
  console.log('Installing react-scripts from npm...');

  run(root, appName, version, verbose);
}

function run(root, appName, version, verbose) {
  var args = [
    'install',
    verbose && '--verbose',
    '--save-dev',
    '--save-exact',
    getInstallPackage(version),
  ].filter(function(e) { return e; });
  var proc = spawn('npm', args, {stdio: 'inherit'});
  proc.on('close', function (code) {
    if (code !== 0) {
      console.error('`npm ' + args.join(' ') + '` failed');
      return;
    }

    var scriptsPath = path.resolve(
      process.cwd(),
      'node_modules',
      'react-scripts',
      'scripts',
      'init.js'
    );
    var init = require(scriptsPath);
    init(root, appName, verbose);
  });
}

function getInstallPackage(version) {
  var packageToInstall = 'react-scripts';
  var validSemver = semver.valid(version);
  if (validSemver) {
    packageToInstall += '@' + validSemver;
  } else if (version) {
    // for tar.gz or alternative paths
    packageToInstall = version;
  }
  return packageToInstall;
}

function checkNodeVersion() {
  var packageJsonPath = path.resolve(
    process.cwd(),
    'node_modules',
    'react-scripts',
    'package.json'
  );
  var packageJson = require(packageJsonPath);
  if (!packageJson.engines || !packageJson.engines.node) {
    return;
  }

  if (!semver.satisfies(process.version, packageJson.engines.node)) {
    console.error(
      chalk.red(
        'You are currently running Node %s but create-react-app requires %s.' +
        ' Please use a supported version of Node.\n'
      ),
      process.version,
      packageJson.engines.node
    );
  }
}
```
