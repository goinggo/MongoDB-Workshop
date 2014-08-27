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

The `insert()` method inserts documents into a collection.


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

The `remove()` method deletes documents from a collection. Here's the basic structure of a `remove()`:

```js
db.characters.remove( { occupation: 'Smuggler' } );
```

The first argument is called a `query` object. It specifies which documents you want to delete using [query operators](http://docs.mongodb.org/manual/reference/operator/query/), which we will discuss in more detail in the Querying section. For now we will use simple equals in our `query` objects. In these examples, the `query` object matches any document with `occupation` field equal to *Smuggler*.


### Multiple documents ###

By default, a call to `remove()` will delete any documents in the collection that match your `query` object:

```js
// Delete all smugglers
db.characters.remove( { occupation: 'Smuggler' } );
```


### Single document ###

To delete only a single document that matches the `query` object, send the `justOne` option in the call to `remove()`:

```js
// Delete a single smuggler
db.characters.remove(
    { occupation: 'Smuggler' },
    { justOne: true }
);
```


### Delete all documents ###

To delete all documents in a collection, use an empty `query` object:

```js
// Delete all characters
db.characters.remove( {} );
```


`update()`
----------

...


Exercise
--------

...


### [Back to: Data modeling](01-data-modeling.md) ###
