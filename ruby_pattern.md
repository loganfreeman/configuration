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
set up gems listed in the Gemfile
---
```ruby
# Set up gems listed in the Gemfile.
ENV['BUNDLE_GEMFILE'] ||= File.expand_path('../../Gemfile', __FILE__)

require 'bundler/setup' if File.exists?(ENV['BUNDLE_GEMFILE'])
```
decorator pattern
---
```ruby
Blorgh::Article.class_eval do
  def time_since_created
    Time.current - created_at
  end
end
```
inheritance
---
```ruby
  module Inheritance

      def self.included(base)
        base.extend ClassMethods
      end

      module ClassMethods

        def settings_inherited_by(heir)
          (@setting_heirs ||= []) << heir
          heir.send :include, ActiveAdmin::Settings
        end

        def inheritable_setting(name, default)
          setting name, default
          @setting_heirs.each{ |c| c.setting name, default }
        end

        def deprecated_inheritable_setting(name, default)
          deprecated_setting name, default
          @setting_heirs.each{ |c| c.deprecated_setting name, default }
        end

      end
    end

  end
```
add method dynamically
---
```ruby
    AbstractController::Callbacks::ClassMethods.public_instance_methods.
      select { |m| m.match(/(filter|action)/) }.each do |name|
      define_method name do |*args, &block|
        controllers_for_filters.each do |controller|
          controller.public_send name, *args, &block
        end
      end
    end
```
loader
---
```ruby
module ActiveAdmin
  module Views
    # Loads all the classes in views/*.rb
    Dir[File.expand_path('../views', __FILE__) + "/**/*.rb"].sort.each{ |f| require f }
  end
end
```
ActiveSupport::Concern
---
```ruby
module TagLib
  extend ActiveSupport::Concern

  module ClassMethods
    def find_by_tags()
      # ...
    end
  end
 
  module InstanceMethods
    def tags()
      # ...
    end
  end 
end

class ActiveRecord::Base
  include TagLib
end
```
instance_exec
---
This method is different from the others in that it calls `instance_exec` on the reciever, passing it the proc. This evaluates the proc in the context of the reciever, thus changing what `self` means inside the proc.
```ruby
def render_in_context(context, obj, *args)
  context ||= self # default to `self`
  case obj
  when Proc
    context.instance_exec *args, &obj
  when Symbol
    context.public_send obj, *args
  else
    obj
  end
end
```
lazy eval
---
```ruby
    # Creates a namespace for the given name
    #
    # Yields the namespace if a block is given
    #
    # @return [Namespace] the new or existing namespace
    def namespace(name)
      name ||= :root

      namespace = namespaces[name] ||= begin
        namespace = Namespace.new(self, name)
        ActiveSupport::Notifications.publish ActiveAdmin::Namespace::RegisterEvent, namespace
        namespace
      end

      yield(namespace) if block_given?

      namespace
    end
```
`extended` vs `included`
---
```ruby
      def self.included(klass)
        klass.class_attribute(*VIEW_ACCESSORS)

        klass.class_eval do
          remove_method :assets_environment
          def assets_environment
            if instance_variable_defined?(:@assets_environment)
              @assets_environment = @assets_environment.cached
            elsif env = self.class.assets_environment
              @assets_environment = env.cached
            else
              nil
            end
          end
        end
      end

      def self.extended(obj)
        obj.class_eval do
          attr_accessor(*VIEW_ACCESSORS)

          remove_method :assets_environment
          def assets_environment
            if env = @assets_environment
              @assets_environment = env.cached
            else
              nil
            end
          end
        end
      end
  ```
detect
---
```ruby
        # Try each asset resolver and return the first non-nil result.
        def resolve_asset
          asset_resolver_strategies.detect do |resolver|
            if result = yield(resolver)
              break result
            end
          end
        end
```
get wrapper
---
```ruby
      def get_cache_wrapper(cache)
        if cache.is_a?(Cache)
          cache

        # `Cache#get(key)` for Memcache
        elsif cache.respond_to?(:get)
          GetWrapper.new(cache)

        # `Cache#[key]` so `Hash` can be used
        elsif cache.respond_to?(:[])
          HashWrapper.new(cache)

        # `Cache#read(key)` for `ActiveSupport::Cache` support
        elsif cache.respond_to?(:read)
          ReadWriteWrapper.new(cache)

        else
          cache = Sprockets::Cache::NullStore.new
          GetWrapper.new(cache)
        end
      end
