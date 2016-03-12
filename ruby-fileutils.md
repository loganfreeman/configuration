walk
---
```ruby
def walk(start, &block)
  Dir.foreach(start) do |x|
    path = File.join(start, x)
    if x == "." or x == ".."
      next
    elsif File.directory?(path)
      walk(path, &block)
    else
      yield x
    end
  end
end
```
