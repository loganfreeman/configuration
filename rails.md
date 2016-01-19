How to write complex migrations in Rails
---
- Use the full power of SQL
```sql
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

```sql
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
