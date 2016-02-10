singleton
---
```ruby
    def self.instance
      # Default to C omment styles
      @instance ||= new(comments: ["//", ["/*", "*/"]])
    end

    def self.call(input)
      instance.call(input)
    end
```
