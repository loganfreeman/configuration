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
