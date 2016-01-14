### [How Passenger + Apache autodetects applications](https://www.phusionpassenger.com/library/indepth/ruby/app_autodetection/apache/)
---
1. First, it extracts the directory name from the virtual host root, like this: dirname(DocumentRoot). For example, if you set DocumentRoot /webapps/foo/public , then Passenger extracts the /webapps/foo portion of it. We call this the "application root".
2. Passenger checks whether config.ru exists in the application root directory
3. Passenger does not resolve any symlinks in the DocumentRoot path by default since version 2.2.0. If you need the old symlink-resolving behavior for whatever reason, then you can turn on PassengerResolveSymlinksInDocumentRoot. Another way to solve this situation is to explicitly tell Passenger what the correct application root is through the PassengerAppRoot option.
4. If Passenger does not detect an application inside the application root, then Passenger will let Apache serve the virtual host root as if Passenger is not activated.

**Overwrite autodetection**<br>
1. PassengerAppRoot – Tells Passenger where your application root is.
2. PassengerAppType – Tells Passenger what kind of application it is.
3. PassengerStartupFile – Tells Passenger where your application's entry point file is.

### Spawn methods explained
---
At its core, [Passenger is an HTTP proxy and process manager](https://www.phusionpassenger.com/library/indepth/ruby/spawn_methods/)

### Smart spawning hooks
---
If you want any code to be executed before the preloader has forked any child processes, then call that code in config.ru (or from any code called while loading config.ru, such as config/application.rb).

If you want any code to be executed after the preloader has forked a child process, and you want that code to be run in the context of the child process, then use the :starting_worker_process hook that Passenger provides. Put the following code in config.ru (or from any code called while loading config.ru, such as config/application.rb):
```rb
if defined?(PhusionPassenger)
  PhusionPassenger.on_event(:starting_worker_process) do |forked|
    if forked
      # We're in smart spawning mode.
      ... your code here ...
    else
      # We're in direct spawning mode. We don't need to do anything.
    end
  end
end
```
### Relevant Pages in the Passenger Docs
---
[The Apache version of the Passenger user’s guide](http://www.modrails.com/documentation/Users%20guide%20Apache.html) covers the Passenger-specific configuration directives we use below in much greater detail.

### Deploying a Ruby app on an AWS production server
---
This page describes [the deployment of a Ruby app](https://www.phusionpassenger.com/library/walkthroughs/deploy/ruby/aws/apache/oss/osx/deploy_app.html), assuming that Passenger was installed through the following operating system configuration: Mac OS X. 

### create a user for the app
```shell
$ sudo adduser myappuser
$ sudo mkdir -p ~myappuser/.ssh
$ sudo sh -c "cat $HOME/.ssh/authorized_keys >> ~myappuser/.ssh/authorized_keys"
$ sudo chown -R myappuser: ~myappuser/.ssh
$ sudo chmod 700 ~myappuser/.ssh
$ sudo sh -c "chmod 600 ~myappuser/.ssh/*"
$ sudo mkdir -p /var/www/myapp
$ sudo chown myappuser: /var/www/myapp
$ cd /var/www/myapp
$ sudo -u myappuser -H git clone git://github.com/username/myapp.git code
```
### Login as the app's user
```shell
$ sudo -u myappuser -H bash -l
$ rvm use ruby-2.2.3
```
### Install dependencies
```shell
$ cd /var/www/myapp/code
$ bundle install --deployment --without development test
```

### compile assets and run the database migration
```shell
$ bundle exec rake assets:precompile db:migrate RAILS_ENV=production
```
