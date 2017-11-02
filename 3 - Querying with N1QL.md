# Lab 3 - Querying with N1QL

This is the third lab for the Couchbase Test Drive. This is the second lab for the Couchbase Test Drive. You need to have completed [Lab 1 - Logging into Couchbase](1%20-%20Logging%20into%20Couchbase.md) before starting this lab.

## Objective

This lab is designed to get you familiar with the N1QL (Non 1st-Normal-Form Query Language). N1QL is a flavor of SQL that is designed to work with JSON data. If you've used any flavor of SQL in the past, you should be right at home. You will learn about Query Workbench, writing SELECT queries, basic JOINs, and data manipulate queries. At the end of this lab, you will have a basic understanding of N1QL.

N1QL is a very rich language, so this lab will only be scratching the surface. You are invited to check out the [extensive N1QL documentation](https://developer.couchbase.com/documentation/server/current/n1ql/n1ql-language-reference/index.html) or the [interactive N1QL tutorial](http://query.pub.couchbase.com/tutorial/#1), once you have a a handle for the basics.

This lab will not be covering any clients or SDK usage.

## Steps

### Query Workbench

From the Couchbase Console, click "Query". You will taken to an interactive page that allows you to enter a N1QL query, execute it, and see the results. Try a very simple query like `SELECT 1;`. You can type it in, end with a semicolon, and press "enter", or you can just click the "Execute" button. In the "Result" window, you'll see the full response to your request. It includes fields like `success` and `metrics`, but focus on the `results` field. It's an array of JSON documents. In this case, it's only one document with one field (assigned a name of `$1`, since you didn't specify a name), and a value of 1.

![First N1QL query](/images/3/0301-first-n1ql.png)

Before doing anything more advanced, you must first understand the basics of indexing. In relational databases, you often don't need to create indexes on small tables, because a table scan is often fast enough for small amounts of data. So, indexes aren't required.

In a huge collection of documents, you must create an index on the fields you want to query, otherwise the queries will not execute.

In Couchbase Server, You can optionally create an index on a buckeet that is roughly equivalent to a table scan: `CREATE PRIMARY INDEX def_primary on bucketname`. Fortunately, this index has already been created on the "travel-sample" bucket automatically, along with several other indexes. Click the "Indexes" tab in the Couchbase Console to see what indexes exist.

![Indexes](/images/3/0302-indexes.png)

An index called `def_primary` already exists on travel-sample.

There are several other indexes on this bucket as well. For instance, if you example the `def_type` index, you'll see that the `type` field is being indexed.

![Index on type field](/images/3/0303-def-type-index.png)

Consider this query:

```sql
SELECT t.*
FROM `travel-sample` t
WHERE t.type = 'airline';
```

