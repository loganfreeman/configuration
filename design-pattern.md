singleton
---
```ruby
    def self.instance
      # Default to C omment styles
      @instance ||= new(comments: ["//", ["/*", "*/"]])
    end
```
