Create a method to speed up future calls
---
```ruby
  def cache_method_call(name)
      class_eval <<-END, __FILE__, __LINE__
        def #{name}(*args, &blk)
          current.__send__(:#{name}, *args, &blk)
        end
      END
  end

  # Proxies methods to the current environment, creating a
  # method before dispatching to speed up future calls.
  def method_missing(name, *args, &blk)
    if current.respond_to?(name)
      cache_method_call name
      current.__send__ name, *args, &blk
    else
      super
    end
  end
```
Require all the files in a directory
```ruby
   %w(configuration utility alias list gemset rubies cleanup sets env tools info wrapper).each do |key|
      # assume rvm is in load path, this will require all files at rvm/environment/*
      require File.join("rvm", "environment", key)
    end
```
Safe require
---
```ruby
def require?(path)
  require path
rescue LoadError => e
  # HACK: ( because we should raise on syntax errors but
  # not if the file doesn't exist. TODO make robust!
  raise unless e.to_s.include? path
end
```
exec command
---
```ruby
if which "brew-#{cmd}"
  %w[CACHE CELLAR LIBRARY_PATH PREFIX REPOSITORY].each do |e|
    ENV["HOMEBREW_#{e}"] = Object.const_get("HOMEBREW_#{e}").to_s
  end
  exec "brew-#{cmd}", *ARGV
end
```
dynamic return value based on input
---
```ruby
        def sudo(*parameters, &block)
          options = parameters.last.is_a?(Hash) ? parameters.pop.dup : {}
          command = parameters.first
          user = options[:as] && "-u #{options.delete(:as)}"

          sudo_prompt_option = "-p '#{sudo_prompt}'" unless sudo_prompt.empty?
          sudo_command = [fetch(:sudo, "sudo"), sudo_prompt_option, user].compact.join(" ")

          if command
            command = sudo_command + " " + command
            run(command, options, &block)
          else
            return sudo_command
          end
        end
```
based on the first parameter, it either run the command or returns a string

define a method on self 
---
by opening eigenclass of self, passing a block as the method definition. This block is evaluated using `instance_eval`. 
```ruby
      def define_task(task)
        tasks[task.name] = task

        metaclass = class << self; self; end
        metaclass.send(:define_method, task.name) { execute_task(tasks[task.name]) }
      end
```
array splat
---
```ruby
  gateways = [*gateway].collect { |g| ServerDefinition.new(g) }
```

context switch
---
```ruby
  def local
    if block_given?
      begin
        saved, @local_mode = @local_mode, true
        yield
      ensure
        @local_mode = saved
      end
    else
      LocalProxy.new(self)
    end
  end
```
handle different versions 
---
```ruby
require "capistrano-resque/version"

if defined?(Capistrano::VERSION) && Gem::Version.new(Capistrano::VERSION).release >= Gem::Version.new("3.0.0")
  load File.expand_path("../capistrano-resque/tasks/capistrano-resque.rake", __FILE__)
else
  require "capistrano-resque/capistrano_integration"
end
```
