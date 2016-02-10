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
process directive
---
```ruby
      def process_directives(directives)
        directives.each do |line_number, name, *args|
          begin
            send("process_#{name}_directive", *args)
          rescue Exception => e
            e.set_backtrace(["#{@filename}:#{line_number}"] + e.backtrace)
            raise e
          end
        end
      end
```
