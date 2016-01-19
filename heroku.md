[Getting Started with Rails 4.x on Heroku](https://devcenter.heroku.com/articles/getting-started-with-rails4)
---
```ruby
gem install rails --no-ri --no-rdoc
rails new myapp --database=postgresql
cd myapp
```
Edit your Gemfile and change this line:
```
gem 'sqlite3'
```
To
```
gem 'pg'
```
Now do
```
bundle install
```
In addition to using the pg gem, you’ll also need to ensure the config/database.yml is using the postgresql adapter.
The development section of your **config/database.yml** file should look something like this:
```
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
```
heroku create
# You can verify that the remote was added to your project by running
git config --list | grep heroku
```
