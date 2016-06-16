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
```
