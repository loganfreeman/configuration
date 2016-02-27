import all sources
---
```ruby
# Import all the Sources (CI, Request and SCM)
Dir[File.expand_path('danger/*source/*.rb', File.dirname(__FILE__))].each do |file|
  require file
end
```
method_missing
---
```ruby
      # When an undefined method is called, we check to see if it's something
      # that either the `scm` or the `request_source` can handle.
      # This opens us up to letting those object extend themselves naturally.
      def method_missing(method_sym, *_arguments, &_block)
        unless AvailableValues.all.include?(method_sym)
          raise "Unknown method '#{method_sym}', please check out the documentation for available variables".red
        end

        if AvailableValues.scm.include?(method_sym)
          # SCM Source
          return env.scm.send(method_sym)
        end

        if AvailableValues.request_source.include?(method_sym)
          # Request Source
          return env.request_source.send(method_sym)
        end
      end
```
