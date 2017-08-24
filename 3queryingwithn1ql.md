# Lab 3 - Querying with N1QL

This is the third lab for the Couchbase Azure Test Drive. You need to have completed at least the [first lab (Couchbase basics)](1couchbasebasics.md) before starting this lab. The [second lab (Key/Value Document Storage)](2keyvaluedocumentstorage.md) is optional, but recommended.

## Objective

This lab is designed to get you familiar with the N1QL (Non 1st-Normal-Form Query Language). N1QL is a flavor of SQL that is designed to work with JSON data. If you've used any flavor of SQL in the past, you should be right at home. You will learn about Query Workbench, writing SELECT queries, basic JOINs, and data manipulate queries. At the end of this lab, you will have a basic understanding of N1QL.

N1QL is a very rich language, so this lab will only be scratching the surface. You are invited to check out the link:https://developer.couchbase.com/documentation/server/current/n1ql/n1ql-language-reference/index.html[extensive N1QL documentation] or the link:http://query.pub.couchbase.com/tutorial/#1[interactive N1QL tutorial], once you have a a handle for the basics.

This lab will not be covering any clients or SDK usage.

## Steps

### Query Workbench

From the Couchbase Console, click "Query". You will taken to an interactive page that allows you to enter a N1QL query, execute it, and see the results. Try a very simple query like `SELECT 1;`. You can type it in, end with a semicolon, and press "enter", or you can just click the "Execute" button. In the "Result" window, you'll see the full response to your request. It includes fields like `success` and `metrics`, but focus on the `results` field. It's an array of JSON documents. In this case, it's only one document with one field (assigned a name of `$1`, since you didn't specify a name), and a value of 1.

![First N1QL query](/images/0301-first-n1ql.png)

Before doing anything more advanced, you must first understand the basics of indexing. In relational databases, you often don't need to create indexes on small tables, because a table scan is often fast enough for small amounts of data. So, indexes aren't required.

In a huge collection of documents, you must create an index on the fields you want to query, otherwise the queries will not execute.

In Couchbase Server, You can optionally create an index on a buckeet that is roughly equivalent to a table scan: `CREATE PRIMARY INDEX def_primary on bucketname`. Fortunately, this index has already been created on the "travel-sample" bucket automatically, along with several other indexes. Click the "Indexes" tab in the Couchbase Console to see what indexes exist.

![Indexes](/images/0302-indexes.png)

An index called `def_primary` already exists on travel-sample.

There are several other indexes on this bucket as well. For instance, if you example the `def_type` index, you'll see that the `type` field is being indexed.

![Index on type field](/images/0303-def-type-index.png)

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
],
```

The `airlineid`, `destinationairport`, and `sourceairport` fields all come from the route document. But the `name` field comes from the airline document.

### INSERT/UPDATE/DELETE

TODO

### META and other JSON tidbits (TODO)

TODO

## Summary

In this lab, you have learned all the basics of querying documents with N1QL. You have learned how to use the Query Workbench, SELECT documents, modify documents with N1QL, and some unique features of N1QL to deal with JSON. With N1QL, you can query denormalized JSON documents while being productive very quickly with a familiar SQL syntax.

N1QL is a great tool for finding documents that meet strict criteria. It is not necessarily the best tool for a generalized search. For that, please check out [lab 4 to learn how find documents with a full text search](4fts.md)

_Important note: If you can use a key/value operation, you should: it will be faster than running a query. However, key/value operations are sometimes not feasible for certain data access patterns, and that's where N1QL can help._