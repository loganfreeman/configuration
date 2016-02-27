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
Disposable
---
```js
function createQuery(db, collection, options, batchSize) {
  return Rx.Observable.create(function(observer) {
    var cursor = db.collection(collection).find({}, options);
    cursor.batchSize(batchSize || 20);
    // Cursor.each will yield all doc from a batch in the same tick,
    // or schedule getting next batch on nextTick
    debug('opening cursor for %s', collection);
    cursor.each(function(err, doc) {
      if (err) {
        return observer.onError(err);
      }
      if (!doc) {
        console.log('onCompleted');
        return observer.onCompleted();
      }
      observer.onNext(doc);
    });

    return Rx.Disposable.create(function() {
      debug('closing cursor for %s', collection);
      cursor.close();
    });
  });
}
```
batch processing
---
```js
// batch them into arrays of twenty documents
var userSavesCount = users
  .bufferWithCount(batchSize)
  // get bd object ready for insert
  .withLatestFrom(dbObservable, function(users, db) {
    return {
      users: users,
      db: db
    };
  })
  .flatMap(function(dats) {
    // bulk insert into user collection
    return insertMany(dats.db, 'user', dats.users, { w: 1 });
  })
  .flatMap(function() {
    return totalUser;
  })
  .doOnNext(function(totalUsers) {
    count = count + batchSize;
    debug('user progress %s', count / totalUsers * 100);
  })
  // count how many times insert completes
  .count();
  ```
