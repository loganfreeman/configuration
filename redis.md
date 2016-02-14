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
  
     api.cache.lock = function(key, expireTimeMS, next){
      if(typeof expireTimeMS === 'function' && next === null){
        expireTimeMS = expireTimeMS;
        expireTimeMS = null;
      }
      if(expireTimeMS === null){
        expireTimeMS = api.cache.lockDuration;
      }

      api.cache.checkLock(key, null, function(err, lockOk){
        if(err || lockOk !== true){
          next(err, false);
        }else{
          api.redis.client.setnx(api.cache.lockPrefix + key, api.cache.lockName, function(err){
            if(err){
              next(err);
            }else{
              api.redis.client.expire(api.cache.lockPrefix + key, Math.ceil(expireTimeMS/1000), function(err){
                lockOk = true;
                if(err){ lockOk = false; }
                next(err, lockOk);
              });
            }
          });
        }
      });
    };

    api.cache.unlock = function(key, next){
      api.cache.checkLock(key, null, function(err, lockOk){
        if(err || lockOk !== true){
          next(err, false);
        }else{
          api.redis.client.del(api.cache.lockPrefix + key, function(err){
            lockOk = true;
            if(err){ lockOk = false; }
            next(err, lockOk);
          });
        }
      });
    };
 ```
