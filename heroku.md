[Getting Started with Rails 4.x on Heroku](https://devcenter.heroku.com/articles/getting-started-with-rails4)
---
```shell
gem install rails --no-ri --no-rdoc
rails new myapp --database=postgresql
cd myapp
```
Edit your Gemfile and change this line:
```ruby
gem 'sqlite3'
```
To
```ruby
gem 'pg'
```
Now do
```shell
bundle install
```
In addition to using the pg gem, you’ll also need to ensure the config/database.yml is using the postgresql adapter.
The development section of your **config/database.yml** file should look something like this:
```yml
# PostgreSQL. Versions 8.2 and up are supported.
#
# Install the pg driver:
#   gem install pg
# On OS X with Homebrew:
#   gem install pg -- --with-pg-config=/usr/local/bin/pg_config
# On OS X with MacPorts:
#   gem install pg -- --with-pg-config=/opt/local/lib/postgresql84/bin/pg_config
# On Windows:
#   gem install pg
#       Choose the win32 build.
#       Install PostgreSQL and put its /bin directory on your path.
#
# Configure Using Gemfile
# gem 'pg'
#
default: 
  adapter: postgresql
  encoding: unicode
  # For details on connection pooling, see rails configuration guide
  # http://guides.rubyonrails.org/configuring.html#database-pooling
  pool: 5
```
 To enable features such as static asset serving and logging on Heroku please add rails_12factor gem to your Gemfile
 ```
 gem 'rails_12factor', group: :production
 ```
Make sure you are in the directory that contains your Rails app, then create an app on Heroku:
```shell
heroku create
# You can verify that the remote was added to your project by running
git config --list | grep heroku
git push heroku master
# If you are using the database in your application you need to manually migrate the database by running:
heroku run rake db:migrate # Any commands after the heroku run will be executed on a Heroku dyno
# You can obtain an interactive shell session by running
heroku run bash
# Let’s ensure we have one dyno running the web process type:
heroku ps:scale web=1
heroku ps # The heroku ps command lists the running dynos of your application
# We can now visit the app in our browser with
heroku open
# You can view information about your running app using one of the logging commands:
heroku logs --tail
```
By default, your app’s web process runs rails server, which uses Webrick.
Regardless of the webserver you choose, production apps should always specify the webserver explicitly in the Procfile.
First, add Puma to your application Gemfile:
```ruby
gem 'puma'
```
