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
        { name: 'Lando Calrissian', occupation: 'Cloud City Administrator' },
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

The `update()` method modifies existing documents by changing specific fields or replacing the document entirely. Here's the gist of it:

```js
db.characters.update(
    { name: 'Han Solo' },
    { $set: { debt: false } }
);
```

The first argument is a `query` object, just like with `remove()`. You use it to specify which documents you want to update.

The second argument is the `update` object, which describes the modifications that you want to apply to the matched documents. It uses [update operators](http://docs.mongodb.org/manual/reference/operator/update/) to describe the modifications. We will discuss those shortly.


### Single document ###

By default, calls to `update()` only modify a single document:

```js
// Set a single bounty hunter's `owed` field to 0
db.characters.update(
    { occupation: 'Bounty Hunter' },
    { $set: { owed: 0 } }
);
```


### Multiple documents ###

If you want to apply the modification to multiple documents, send the `multi` option:

```js
// Set all bounty hunter's `owed` fields to 0
db.characters.update(
    { occupation: 'Bounty Hunter' },
    { $set: { owed: 0 } },
    { multi: true }
);
```


### Update operators ###

You might have noticed that in these examples we have used `$set` in our `update` object. This `$set` is an [**update operator**](http://docs.mongodb.org/manual/reference/operator/update/), a field with special meaning in an `update` objects. Special operators in `MongoDB` such as `$set` for `update()` start with a dollar sign `$`. Because of this convention, `MongoDB` doesn't allow your field names to start with `$`.


### Replacing the entire document ###

Typically when you update a record in a database, you are just setting specific fields and leaving the rest of the record unchanged. In `MongoDB` if you use that approach you will replace the entire document! Take this `update()` for example:

```js
// Replace Han Solo with a document that only has `debt` equal to false
db.characters.update(
    { name: 'Han Solo' },
    { debt: false }
);
```

This update **replaces the entire Han Solo document** with this document:

```js
// The smuggler formerly known as Han
{ debt: false }
```

Sometimes this is the desired functionality, but most of the times I find that what we want is to just update the specified fields and not mess with anything else.


### Update operator: `$set` ###

That's why we use [`$set`](http://docs.mongodb.org/manual/reference/operator/update/set/)! The `$set` update operator modifies the fields you specify and that's it. Take the above example, now using `$set`:

```js
// Set Han Solo's `debt` field to false
db.characters.update(
    { name: 'Han Solo' },
    { $set: { debt: false } }
);
```

As with all of the update operators we will talk about here, `$set` can modify multiple fields at the same time:

```js
// Set Han Solo's `debt` and `scruffyLooking` fields to false
db.characters.update(
    { name: 'Han Solo' },
    { $set: { debt: false, scruffyLooking: false } }
);
```

Let's discuss some other useful update operators!


### Update operator: `$unset` ###

You can use the [`$unset`](http://docs.mongodb.org/manual/reference/operator/update/unset/) update operator to delete fields from a document:

```js
// Delete the `debt` field from the Han Solo document
db.characters.update(
    { name: 'Han Solo' },
    { $unset: { debt: '' } }
);
```

The value you give the field in the `$unset` expression doesn't affect the operation. This works just as well:

```js
// Delete the `debt` field from the Han Solo document
db.characters.update(
    { name: 'Han Solo' },
    { $unset: { debt: 'Tell Jabba that I’ve got his money.' } }
);
```


### Update operator: `$inc` ###

The [`$inc`](http://docs.mongodb.org/manual/reference/operator/update/inc/) update operator is for incrementing the values of fields:

```js
// Increase Boba Fett's `owed` field by 5000
db.characters.update(
    { name: 'Boba Fett' },
    { $inc: { owed: 5000 } }
);
```

If the field doesn't exist, it will add the field with the specified amount. Take this document for example:

```js
{
    name: 'Jango Fett',
    occupation: 'Bounty Hunter'
}
```

If we run this update:

```js
db.characters.update(
    { name: 'Jango Fett' },
    { $inc: { owed: 10000 } }
);
```

It will result in this updated document:

```js
{
    name: 'Jango Fett',
    occupation: 'Bounty Hunter',
    owed: 10000
}
```


### Update operators: `$push` ###

The [`$push`](http://docs.mongodb.org/manual/reference/operator/update/push/) update operator is designed to work with array fields inside your document. It adds a value to an array field:

```js
// Add `credits` to Han Solo's `likes` array field
db.characters.update(
    { name: 'Han Solo' },
    { $push: { likes: 'credits' } }
);
```

Like with `$inc`, if the field doesn't exist `$push` will add the field with the value inside the array.


Exercise
--------

...


### [Back to: Data modeling](01-data-modeling.md) ###
