Usage
---
To send a one-time password via text message you initialize a new Auth0LockPasswordless object and invoke the sms method.
```js
var clientID = "YOUR_AUTH0_APP_CLIENTID";
var domain = "YOUR_DOMAIN_AT.auth0.com";
document.getElementById("loginButton").onclick = function(e) {
  var lock = new Auth0LockPasswordless(clientID, domain);
  lock.sms(function(error, profile, id_token) {
    // This will be invoked when the user enters the one-time password he or she
    // received via text message. Here we just welcome the user, but usually you
    // want save the profile and id_token, and handle errors.
    if (!error) {
      alert("Hi " + profile.name);
    }
  });
};
```
