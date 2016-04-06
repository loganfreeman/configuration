Laravel
---
Model
```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Book extends Model
{
    /**
     * Get the author that wrote the book.
     */
    public function author()
    {
        return $this->belongsTo('App\Author');
    }
}
```
Query
```php
$books = App\Book::with('author')->get();

foreach ($books as $book) {
    echo $book->author->name;
}
```
SQL
```sql
select * from books

select * from authors where id in (1, 2, 3, 4, 5, ...)
```
