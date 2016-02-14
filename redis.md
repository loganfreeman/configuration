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
do cluster
---
```node
  api.redis.subscribe = function(callback){
      var channel = api.config.redis.channel;

      if(api.redis.status.subscribed){
        return callback();
      }

      api.redis.subscriber.on('message', function(messageChannel, message){
        try{ message = JSON.parse(message); }catch(e){ message = {}; }
        if(messageChannel === channel && message.serverToken === api.config.general.serverToken){
          if(api.redis.subscriptionHandlers[message.messageType]){
            api.redis.subscriptionHandlers[message.messageType](message);
          }
        }
      });

      api.redis.subscriber.subscribe(channel, function(){
        api.redis.status.subscribed = true;
        callback();
      });
    };

    api.redis.publish = function(payload){
      var channel = api.config.redis.channel;
      api.redis.client.publish(channel, JSON.stringify(payload));
    };
    // Subsciption Handlers

    api.redis.subscriptionHandlers.do = function(message){
      if(!message.connectionId || ( api.connections && api.connections.connections[message.connectionId]) ){
        var cmdParts = message.method.split('.');
        var cmd = cmdParts.shift();
        if(cmd !== 'api'){ throw new Error('cannot operate on a method outside of the api object') }
        var method = api.utils.stringToHash(cmdParts.join('.'));

        var callback = function(){
          var responseArgs = Array.apply(null, arguments).sort();
          process.nextTick(function(){
            api.redis.respondCluster(message.requestId, responseArgs);
          });
        };
        var args = message.args;
        if(args === null){ args = []; }
        if(!Array.isArray(args)){ args = [args]; }
        args.push(callback);
        method.apply(null, args);
      }
    };

    api.redis.subscriptionHandlers.doResponse = function(message){
      if(api.redis.clusterCallbaks[message.requestId]){
        clearTimeout(api.redis.clusterCallbakTimeouts[message.requestId]);
        api.redis.clusterCallbaks[message.requestId].apply(null, message.response);
        delete api.redis.clusterCallbaks[message.requestId];
        delete api.redis.clusterCallbakTimeouts[message.requestId];
      }
    };

    // RPC

    api.redis.doCluster = function(method, args, connectionId, callback){
      var requestId = uuid.v4();
      var payload = {
        messageType  : 'do',
        serverId     : api.id,
        serverToken  : api.config.general.serverToken,
        requestId    : requestId,
        method       : method,
        connectionId : connectionId,
        args         : args,   // [1,2,3]
      };

      api.redis.publish(payload);

      if(typeof callback === 'function'){
        api.redis.clusterCallbaks[requestId] = callback;
        api.redis.clusterCallbakTimeouts[requestId] = setTimeout(function(requestId){
          if(typeof api.redis.clusterCallbaks[requestId] === 'function'){
            api.redis.clusterCallbaks[requestId](new Error('RPC Timeout'));
          }
          delete api.redis.clusterCallbaks[requestId];
          delete api.redis.clusterCallbakTimeouts[requestId];
        }, api.config.redis.rpcTimeout, requestId);
      }
    };

    api.redis.respondCluster = function(requestId, response){
      var payload = {
        messageType  : 'doResponse',
        serverId     : api.id,
        serverToken  : api.config.general.serverToken,
        requestId    : requestId,
        response     : response, // args to pass back, including error
      };

      api.redis.publish(payload);
    };
```
