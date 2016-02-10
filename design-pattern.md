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
enumerator
---
```ruby
    # Public: Find all assets matching pattern set in environment.
    #
    # Returns Enumerator of Assets.
    def find(*args)
      unless environment
        raise Error, "manifest requires environment for compilation"
      end

      environment = self.environment.cached
      args.flatten.each do |path|
        environment.find_all_linked_assets(path) do |asset|
          yield asset
        end
      end

      nil
    end
```
