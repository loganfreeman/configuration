Difference between exec, system and %x() or Backticks
---
The `system` method calls a system program. You have to provide the command as a string argument to this method.

The invoked program will use the current `STDIN`, `STDOUT` and `STDERR` objects of your Ruby program. In fact, the actual return value is either `true`, `false` or `nil`. In the example the date was printed through the IO object of STDIN. The method will return `true` if the process exited with a zero status, `false` if the process exited with a non-zero status and `nil` if the execution failed.

Another side effect is that the global variable `$?` is set to a `Process::Status` object. This object will contain information about the call itself, including the process identifier (PID) of the invoked process and the exit status.
```ruby
system("date")
system "rm test-rails-app"
system "ln -s #{test_app_path} test-rails-app"
```
`Backticks` (``) call a system program and return its output. The global variable `$?` is set through the backticks, too. With backticks you can also make use string interpolation.

Using `%x` is an alternative to the backticks style. It will return the output, too.

By using `Kernel#exec` the current process (your Ruby script) is replaced with the process invoked through exec. The method can take a string as argument. In this case the string will be subject to shell expansion. When using more than one argument, then the first one is used to execute a program and the following are provided as arguments to the program to be invoked.

`STDOUT`, `STDIN`
---
```ruby
STDOUT.print "Add #{bower_names.size} components? (y/n) "
input = STDIN.gets.strip
```
trap
---
```ruby
  def run(*args)
    load_environment!

    if File.exist?(procfile)
      engine.load_procfile(procfile)
    end

    pid = fork do
      begin
        engine.env.each { |k,v| ENV[k] = v }
        if args.size == 1 && process = engine.process(args.first)
          process.exec(:env => engine.env)
        else
          exec args.shelljoin
        end
      rescue Errno::EACCES
        error "not executable: #{args.first}"
      rescue Errno::ENOENT
        error "command not found: #{args.first}"
      end
    end
    trap("INT") do
      Process.kill(:INT, pid)
    end
    Process.wait(pid)
    exit $?.exitstatus
  rescue Interrupt
  end
  ```
setup deferred signal handlers
---
```ruby
  HANDLED_SIGNALS = [ :TERM, :INT, :HUP ]
  # Set up deferred signal handlers
  #
  def register_signal_handlers
    HANDLED_SIGNALS.each do |sig|
      if ::Signal.list.include? sig.to_s
        trap(sig) { Thread.main[:signal_queue] << sig ; notice_signal }
      end
    end
  end

  # Unregister deferred signal handlers
  #
  def restore_default_signal_handlers
    HANDLED_SIGNALS.each do |sig|
      trap(sig, :DEFAULT) if ::Signal.list.include? sig.to_s
    end
  end
  ```
