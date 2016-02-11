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
