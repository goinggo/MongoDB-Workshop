Write operations
================

Database operation basics
-------------------------

In general, database operations in `MongoDB` are accessed through a `db` object and have the following structure:

```js
/*=============================
    |  collection  |  method  |
===---============---=========*/
db  .  characters  .  find()  ;
```


`insert()`
----------

### Single document ###

In the previous section, we saw a sneak peak of how [`insert()`](http://docs.mongodb.org/manual/reference/method/db.collection.insert/) works. Here's a more basic example of inserting a `characters` document:

```js
db.characters.insert( { name: 'Boba Fett', occupation: 'Bounty Hunter' } );
```


### Multiple documents ###

You can also insert arrays of documents to insert multiples with a single call:

```js
db.characters.insert(
    [
        { name: 'Lando Calrissian', occupation: 'Cloud  City Administrator' },
        { name: 'Admiral Ackbar' },
        { _id: 1, name: 'Obi-Wan Kenobi' }
    ]
);
```


### Dynamic schema redux ###

Notice that these documents have different fields. As we covered in the first section, that is okay!


### Collection creation ###

When you call `insert()`, `MongoDB` **automatically** creates the collection if it doesn't exist. You can write to your collections with no setup!


### `_id` field ###

By default, `MongoDB` uses the `_id` field as the primary key for a document in your collections. If you don't specify a value for `_id` in a document, the database will assign a unique value for you. Note that `_id` values you specify must be unique within the collection or you will get an error.


`remove()`
----------

...


`update()`
----------

...


Exercise
--------

...


### [Back to: Data modeling](01-data-modeling.md) ###
