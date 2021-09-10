# Final Exam

## Question 1

Problem:

Which of these statements is/are true?
Attempts Remaining:Correct Answer

Check all answers that apply:

- [X] Creating an ascending index on a monotonically increasing value creates index keys on the right-hand side of the index tree.
- [ ] You can index multiple array fields in a single document with a single compound index.
- [ ] Write concern has no impact on write latency.
- [ ] Covered queries can sometimes still require some of your documents to be examined.
- [ ] A collection scan has a logarithmic search time.

## Question 2

Problem:

Which of the following statements is/are true?

Check all answers that apply:

- [X] It's important to ensure that your shard key has high cardinality.
- [X] It's important to ensure that secondaries with indexes that differ from the primary not be eligible to become primary.
- [X] Indexes can be walked backwards by inverting their keys in a sort predicate.
- [X] Partial indexes can be used to reduce the size requirements of the indexes.
- [X] Indexes can decrease insert throughput.

## Question 3

Problem:

Which of the following statements is/are true?

Check all answers that apply:

- [X] Collations can be used to create case insensitive indexes.
- [X] By default, all MongoDB user-created collections have an _id index.
- [X] It's common practice to co-locate your mongos on the same machine as your application to reduce latency.
- [ ] MongoDB indexes are markov trees.
- [ ] Background index builds block all reads and writes to the database that holds the collection being indexed.

## Question 4

Problem:

Which of the following statements is/are true?

Check all answers that apply:

- [ ] Under heavy write load you should scale your read throughput by reading from secondaries.
- [X] Indexes are fast to search because they're ordered such that you can find target values with few comparisons.
- [X] Indexes can solve the problem of slow queries.
- [ ] When you index on a field that is an array it creates a partial index.
- [ ] On a sharded cluster, aggregation queries using $lookup will require a merge stage on a random shard.

## Question 5

Problem:

Which of the following statements is/are true?

Check all answers that apply:

- [ ] Compound indexes can service queries that filter on a prefix of the index keys.
- [ ] By default, the explain() command will execute your query.
- [ ] Compound indexes can service queries that filter on any subset of the index keys.
- [ ] Query plans are removed from the plan cache on index creation, destruction, or server restart.
- [X] If no indexes can be used then a collection scan will be necessary.

## Question 6

Problem:

Which of the following statements is/are true?

Check all answers that apply:

- [x] An index doesn't become multikey until a document is inserted that has an array value.
- [ ] Indexes can only be traversed forward.
- [x] You can use the --wiredTigerDirectoryForIndexes option to place your indexes on a different disk than your data.
- [X] The ideal ratio between nReturned and totalKeysExamined is 1.
- [ ] Running performance tests from the mongo shell is an acceptable way to benchmark your database.

## Question 7

Problem:

Given the following indexes:

```js
{ categories: 1, price: 1 }
{ in_stock: 1, price: 1, name: 1 }
```

The following documents:

```js
{ price: 2.99, name: "Soap", in_stock: true, categories: ['Beauty', 'Personal Care'] }
{ price: 7.99, name: "Knife", in_stock: false, categories: ['Outdoors'] }
```

And the following queries:

```js
db.products.find({ in_stock: true, price: { $gt: 1, $lt: 5 } }).sort({ name: 1 })
db.products.find({ in_stock: true })
db.products.find({ categories: 'Beauty' }).sort({ price: 1 })
```

Which of the following is/are true?

Check all answers that apply:

- [ ] Index #2 properly uses the equality, sort, range rule for query #1.
- [X] Index #2 can be used by both query #1 and #2.
- [X] Index #1 would provide a sort to query #3.
- [ ] There would be a total of 4 index keys created across all of these documents and indexes.
