* Spawn methods explained
> \s[At its core, Passenger is an HTTP proxy and process manager](https://www.phusionpassenger.com/library/indepth/ruby/spawn_methods/)
* Relevant Pages in the Passenger Docs
> [The Apache version of the Passenger user’s guide](http://www.modrails.com/documentation/Users%20guide%20Apache.html) covers the Passenger-specific configuration directives we use below in much greater detail.

* Deploying a Ruby app on an AWS production server
> This page describes [the deployment of a Ruby app](https://www.phusionpassenger.com/library/walkthroughs/deploy/ruby/aws/apache/oss/osx/deploy_app.html), assuming that Passenger was installed through the following operating system configuration: Mac OS X. 

* create a user for the app
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
* Login as the app's user
```shell
$ sudo -u myappuser -H bash -l
$ rvm use ruby-2.2.3
```
* Install dependencies
```shell
$ cd /var/www/myapp/code
$ bundle install --deployment --without development test
```

* compile assets and run the database migration
```shell
$ bundle exec rake assets:precompile db:migrate RAILS_ENV=production
```
