Data modeling
=============

There are no joins in `MongoDB`. This makes modeling your data a little bit different than in a relational database.


Sub-documents
-------------

Instead of joining tables, when using `MongoDB` what you can do is put all the data related to each document inside the document itself in the form of **sub-documents**. A sub-document is just a field in your document that contains an object or array of objects that represent what would normally be a relation in a relational database. It's as if your collection was **pre-joined**. In our Han Solo example, `weapon` is considered a sub-document.

```js
{
    /* ... */
    weapon: { amount: 1, type: { id: 3, name: 'blaster pistol', firepower: 'low' } },
    /* ... */
}
```


Don't normalize
---------------

Since your collections are pre-joined, there will be a lot of redundant data. For example, if there are a lot of `characters` with blaster pistols, the data that describes the blaster pistol `type` will be redundant many times over. In general, you shouldn't worry about [**database normalization**](http://en.wikipedia.org/wiki/Database_normalization) (trying to eliminate redundancies in your data model). It's okay to have data duplicated for the sake of faster reads. Reads are faster if they only have to go to a single collection to get their data.


Data model duel
---------------

Let's see how the schema for our Han Solo example would compare between `MySQL` and `MongoDB`.


### MySQL ###

For `MySQL` we obviously need a `characters` table (without indexes for simplicity's sake):

```sql
CREATE TABLE characters (
    id INT,
    name VARCHAR(100),
    occupation INT,     # foreign key relation
    weapon_amount INT,
    weapon_type INT,    # foreign key relation
    debt TINYINT(1)
);

INSERT INTO characters VALUES (1, 'Han Solo', 2, 1, 3, TRUE);
```

We also need separate tables for `occupation`, `likes` and `weapon_type` since they are relations. Here's `occupation` and `weapon_type`:

```sql
CREATE TABLE occupations (id INT, name VARCHAR(100));
INSERT INTO occupations VALUES
    (1, 'Bounty Hunter'),
    (2, 'Smuggler'),
    (3, 'Jedi')
;

CREATE TABLE weapon_types (id INT, name VARCHAR(100), firepower VARCHAR(100));
INSERT INTO weapon_types VALUES
    (1, 'lightsaber', NULL),
    (2, 'bowcaster', 'high'),
    (3, 'blaster pistol', 'low')
;
```

In the case of `likes` we need two tables since it's a many-to-many relation:

```sql
CREATE TABLE likes (id INT, name VARCHAR(100));
INSERT INTO likes VALUES
    (1, 'wisecracks'),
    (2, 'shooting first'),
    (3, 'credits')
;

CREATE TABLE characters_likes (character_id INT, like_id INT);
INSERT INTO characters_likes VALUES
    (1, 1),
    (1, 2)
;
```

I'm not even going to get into what the query would look like. Urgh...


### MongoDB ###

Now for `MongoDB` let's again bring up our example from the introduction:

```js
db.characters.insert({
    name: 'Han Solo',
    occupation: 'Smuggler',
    likes: [ 'wisecracks', 'shooting first' ],
    weapon: { amount: 1, type: { id: 3, name: 'blaster pistol', firepower: 'low' } },
    debt: true
});
```

This may be a contrived example, but the point is that relational databases can be tedious to develop on at times. Often in those cases, working with a dynamic schema and no-normalization database like `MongoDB` is much nicer.


Different structures for different junctures
--------------------------------------------

There may be some situations in which it's more appropriate to treat your data relationally and make multiple reads to the database to build your compose your application's data model. Evaluate the needs of your projects on a case by case basis!


### [Back to: What is MongoDB?](00-what-is-mongodb.md) ###
