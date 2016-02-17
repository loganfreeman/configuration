decode credentials
---
```ruby
      # Helper to decode credentials from HTTP.
      def decode_credentials
        return [] unless request.authorization && request.authorization =~ /^Basic (.*)/mi
        Base64.decode64($1).split(/:/, 2)
      end
```
