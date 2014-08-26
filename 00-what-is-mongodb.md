What is MongoDB?
================

Database
--------

MongoDB (a play on the word “hu**mongo**us”) is an open source database classified as `NoSQL` and written in `C++`.


NoSQL
-----

As a `NoSQL` database, it doesn't describe data in tables, rows and relations.


Documents
---------

Instead it uses the concepts of [**documents**](http://docs.mongodb.org/manual/core/document/) for records and [**collections**](http://docs.mongodb.org/manual/reference/glossary/#term-collection) instead of tables.


JSON
----

Documents have a general structure that most of you are probably familiar with: `JSON`. That means a document has **fields** and **values**. Values can basically be **strings**, **numbers**, **booleans**, **arrays** of values (denoted by `[]`) and **sub-documents** (objects denoted by nested `{}`).

```js
{
    name: 'Han Solo',
    occupation: 'Smuggler',
    likes: [ 'wisecracks', 'shooting first' ],
    weapon: { amount: 1, type: { id: 3, name: 'blaster pistol', firepower: 'low' } },
    debt: true
}
```


Dynamic schema
--------------

While collections are analogous to the tables in other databases, collections **don't enforce a specific schema** for the documents contained within them. In other words, individual documents in a collection can have whatever structure is convenient for your purpose. No more columns full of `NULL` values! No more table structure migrations!


Replication
-----------

MongoDB has high availability built in. [Replication sets](http://docs.mongodb.org/manual/core/replication-introduction/) are small clusters of database servers that keep redundant copies of your data and failover between each other automatically to minimize your data's downtime.


Sharding
--------

Horizontal scaling is also provided built in via [sharding](http://docs.mongodb.org/manual/core/sharding-introduction/). Sharding allows easy distribution of your data between multiple servers to share the load.


JavaScript
----------

When you connect to your database directly (and you know you will have to eventually), there's no crazy syntax to remember. It's just a simple [CRUD API](http://docs.mongodb.org/manual/crud/) and `JavaScript`:

```js
db.characters.find({ occupation: 'Smuggler' });
```

We all know at least a little bit of `JavaScript`, right?
