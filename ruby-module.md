require all
---
```ruby
def require_all(path)
  glob = File.join(File.dirname(__FILE__), path, '*.rb')
  Dir[glob].each do |f|
    require f
  end
end
```

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
instance_eval
---
```ruby
      instance_eval do
        # rubocop:disable Lint/RescueException
        begin
          # rubocop:disable Eval
          eval(contents, nil, path.to_s)
          # rubocop:enable Eval
        rescue Exception => e
          message = "Invalid `#{path.basename}` file: #{e.message}"
          raise DSLError.new(message, path, e.backtrace, contents)
        end
        # rubocop:enable Lint/RescueException
      end
```
gem path 
---
```ruby
  def self.gem_path
    gem_name = "danger"
    unless Gem::Specification.find_all_by_name(gem_name).any?
      raise "Couldn't find gem directory for 'danger'"
    end
    return Gem::Specification.find_by_name(gem_name).gem_dir
  end
```
For use/testing when no gem is installed
---
```ruby
$LOAD_PATH.unshift File.dirname(__FILE__) # For use/testing when no gem is installed
```
platform
---
```ruby
    def platform_path
      [:linux, :darwin, :cygwin, :windows].each do |sys|
        return self.send("#{sys}_path") if Vagrant::Util::Platform.respond_to?("#{sys}?") && Vagrant::Util::Platform.send("#{sys}?")
      end
      nil
    end
```
