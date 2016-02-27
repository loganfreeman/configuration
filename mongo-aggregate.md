aggregate emails for matching users
---
```js
  database.collection('user').aggregate([
    {$match: { 'email': { $exists: true } } },
    {$match: { 'email': { $ne: '' } } },
    {$match: { 'email': { $ne: null } } },
    {$match: { 'sendMonthlyEmail': true } },
    {$match: { 'email': { $not: /(test|fake)/i } } },
    {$group: { '_id': 1, 'emails': {$addToSet: '$email' } } }
  ], function(err, results) {
    if (err) { throw err; }

    console.log('\"email\"\n\"' + results[0].emails.join('\"\n\"') + '\"');
    process.exit(0);
  });
```