spawn a process
---
`Process.spawn` doesn’t wait for end of the command. The parent process should use Process.wait to collect the termination status of its child
```ruby
  def run(options={})
    env    = @options[:env].merge(options[:env] || {})
    output = options[:output] || $stdout
    runner = "#{Foreman.runner}".shellescape
    
    Dir.chdir(cwd) do
      Process.spawn env, expanded_command(env), :out => output, :err => output
    end
  end
```
[Create Enumerator](http://blog.arkency.com/2014/01/ruby-to-enum-for-enumerator/)
---
There are 3 ways to create your own Enumerator:

* `Kernel#to_enum`
* `Kernel#enum_for`
* `Enumerator.new`

**Enumerator.new**

*Iteration is defined by the given block, in which a “yielder” object, given as block parameter, can be used to yield a value:*
```ruby
fib = Enumerator.new do |y|
  a = b = 1
  loop do
    y << a
    a, b = b, a + b
  end
end

fib.take(10) # => [1, 1, 2, 3, 5, 8, 13, 21, 34, 55]
```
*The optional parameter can be used to specify how to calculate the size in a lazy fashion. It can either be a value or a callable object.*
```ruby
polish_postal_codes = Enumerator.new(100_000) do |y|
  100_000.times do |number|
    code    = sprintf("%05d", number)
    code[1] = code[1] + "-"
    y.yield(code)
  end
end

polish_postal_codes.size    # => 100000 
                            # returned without computing
                            # all elements

polish_postal_codes.take(3) # => ["00-000", "00-001", "00-002"]
```
**Method: Object#to_enum**

*Creates a new Enumerator which will enumerate by calling method on `obj`, passing args if any.*
```ruby
    def find(*args)
      unless environment
        raise Error, "manifest requires environment for compilation"
      end

      return to_enum(__method__, *args) unless block_given?

      environment = self.environment.cached
      args.flatten.each do |path|
        environment.find_all_linked_assets(path) do |asset|
          yield asset
        end
      end

      nil
    end
```
anonymous class
---
```ruby
# returns an anonymous wispered class
def publisher_class
  Class.new { include Wisper::Publisher }
end
```
instance_eval and send
---
```ruby
publisher.instance_eval { broadcast(:failure) }
publisher.send(:broadcast, :it_happened)
```
This is another form of instance_eval. instance_eval evals the block in the context of the metaclass, but resolve any method in the context of the instance itself.
```ruby
    class << (@@properties = [])
      def names
        map(&:first)
      end

      def value_for(property_name)
        if property = assoc(property_name)
          property.last
        end
      end
    end
```
instance_eval on self
```ruby
    class << self #:nodoc:
      def property(name, value = nil)
        value ||= yield
        properties << [name, value] if value
      rescue Exception
      end

      def to_s
        column_width = properties.names.map(&:length).max
        info = properties.map do |name, value|
          value = value.join(", ") if value.is_a?(Array)
          "%-#{column_width}s   %s" % [name, value]
        end
        info.unshift "About your application's environment"
        info * "\n"
      end

      alias inspect to_s

      def to_html
        '<table>'.tap do |table|
          properties.each do |(name, value)|
            table << %(<tr><td class="name">#{CGI.escapeHTML(name.to_s)}</td>)
            formatted_value = if value.kind_of?(Array)
                  "<ul>" + value.map { |v| "<li>#{CGI.escapeHTML(v.to_s)}</li>" }.join + "</ul>"
                else
                  CGI.escapeHTML(value.to_s)
                end
            table << %(<td class="value">#{formatted_value}</td></tr>)
          end
          table << '</table>'
        end
      end
    end

    # The Rails version.
    property 'Rails version' do
      Rails.version.to_s
    end
```
load hash into local context
---
```ruby
require 'erb'
require 'ostruct'
require 'yaml'

opts = OpenStruct.new({
  first_name: "Martin",
  last_name: "Brennan"
})

# alternatively from yaml file
config = YAML::load(File.read('config.yml'))[rails_env]
opts = OpenStruct.new(config)

# load the erb template. 'Hello <%= first_name %> <%= last_name %>.'
template = File.read('template.erb')

# render the template
puts ERB.new(template).result(opts.instance_eval {binding})
```
Struct
---
```ruby
NewsletterJob = Struct.new(:text, :emails) do
  def perform
    emails.each { |e| NewsletterMailer.deliver_text_to_email(text, e) }
  end
end
```
Ruby Constant Lookup
---
When you refer to a constant in a given lexical scope, that constant is searched for in:

1. Each entry in `Module.nesting`
2. Each entry in `Module.nesting.first.ancestors`
3. Each entry in `Object.ancestors` if `Module.nesting.first` is `nil` or a module.

processing indicator
---
```ruby
    def show_processing_indicator
      $indicator = Thread.new do
        loop do
          print "."
          sleep 0.2
        end
      end.run
    end

    def hide_processing_indicator
      $indicator.kill
      puts ""
    end
```
benchmark of `class_eval` and `define_method`
---
```ruby
require 'benchmark'

GC.disable

N = 100000
Benchmark.bm(13) do |x|
  x.report("define_method") {
    class Foo
      N.times { |i| define_method("foo_#{i}") { } }
    end
  }

  x.report("class_eval") {
    class Bar
      N.times { |i|
        class_eval <<-eoruby, __FILE__, __LINE__ + 1
          def bar_#{i}
          end
        eoruby
      }
    end
  }
end
```
Measuring Instructions
---
```ruby
require 'objspace'

N = (ENV['N'] || 100_000).to_i

class Foo
  N.times do |i|
    if ENV['EVAL']
      class_eval "def bar_#{i}; end"
    else
      define_method("bar_#{i}") { }
    end
  end
end

GC.start

p ObjectSpace.memsize_of_all(RubyVM::InstructionSequence)
```
