Grunt meteor
---
```
        exec: {
            'meteor-init': {
                command: [
                    // Make sure Meteor is installed, per https://meteor.com/install.
                    // The curl'ed script is safe; takes 2 minutes to read source & check.
                    'type meteor >/dev/null 2>&1 || { curl https://install.meteor.com/ | sh; }',
                    // Meteor expects package.js to be in the root directory of
                    // the checkout, but we already have a package.js for Dojo
                    'mv package.js package.dojo && cp meteor/package.js .'
                ].join(';')
            },
            'meteor-cleanup': {
                // remove build files and restore Dojo's package.js
                command: 'rm -rf ".build.*" versions.json; mv package.dojo package.js'
            },
            'meteor-test': {
                command: 'spacejam --mongo-url mongodb:// test-packages ./'
            },
            'meteor-publish': {
                command: 'meteor publish'
            }
        }
```
