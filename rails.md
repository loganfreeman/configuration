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
