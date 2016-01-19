Ruby thread safety
---
This page ["How Do I Know Whether My Rails App Is Thread-safe or Not?"](https://bearmetal.eu/theden/how-do-i-know-whether-my-rails-app-is-thread-safe-or-not/) explains all the pitfalls of thread safety in rails

Nobody understands the GIL
---
MRI has something called a [global interpreter lock (GIL)](http://www.jstorimer.com/blogs/workingwithcode/8085491-nobody-understands-the-gil). It's a lock around the execution of Ruby code. This means that in a multi-threaded context, only one thread can execute Ruby code at any one time.

Non-native methods
---
methods implemented with Ruby code have no atomicity guarantees on MRI. Only methods implemented with native C code have this guarantee.

Race conditions
---
GIL doesn't prevent ract conditions
```
class Sheep
  def initialize
    @shorn = false
  end

  def shorn?
    @shorn
  end

  def shear!
    puts "shearing..."
    @shorn = true
  end
end

sheep = Sheep.new

5.times.map do
  Thread.new do
    unless sheep.shorn?
      sheep.shear!
    end
  end
end.each(&:join)
```
Ruby Strings - Creation and Basics
---
* Quoting Ruby Strings
```ruby
myString = "This is also my string.\nGet your own string"
```
* General Delimited Strings
Ruby allows you to define any character you want as a string delimiter simply by prefixing the desired character with a %. For example, we could use the ampersand to delimit our string:
```ruby
myString = %&This is my String&
```
* Ruby Here Documents
A Here Document (or heredoc as it is more commonly referred to) provides a mechanism for creating free format strings, preserving special characters such as new lines and tabs.

A here document is created by preceding the text with << followed by the delimiter string you wish to use to mark the end of the text. The following example uses the string "DOC" as the delimiter:
```ruby
myText = <<DOC
Please Detach and return this coupon with your payment.
Do not send cash or coins.

Please write your name and account number on the check and
make checks payable to:

        Acme Corporation

Thank you for your business.
DOC
```