What happens when you execute that query depends on the indexes you have (or don't have). If I have the `def_type` index, then this query will make use of that index to quickly fetch any document with a type value of "airline".

If all you have is the `def_primary` index, then the above query will need to scan every document in the bucket, which is significantly slower.

If you don't have any indexes at all, the above query will fail to execute with an error.

Try running the above query while experimenting with adding/removing indexes. Here are some commands you will need:

* Drop the primary index: `` DROP INDEX `travel-sample`.`def_primary`; ``
* Drop the type index: `` DROP INDEX `travel-sample`.`def_type`; ``
* Create the primary index: `` CREATE PRIMARY INDEX `def_primary` ON `travel-sample` ``
* Create the type index: `` CREATE INDEX `def_type` ON `travel-sample`(`type`) ``

When you're done, make sure both `def_type` and `def_primary` indexes exist in their initial state.

### JOIN

One feature that is somewhat unique to Couchbase Server is the ability to `JOIN` documents together in a query. Currently, a value can be joined to a document by its key.

Back to the travel-sample, here is a document with key **route_10000**.

```javascript
 {
    "sourceairport": "TLV",
    "destinationairport": "MRS",
    "airlineid": "airline_137",
    ...
}
```

Notice that is has a field, `airlineid`, which contains a value **airline_137**. The document with that key looks like:

```javascript
{
  "type": "airline",
  "name": "Air France",
  ...
}
```

There is another document with that key, so we could join these two documents together:

```sql
SELECT r.sourceairport, r.destinationairport, r.airlineid, a.name
FROM `travel-sample` r
JOIN `travel-sample` a ON KEYS r.airlineid
```

Notice that this query is performing a `JOIN` from the "travel-sample" bucket to itself. It is selecting every document from "travel-sample" and aliasing the fields from there as "r". It's joining to any document where the key matches the value of `airlineid`. `JOIN` is an `INNER JOIN` by default, so this query is implicitly limiting the set of documents returned in "r" by using `airlineid` (which is unique to route documents).

The above query will actually take quite a while to run (because it is using the primary index), and it will return 17000+ results. To narrow it down further, let's limit it to just **route_10000**.

```sql
SELECT r.sourceairport, r.destinationairport, r.airlineid, a.name
FROM `travel-sample` r
JOIN `travel-sample` a ON KEYS r.airlineid
WHERE META(r).id = 'route_10000';
```

More on `META` later, but for now, running that query should return one result.

```javascript
"results": [
    {
        "airlineid": "airline_137",
        "destinationairport": "MRS",
        "name": "Air France",
        "sourceairport": "TLV"
    }
]
```

The `airlineid`, `destinationairport`, and `sourceairport` fields all come from the route document. But the `name` field comes from the airline document.

### INSERT

With N1QL, you also get the ability to run INSERT, UPDATE, and DELETE queries (and more, like [MERGE](https://developer.couchbase.com/documentation/server/current/n1ql/n1ql-language-reference/merge.html) and [UPSERT](https://developer.couchbase.com/documentation/server/current/n1ql/n1ql-language-reference/upsert.html#topic_11_9)).

Let's go through all 3 to see how they work and how they differ from SQL that you've written before.

An `INSERT` will always be inserting into `KEY` and `VALUE` fields. The `VALUE` field can be a JSON literal, or the result of a `SELECT` query. Here's an example of an `INSERT` with a JSON literal:

```sql
INSERT INTO `travel-sample` (KEY, VALUE)
VALUES ("lab3document", { "name": "Matthew", "twitter": "@mgroves" } );
```

This will create a document with the key "lab3document". Now try finding that document by its key (refer back to [lab 2](2keyvaluedocumentstorage.md) if you need to). You can also use `RETURNING` in the query to return back the record that you just inserted:

```sql
INSERT INTO `travel-sample` t (KEY, VALUE)
VALUES (UUID(), { "name": "Matthew", "twitter": "@mgroves" } )
RETURNING t.*, META(t).id;
```

The `RETURNING` acts like a `SELECT`. In the above query, I've given the "travel-sample" bucket an alias of "t". In the `RETURNING`, I've asked for t.* (the entire document) and I used `META` to get its key. Since I used the `UUID` function to generate a unique key, the `RETURNING` comes in very handy in telling me the value of the key that was generated. Here are the results of the above `INSERT` query:

```javascript
"results": [
    {
        "id": "4ecff4f1-dda0-4c9e-bbe1-3c0d95734253",
        "name": "Matthew",
        "twitter": "@mgroves"
    }
]
```

Try this for yourself in Query Workbench.

### UPDATE

`UPDATE` in N1QL is used to make changes to an existing document. Assuming you still have the "lab3document" from above, here's an example of `UPDATE` in action:

```sql
UPDATE `travel-sample`
USE KEYS "lab3document"
SET name = "Matthew Groves", shoeSize = 13;
```

With this `UPDATE`, I'm changing the value of the existing `name` field to be my first and last name. Also note that I setting the value of `shoeSize`, which previously didn't exist in the document.

`RETURNING` can also be used with an `UPDATE`.

### DELETE

Finally, let's get rid of this "lab3document" with a `DELETE` query.

```sql
DELETE FROM `travel-sample`
USE KEYS "lab3document";
```

This will delete the document with the matching key. If you use a `RETURNING` with a `DELETE`, it will return the document that you deleted.

### META and other JSON tidbits (TODO)

`META` has been mentioned a few times already, and `UUID` was also mentioned. These are [N1QL functions](https://developer.couchbase.com/documentation/server/current/n1ql/n1ql-language-reference/functions.html) (of which there are many). `UUID` simply generates a universally unique identifier (similar to a GUID). `META` is a function that, applied to a document, returns metadata about that document. The document key (`id`) is the main reason you'd use `META`, but there is other information in there that might be of some use.

Before wrapping up, there is one more basic thing about N1QL that should be made explicit. Earlier, a "route" document was used as an example. This document contains an array of objects as a field. N1QL needs a way to address both array elements and fields within an object hierarchy. Intuitively, you'll use the JavaScript array `[]` syntax and dotted `.` syntax. Here's an example of a query that gets the first item from the schedule array, and the `day` value within that item.

```sql
SELECT t.schedule[0].day
FROM `travel-sample` t
WHERE META(t).id = "route_10000";
```

Because there are arrays and hierarchies present in JSON data, the N1QL language has added a lot of statements, functions, and operators to query different shapes of data. Because of this, N1QL is considered a "superset" of SQL.

## Summary

In this lab, you have learned the basics of querying documents with N1QL. You have learned how to use the Query Workbench, SELECT documents, modify documents with N1QL, and some unique features of N1QL to deal with JSON. With N1QL, you can query denormalized JSON documents while being productive very quickly with a familiar SQL syntax.

There is a lot more to learn about N1QL, even for SQL experts. Check out the [N1QL documentation for a full language reference](https://developer.couchbase.com/documentation/server/current/n1ql/n1ql-language-reference/index.html), and if you have tricky N1QL questions, the [Couchbase N1QL forums](https://forums.couchbase.com/c/n1ql) can help you find the answers.

N1QL is a great tool for finding documents that meet strict criteria. It is not necessarily the best tool for a generalized search. For that, please check out [lab 4 to learn how find documents with a full text search](4%20-%20Full%20Text%20Search.md)

_Important note: If you can use a key/value operation, you should: it will be faster than running a query. However, key/value operations are sometimes not feasible for certain data access patterns, and that's where N1QL can help._
