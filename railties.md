rake tasks
---
```ruby
  class Railtie < Rails::Railtie
    rake_tasks do
      load "rails_erd/tasks.rake"
    end
  end
```
