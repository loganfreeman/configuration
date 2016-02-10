two layer cache
---
```ruby
    def fetch(key)
      start = Time.now.to_f
      expanded_key = expand_key(key)
      value = @fetch_cache.get(expanded_key)
      if value.nil?
        value = @cache_wrapper.get(expanded_key)
        if value.nil?
          value = yield
          @cache_wrapper.set(expanded_key, value)
          @logger.debug do
            ms = "(#{((Time.now.to_f - start) * 1000).to_i}ms)"
            "Sprockets Cache miss #{peek_key(key)}  #{ms}"
          end
        end
        @fetch_cache.set(expanded_key, value)
      end
      value
    end
```
