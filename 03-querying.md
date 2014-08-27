Querying
========

**Queryies** or read operations in `MongoDB` are the ones that retrieve your data from the database. They make use of a `criteria` object that you send to specify which documents you want to retrieve. You can also send an optional `projection` object that specifies the fields you want to retrieve from those documents.


`find()`
--------

The most common read operation is your basic [`find()`](http://docs.mongodb.org/manual/reference/method/db.collection.find/), which retrieves all the documents you specify with your `criteria` object:

```js
// Retrieve all bounty hunters
db.characters.find( { occupation: 'Bounty Hunter' } );
```


Query by sub-document
---------------------

You can also query documents by sub-documents they might have by using a `criteria` object and [**dot notation**](http://docs.mongodb.org/manual/core/document/#dot-notation) fields:

```js
// Find bounty hunters that use blaster pistols
db.characters.find(
    {
        occupation: 'Bounty Hunter',
        'weapon.type.name': 'blaster pistol'
    }
);
```


Query operators
---------------

Using `find()` is easy enough with equality criteria, but where it gets really interesting is when you start getting into [**query operators**](http://docs.mongodb.org/manual/reference/operator/query/). Just like update operators, query operators start with a dollar sign `$` and signify special logic for your read operations. Let's look at some basic but useful ones.


### `$ne` ###

The `$ne` query operator matches documents in which the field isn't equal to the `$ne` value:

```js
// Retrieve all characters who aren't bounty hunters
db.characters.find( { occupation: { $ne: 'Bounty Hunter' } } );
```


### `$gt` and `$gte` ###

The `$gt` operator matches documents in which the field is greater than the `$gt` value:

```js
// Retrieve bounty hunters who are owed more than 10,000 credits
db.characters.find(
    {
        occupation: 'Bounty Hunter',
        owed: { $gt: 10000 }
    }
);
```

The `$gte` operator works identically except it also matches documents in which the field is equal to the `$gte` value.


### `$lt` and `$lte` ###

The `$lt` operator matches documents in which the field is less than the `$lt` value:

```js
// Retrieve bounty hunters who are owed less than 5,000 credits
db.characters.find(
    {
        occupation: 'Bounty Hunter',
        owed: { $lt: 5000 }
    }
);
```

The `$lte` operator works identically except it also matches documents in which the field is equal to the `$lte` value.


### `$or` ###

If you notice, `criteria` objects use logical `AND` to select documents. Take this `find()` for example:

```js
// Retrieve characters who are bounty hunters AND are also named Boba Fett
db.characters.find( { name: 'Boba Fett', occupation: 'Bounty Hunter' } );
```

The `$or` operator allows you to express your `criteria` using logical `OR` by sending it an array of alternatives:

```js
// Retrieve characters who are bounty hunters OR have blaster pistols
db.characters.find(
    {
        $or: [
            { occupation: 'Bounty Hunter' },
            { 'weapon.type.name': 'blaster pistol' }
        ]
    }
);
```


### `$in` ###

The `$in` operator allows you to elegantly match multiple values to a single field:

```js
// Retrieve smugglers and bounty hunters
db.characters.find( { occupation: { $in: [ 'Smuggler', 'Bounty Hunter' ] } } );
```

If the field is an array, it matches documents that have at least one of the values:

```js
// Retrieve characters whose `likes` array contains credits OR wisecracks
db.characters.find( { likes: { $in: [ 'credits', 'wisecracks' ] } } );
```


### `remove()` and `update()` ###

These operators can also be used to select documents to modify using the `remove()` and `update()` methods.


`projection` objects
--------------------

A [`projection`](http://docs.mongodb.org/manual/core/read-operations-introduction/#projections) is just some fields with values associated to them. Use `0` to exclude fields and `1` to include them.


### Inclusive projections ###

Here's our previous bounty hunter query but making use of an inclusive `projection` object to specify fields that we want to include:

```js
// Retrieve all bounty hunters but only send me the `name` and `owed` fields
db.characters.find(
    { occupation: 'Bounty Hunter' },
    { name: 1, owed: 1 }
);
```


### Exclusive projections ###

Here's a similar query but making use of an exclusive `projection` object to specify fields that we **don't** want to include:

```js
// Retrieve all bounty hunters but don't send me the `occupation`, `weapon` or `likes` fields
db.characters.find(
    { occupation: 'Bounty Hunter' },
    { occupation: 0, weapon: 0, likes: 0 }
);
```

### Don't mix projections ###

You can't mix inclusive and exclusive fields in a single projection. You have to use one or the other.


### Projecting `_id` ###

The `_id` field is always included in the projection by default unless you explicitly set it to `0`:

```js
// Retrieve all bounty hunters but REALLY only send me the `name` and `owed` fields
db.characters.find(
    { occupation: 'Bounty Hunter' },
    { name: 1, owed: 1, _id: 0 }
);
```

Excluding `_id` in an inclusive projection is the only exception to mixing projections.


`limit()`
---------

The [`limit()`](http://docs.mongodb.org/manual/reference/method/db.collection.find/#limit-the-number-of-documents-to-return) method allows you to limit the amount of results retrieved from a `find()`. You call it after `find()`:

```js
// Retrieve 10 characters
db.characters.find().limit(10);
```


`sort()`
--------

The [`sort()`](http://docs.mongodb.org/manual/reference/method/cursor.sort/) method allows you to sort the results retrieved from a `find()`:

```js
// Retrive all characters sorted by ascending occupation then descending `owed`
db.characters.find().sort( { occupation: 1, owed: -1 } );
```

The method receives a `sort` object in which you can specify the fields you would like to sort by, the order in which to use fields to sort and whether it should sort in ascending or descending order for each field. Use a value of `1` for ascending or a value of `-1` for descending.



Exercise
--------

1. Find the station with the `name` *Station 42363 - Mars*.
2. Find stations that aren't `owned` by *National Data Buoy Center*.
3. Find stations with *AMPS* or *ARES* `payload`.
4. Find stations with `condition.wind_speed_milehour` greater than `3` or `condition.air_temperature_celsius` less than `20`.
5. Find the 10 stations with the lowest `condition.sea_surface_temperature_celsius`.


### [Back to: Write operations](02-write-operations.md) | [Next: Indexing](04-indexing.md) ###
