fast digest of nested JSON object
---
```ruby
    def digest(obj)
      digest = digest_class.new
      queue  = [obj]

      while queue.length > 0
        obj = queue.shift
        klass = obj.class

        if klass == String
          digest << obj
        elsif klass == Symbol
          digest << 'Symbol'
          digest << obj.to_s
        elsif klass == Fixnum
          digest << 'Fixnum'
          digest << obj.to_s
        elsif klass == Bignum
          digest << 'Bignum'
          digest << obj.to_s
        elsif klass == TrueClass
          digest << 'TrueClass'
        elsif klass == FalseClass
          digest << 'FalseClass'
        elsif klass == NilClass
          digest << 'NilClass'.freeze
        elsif klass == Array
          digest << 'Array'
          queue.concat(obj)
        elsif klass == Hash
          digest << 'Hash'
          queue.concat(obj.sort)
        elsif klass == Set
          digest << 'Set'
          queue.concat(obj.to_a)
        elsif klass == Encoding
          digest << 'Encoding'
          digest << obj.name
        else
          raise TypeError, "couldn't digest #{klass}"
        end
      end

      digest.digest
    end
```