```
Callback
---
```ruby
  class Callback
    def initialize
      @before = []
      @after = []

      # Identity proc. Avoids special cases when there is no existing around chain.
      @around = lambda { |*args, &block| block.call(*args) }
    end

    def execute(*args, &block)
      @before.each { |c| c.call(*args) }
      result = @around.call(*args, &block)
      @after.each { |c| c.call(*args) }
      result
    end

    def add(type, &callback)
      case type
      when :before
        @before << callback
      when :after
        @after << callback
      when :around
        chain = @around # use a local variable so that the current chain is closed over in the following lambda
        @around = lambda { |*a, &block| chain.call(*a) { |*b| callback.call(*b, &block) } }
      else
        raise InvalidCallback, "Invalid callback type: #{type}"
      end
    end
  end
```
Lifecycle
---
```ruby
  class Lifecycle
    EVENTS = {
      :enqueue    => [:job],
      :execute    => [:worker],
      :loop       => [:worker],
      :perform    => [:worker, :job],
      :error      => [:worker, :job],
      :failure    => [:worker, :job],
      :invoke_job => [:job]
    }

    def initialize
      @callbacks = EVENTS.keys.each_with_object({}) do |e, hash|
        hash[e] = Callback.new
      end
    end

    def before(event, &block)
      add(:before, event, &block)
    end

    def after(event, &block)
      add(:after, event, &block)
    end

    def around(event, &block)
      add(:around, event, &block)
    end

    def run_callbacks(event, *args, &block)
      missing_callback(event) unless @callbacks.key?(event)

      unless EVENTS[event].size == args.size
        raise ArgumentError, "Callback #{event} expects #{EVENTS[event].size} parameter(s): #{EVENTS[event].join(', ')}"
      end

      @callbacks[event].execute(*args, &block)
    end

  private

    def add(type, event, &block)
      missing_callback(event) unless @callbacks.key?(event)
      @callbacks[event].add(type, &block)
    end

    def missing_callback(event)
      raise InvalidCallback, "Unknown callback event: #{event}"
    end
  end
```
defaults
---
```ruby
    def self.default_logger
      logger = Logger.new(STDOUT)
      logger.progname = 'omniauth'
      logger
    end

    def self.defaults
      @defaults ||= {
        :camelizations => {},
        :path_prefix => '/auth',
        :on_failure => OmniAuth::FailureEndpoint,
        :failure_raise_out_environments => ['development'],
        :before_request_phase   => nil,
        :before_callback_phase  => nil,
        :before_options_phase   => nil,
        :form_css => Form::DEFAULT_CSS,
        :test_mode => false,
        :logger => default_logger,
        :allowed_request_methods => [:get, :post],
        :mock_auth => {:default => AuthHash.new('provider' => 'default', 'uid' => '1234', 'info' => {'name' => 'Example User'})}
      }
    end

    def initialize
      self.class.defaults.each_pair { |k, v| send("#{k}=", v) }
    end
```
hash accessor
---
```ruby
    def self.hash_accessor(*names) #:nodoc:
      names.each do |name|
        class_eval <<-METHOD, __FILE__, __LINE__ + 1
          def #{name}
            self[:#{name}]
          end
          def #{name}=(value)
            self[:#{name}] = value
          end
        METHOD
      end
    end

    hash_accessor :failure_app, :default_scope, :intercept_401
```
dynamically add method on an instance
---
```ruby
def define_singleton_method_by_proc(obj, name, block)
  metaclass = class << obj; self; end
  metaclass.send(:define_method, name, block)
end
p = proc { "foobar!" }
define_singleton_method_by_proc(y, :bar, p)
```
tranform pattern
---
```ruby
module React
  module JSX
    DEFAULT_TRANSFORMER = BabelTransformer
    mattr_accessor :transform_options, :transformer_class, :transformer

    # You can assign `config.react.jsx_transformer_class = `
    # to provide your own transformer. It must implement:
    # - #initialize(options)
    # - #transform(code) => new code
    self.transformer_class = DEFAULT_TRANSFORMER

    def self.transform(code)
      self.transformer ||= transformer_class.new(transform_options)
      self.transformer.transform(code)
    end
  end
end
```
