How to write complex migrations in Rails
---
- Use the full power of SQL
```ruby
class AddFollowersCountToPosts < ActiveRecord::Migration

  def up
    add_column :posts, :followers_count, :integer
    update "UPDATE posts SET followers_count = (SELECT COUNT(*) FROM followers WHERE followers.post_id = posts.id)"
  end
  
  def down
    # not implemented
  end

end
```

```ruby
class MoveCategoriesFromProjectToActivity < ActiveRecord::Migration

  def up
    add_column :activities, :category, :string
    update <<-SQL
      UPDATE activities
      LEFT JOIN projects ON activities.project_id = projects.id
      SET activities.category = projects.category
    SQL
    remove_column :projects, :category
  end

  def down
    # not implemented
  end

end
```
* Embed models into your migrations
```ruby
class AddCurrentToVendor < ActiveRecord::Migration

  class Vendor < ActiveRecord::Base
  end

  class Article < ActiveRecord::Base
    has_many :vendors, :class_name => 'AddCurrentToVendor::Vendor', :order => 'created_at'
  end

  def up
    add_column :vendors, :current, :boolean
    Vendor.reset_column_information
    Vendor.update_all(:current => false)
    Article.all.each do |article|
      first_vendor = article.vendors.first
      if first_vendor
        first_vendor.update_attributes!(:current => true)
      end
    end
  end

  def down
    remove_column :vendors, :current
  end
end
```
* Use helpers methods from the database adapter
```ruby
class AddCurrentToVendor < ActiveRecord::Migration

  def up
    articles = select_all('SELECT * FROM articles')
    articles.each do |article|
      first_vendor = select_one("SELECT * FROM vendors WHERE article_id = #{article['id']}")
      if first_vendor
        update("UPDATE vendors SET current=#{quoted_true} WHERE id=#{first_vendor['id']}")
      end
    end
  end

  def down
    remove_column :vendors, :current
  end
  
end
```
* How do I check the Database type in a Rails Migration?
```ruby
ActiveRecord::Base.connection.instance_of? 
  ActiveRecord::ConnectionAdapters::MysqlAdapter 
# Even more shorted form
ActiveRecord::Base.connection.adapter_name == 'MySQL'

# There is an adapter_name in AbstractAdapter
adapter_type = connection.adapter_name.downcase.to_sym
case adapter_type
when :mysql
  # do the MySQL part
when :sqlite
  # do the SQLite3 part
when :postgresql
  # etc.
else
  raise NotImplementedError, "Unknown adapter type '#{adapter_type}'"
end
```
[The rails initialization process](http://guides.rubyonrails.org/v3.2.9/initialization.html)
---
1. bin/rails
2. config/boot.rb
3. rails/commands.rb
4. actionpack/lib/action_dispatch.rb
5. rails/commands/server.rb
6. Rack: lib/rack/server.rb
7. config/application
8. Rails::Server#start
9. config/environment.rb
10. config/application.rb
11. railties/lib/rails/all.rb
12 .Back to config/environment.rb
13. railties/lib/rails/application.rb
14. Rack: lib/rack/server.rb

[Extend rail with railties](https://blog.engineyard.com/2010/extending-rails-3-with-railties)
---
Rails Constant Autoloading
---
In contrast with Ruby’s autoload, which requires the location of each autoloaded constant to be specified in advance, Rails follows a simple convention that maps constant names to filenames. Nesting corresponds to directories, and constant names are underscored:
```ruby
MyModule::SomeClass # => my_module/some_class.rb
```
For a given constant, this inferred filename is then searched for within a number of autoload paths, as determined by the autoload_paths configuration option. By default, Rails searches in all immediate subdirectories of the `app/` directory.

ActiveSupport helper method
---
```ruby
        ActiveSupport.on_load(:action_controller) do
          if respond_to?(:helper_method)
            helper_method "current_#{mapping}", "#{mapping}_signed_in?", "#{mapping}_session"
          end
        end
```
load_rails
---
```ruby
  def load_rails
    require File.expand_path(File.dirname(__FILE__) + "/../config/environment")
  end
```
