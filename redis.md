sorted set or queue 
---
```ruby
    def atomic_push(conn, payloads)
      if payloads.first['at']
        conn.zadd('schedule'.freeze, payloads.map do |hash|
          at = hash.delete('at'.freeze).to_s
          [at, Sidekiq.dump_json(hash)]
        end)
      else
        q = payloads.first['queue']
        now = Time.now.to_f
        to_push = payloads.map do |entry|
          entry['enqueued_at'.freeze] = now
          Sidekiq.dump_json(entry)
        end
        conn.sadd('queues'.freeze, q)
        conn.lpush("queue:#{q}", to_push)
      end
    end
```
check lock
---
```node
    api.cache.checkLock = function(key, retry, next, startTime){
      if(startTime === null){ startTime = new Date().getTime(); }

      api.redis.client.get(api.cache.lockPrefix + key, function(err, lockedBy){
        if(err){
          next(err, false);
        }else if(lockedBy === api.cache.lockName || lockedBy === null){
          next(null, true);
        }else{
          var delta = new Date().getTime() - startTime;
          if(retry === null || retry === false || delta > retry){
            next(null, false);
          }else{
            setTimeout(function(){
              api.cache.checkLock(key, retry, next, startTime);
            }, api.cache.lockRetry);
          }
        }
      });
    };

    next();
  }
 ```
