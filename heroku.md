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
