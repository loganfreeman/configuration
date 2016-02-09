Difference between exec, system and %x() or Backticks
---
The `system` method calls a system program. You have to provide the command as a string argument to this method.

The invoked program will use the current `STDIN`, `STDOUT` and `STDERR` objects of your Ruby program. In fact, the actual return value is either true, false or nil. In the example the date was printed through the IO object of STDIN. The method will return true if the process exited with a zero status, false if the process exited with a non-zero status and nil if the execution failed.

Another side effect is that the global variable $? is set to a Process::Status object. This object will contain information about the call itself, including the process identifier (PID) of the invoked process and the exit status.
```ruby
system("date")
system "rm test-rails-app"
system "ln -s #{test_app_path} test-rails-app"
```

