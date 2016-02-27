```js
var pm2 = require('pm2');

var instances = process.env.WEB_CONCURRENCY || -1; // Set by Heroku or -1 to scale to max cpu core -1
var maxMemory = process.env.WEB_MEMORY || 512;    // " " "
var transporter = nodemailer.createTransport(transportOptions);
var compiled = _.template(
  'An error has occurred on server ' +
  '<% name %>\n' +
  'Stack Trace:\n\n\n<%= stack %>\n\n\n' +
  'Context:\n\n<%= text %>'
);

pm2.connect(function() {
  pm2.start({
    script    : 'app.js',
    name      : 'production-app',     // ----> THESE ATTRIBUTES ARE OPTIONAL:
    exec_mode : 'cluster',            // ----> https://github.com/Unitech/PM2/blob/master/ADVANCED_README.md#schema
    instances : instances,
    max_memory_restart : maxMemory + 'M',   // Auto restart if process taking more than XXmo
    env: {                            // If needed declare some environment variables
      "NODE_ENV": "production",
      "AWESOME_SERVICE_API_TOKEN": "xxx"
    },
  }, function(err) {
    if (err) return console.error('Error while launching applications', err.stack || err);
    console.log('PM2 and application has been succesfully started');

    pm2.launchBus(function(err, bus) {
      if (err) {
        return console.error(err);
      }
      console.log('event bus connected');
  
      bus.on('process:exception', function(data) {
        var text;
        var stack;
        var name;
        try {
          data.date = moment(data.at || new Date())
            .tz('America/Los_Angeles')
            .format('MMMM Do YYYY, h:mm:ss a z');
  
          text = JSON.stringify(data, null, 2);
          stack = data.data.stack;
          name = data.process.name;
        } catch (e) {
          return e;
        }
  
        transporter.sendMail({
          to: mailReceiver,
          from: 'team@freecodecamp.com',
          subject: 'Server exception',
          text: compiled({ name: name, text: text, stack: stack })
        });
      });
    });

  });
});
```
