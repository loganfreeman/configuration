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
Change the command used to launch your web process by creating a file called Procfile, in Procfile write:
```shell
web: bundle exec puma -t 5:5 -p ${PORT:-3000} -e ${RACK_ENV:-development}
# However a config file is recommended 
web: bundle exec puma -C config/puma.rb
```
For a simple Rails application, we recommend the following basic configuration, the content of **config/puma.rb**:
```ruby
workers Integer(ENV['WEB_CONCURRENCY'] || 2)
threads_count = Integer(ENV['MAX_THREADS'] || 5)
threads threads_count, threads_count

preload_app!

rackup      DefaultRackup
port        ENV['PORT']     || 3000
environment ENV['RACK_ENV'] || 'development'

on_worker_boot do
  # Worker specific setup for Rails 4.1+
  # See: https://devcenter.heroku.com/articles/deploying-rails-applications-with-the-puma-web-server#on-worker-boot
  ActiveRecord::Base.establish_connection
end
```
Puma forks multiple OS processes within each dyno to allow a Rails app to support multiple concurrent requests. In Puma terminology these are referred to as worker processes. Worker processes are isolated from one another at the OS level, therefore not needing to be thread safe.

On MRI, there is a Global Interpreter Lock (GIL) that ensures only one thread can be run at any time. IO operations such as database calls, interacting with the file system, or making external http calls will not lock the GIL. Most Rails applications heavily use IO, so adding additional threads will allow Puma to process multiple threads, gaining you more throughput.

Puma can serve each request in a thread from an internal thread pool. This allows Puma to provide additional concurrency for your web application. Loosely speaking, workers consume more RAM and threads consume more CPU, and both provide more concurrency.

If your app is not thread safe, you will only be able to use workers. Set your min and max threads to 1:
```
heroku config:set MAX_THREADS=1
```

#### On Worker Boot
The on_worker_boot block is run after a worker is spawned, but before it begins to accept requests. This block is especially useful for connecting to different services as connections cannot be shared between multiple processes.

If you are using Rails 4.1+ you can use the database.yml to set your connection pool size and this is all you need to do:
```ruby
on_worker_boot do
  # Valid on Rails 4.1+ using the `config/database.yml` method of setting `pool` size
  ActiveRecord::Base.establish_connection
end
```
Otherwise you must be very specific with the reconnection code:
```ruby
on_worker_boot do
  # Valid on Rails up to 4.1 the initializer method of setting `pool` size
  ActiveSupport.on_load(:active_record) do
    config = ActiveRecord::Base.configurations[Rails.env] ||
                Rails.application.config.database_configuration[Rails.env]
    config['pool'] = ENV['MAX_THREADS'] || 5
    ActiveRecord::Base.establish_connection(config)
  end
end
```
You will need to re-connect to any datastore such as Postgres, Redis, or memcache. 
```ruby
on_worker_boot do
  # ...
  if defined?(Resque)
     Resque.redis = ENV["<redis-uri>"] || "redis://127.0.0.1:6379"
  end
end
```
#### Connection Pool
By default Rails (Active Record) will only create a connection when a new thread or process attempts to talk to the database through a SQL query. Active Record limits the total number of connections per application through a database setting pool; this is the maximum size of the connections your app can have to the database. The default maximum size of the database connection pool is 5. If you try to use more connections than are available, Active Record will block and wait for a connection from the pool.
