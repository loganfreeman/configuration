onNext
---
```js
function createConnection(URI) {
  return Rx.Observable.create(function(observer) {
    debug('connecting to db');
    MongoClient.connect(URI, function(err, database) {
      if (err) {
        return observer.onError(err);
      }
      debug('db connected');
      observer.onNext(database);
      observer.onCompleted();
    });
  });
}
```
