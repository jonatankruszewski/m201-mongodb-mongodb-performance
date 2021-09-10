# M201 MongoDB Performance

## CHAPTER 1

PERFORMANCE. 5 CHAPTER. INTERMIEADTE. DEVELOPMENT AND OPERATIONS TEAMS.
OVERVIEW
INDEXES
INDEX OPERATIONS
CRUD OPTIMIZATIONS
DISTRIBUTED SYSTEMS

### QUIZ

Introduction to MongoDB Performance
Problem:

Which of the following statements is/are true?
Attempts Remaining:∞Unlimited Attempts

Check all answers that apply:

- [ ] Quizzes and labs each make up 50% of your final grade in the class.
- [ ] The content of this course is more geared for DBAs than developers.
- [X] Indexes will be a major topic covered in this course.
- [ ] This course will take a deep dive into how different types of computer hardware can be used to improve database performance.

### Lecture: Hardware and configurations

- Ram is 25X faster than ssds
- Aggregation, index traveersing, write operations, connections and more are all handled in RAM in Mongodb.
- By default, MongoDB will try to use all CPU cores. The most CPUS, mongo performs the better
- Page compression, data calculation, map reduce are CPU demanding tasks.

## LECTURE HARDWARE CONSIDERATIONS AND CONFIGURATIONS PART 2

- Some writes are blocking. For example, updating the same document, is a blocking operation. Multiple CPUS won't help.
- IOPS capacity will affect the deployment.
- RAID architecture might benefit MongoDB deployment.
- RAID 10: RAID 1 + RAID 0. All blocks are duplicated. Minimum of 4 drives.
- Level 1: Mirroring, Level 0: stripping (merging into a larger volume)
- Level 0: Adds speed, read and write from multiple disks.
- Level 0: is not redundant.
- Level 1: Redundancy. Good for avoiding data loss and downtime. Also increase in read speeds. (can read from either of them). Downside: Higher write latency.
- Discouraged: RAID 5, 6, 0.

### QUIZ Chapter 1: Introduction

Hardware Considerations & Configurations Part 2
Problem:

Regarding the performance implications, which of the following statements are correct?
Attempts Remaining:∞Unlimited Attempts

Check all answers that apply:

- [ ] MongoDB does not benefit from adding more RAM to your servers.
- [ ] Disk RAID architecture can impact the performance of your MongoDB deployment.
- [ ] Switching from HDDs to SSDs does not bring a benefit to the performance of MongoDB.
- [X] CPU availability impacts the performance of MongoDB, especially if using the WiredTiger storage engine.

### Lab 1.1: Install Course Tools and Datasets

Check that:
Brew is installed

MongoDB Enterprise is installed. [link](https://www.mongodb.com/try/download/enterprise)

MongoDB Shell is installed. [link](https://www.mongodb.com/try/download/shell)
Or:
brew install mongodb/brew/mongodb-community-shell

MongoDB Database tools is installed. [link](https://www.mongodb.com/try/download/database-tools)
After downloading, cd into the /bin folder and copy the files to /usr/local/bin

```js
sudo cp * /usr/local/bin/
```

To chech brew services (mongodb community should be listed):

```js
brew services list
```

To start mongodb:

```js
brew services start mongodb-community
```

To start the mongoShell:

```js
mongo
```

To stop mongodb:

```js
brew services stop mongodb-community
```

To bring the people.json file:
mongoimport --drop -c people --uri mongodb+srv://user:password@m201.g9a0m.mongodb.net/test /Users/jkru/DATA/DEV/JK/m201MongoDB/people.json

```js
user: M201STUDENT
pass: M201PASSWORD
```

```sh
mongosh "mongodb+srv://m201.w1fqb.mongodb.net/myFirstDatabase" --username M201STUDENT
```

```sh
mongodb+srv://M201STUDENT:M201PASSWORD@m201.w1fqb.mongodb.net/test
```

To upload people.json:

```sh
 mongoimport --drop -c people --uri 'mongodb+srv://M201STUDENT:M201PASSWORD@m201.w1fqb.mongodb.net/m201' people.json
 ```

Same for restaurants:

```sh
 mongoimport --drop -c people --uri 'mongodb+srv://M201STUDENT:M201PASSWORD@m201.w1fqb.mongodb.net/m201' people.json
 ```

Validation:

```sh
use m201
db.people.count({ "email" : {"$exists": 1} })
```

Answer: 50474

## Chapter 2

### Introduction

- Indexes try to solve slow queries.
- Collection Scan. Linear Running time.
- Indexes have O(1) time.
- B-Tree
- Index overhead. Decrease our write speed.
- Too many unneccesary indexes, loss on writing.

### Quiz Introduction to Indexes

Problem:

Which of the following statements regarding indexes are true?

Check all answers that apply:

- [X] Indexes are used to increase the speed of our queries.
- [X] The _id field is automatically indexed on all collections.
- [X] Indexes reduce the number of documents MongoDB needs to examine to satisfy a query.
- [X] Indexes can decrease write, update, and delete performance.

### How Data is Stored on Disk

- Storage engines: MMAPv1, Wired Tiger, Other.
- Indexes on collections over fields present in documents.
- Each database contains a file for the collection and for the indexes. Stored as .wt
- Journal. All writes are atomic.

### Single field indexes

- Simplest indexes in mongoDB.
- Captures the key of a single field.
- We can append .explain('executionStats') to a find
- COLLSCAN not good...
- We can use dot nation to create index in a subfield
- we can store the explainable object

### QUIZ SINGLE FIELD INDEXES

Which of the following queries can use an index on the zip field?
Attempts Remaining:Correct Answer

Check all answers that apply:

- [X] db.addresses.find( { zip : 55555 } )
- [ ] db.addresses.find( { city : "Newark", state : "NJ" } )
- [ ] db.addresses.find()

### UNDERSTANDING EXPLAIN

- exp = db.people.explain()
- exp.find({...})
- explain('queryPlanner') => Default
- explain('executionStats') => Winning plan
- explain('allPlansExecution') => Winning plan + rejected plans
- Rejected plans get into the field when there are more indexes.
- SORT_KEY_GENERATOR = in memory sort

### QUIZ UNDERSTANDING EXPLAIN

Understanding Explain
Problem:

If you observe the following lines in the output of explain('executionStats'), what can you deduce?

```js
"executionStats" : {
  "executionSuccess" : true,
  "nReturned" : 23217,
  "executionTimeMillis" : 91,
  "totalKeysExamined" : 23217,
  "totalDocsExamined" : 23217,
  "executionStages" : {
    "stage" : "SORT",
    "nReturned" : 23217,
    "executionTimeMillisEstimate" : 26,
    "works" : 46437,
    "advanced" : 23217,
    "needTime" : 23219,
    "needYield" : 0,
    "saveState" : 363,
    "restoreState" : 363,
    "isEOF" : 1,
    "sortPattern" : {
      "stars" : 1
    },
    "memUsage" : 32522511,
    "memLimit" : 33554432,
```

Check all answers that apply:

- [X] The index selected for the query was not useful for the sort part of the query
- [X] An in-memory sort was performed
- [X] The system came very close to throwing an exception instead of returning results

### EXPLAIN IN SHARDED CLUSTER

### SHARDING

- mlaunch init --single --sharded 2
- use m201
- sh.enableSharding('m201')
- sh.shardCollection('m201.people', {_id: 1})
- mongoimport the people collection
- db.people.getShardDistribution()
- Mongos will send the query to each shard. Each shard will evaluate the query, select the plan, and then all the info will be aggregated in the mongos.
- SHARD_MERGE => Last stage.
- shards array in the explain method.

### QUIZ Understanding Explain for Sharded Clusters

Problem:

With the output of an explain command, what can you deduce?

Check all answers that apply:

- [X] The index used by the chosen plan
- [X] If a sort was performed by walking the index or done in memory
- [ ] All the available indexes for this collection
- [X] All the different stages the query needs to go through with details about the time it takes, the number of documents processed and returned to the next stage in the pipeline
- [ ] The estimation of the cardinalities of the distribution of the values

### SORTING WITH INDEXES

- Sort => Ram. Sorting algorithm. When 32mb of memory is used, the server will abort.
- Use indexes to sort.

### QUIZ Sorting with Indexes

Problem:

Given the following schema for the products collection:

```sh
{
  "_id": ObjectId,
  "product_name": String,
  "product_id": String
}
```

And the following index on the products collection:

```sh
{ product_id: 1 }
```

Which of the following queries will use the given index to perform the sorting of the returned documents?

Check all answers that apply:

- [X] db.products.find({ product_name: 'Soap' }).sort({ product_id: 1 })
- [ ] db.products.find({ product_name: 'Wax' }).sort({ product_name: 1 })
- [x] db.products.find({}).sort({ product_id: 1 })
- [x] db.products.find({}).sort({ product_id: -1 })
- [x] db.products.find({ product_id: '57d7a1' }).sort({ product_id: -1 })

### COMPOUND INDEXES

- Index on 2 or more fields.
- Index keys as ordered lists.
- Order matters

### QUERYING COMPOUND INDEXES

- Order is important.

### When you can sort with Indexes

- Compound indexes can be used to sort.
- When index prefix is kept.

### QUIZ When you can sort with Indexes

Problem:

Which of the following statements are true?
Attempts Remaining:∞Unlimited Attempts

Check all answers that apply:

- [X] Index prefixes can be used in query predicates to increase index utilization.
- [X] Index prefixes can be used in sort predicates to prevent in-memory sorts.
- [X] We can invert the keys of an index in our sort predicate to utilize an index by walking it backwards.
- [ ] It's impossible to have a sorted query use an index for both sorting and filtering.

### Multi Key indexes

- Indexing an array.
- Each element in the array will create a new key.
- We can't create a compound multikey index.
- Be carefull of not creating on arrays that are large
- Multikey indexes don't support covered queries!!
- It will error if you try to create a compound index on 2 multikeys indexes

### QUIZ Multikey Indexes

Problem:

Given the following index:

```js
{ name: 1, emails: 1 }
```

When the following document is inserted, how many index entries will be created?

```js
{
  "name": "Beatrice McBride",
  "age": 26,
  "emails": [
      "puovvid@wamaw.kp",
      "todujufo@zoehed.mh",
      "fakmir@cebfirvot.pm"
  ]
}
```

Check all answers that apply:

- [ ] 1
- [ ] 2
- [X] 3
- [ ] 4

### Partial indexes

- To index a subset of documents.
- Reduces performance costs

```js
db.restaurants.createIndex(
    {"address.city":1, 'cuisine': 1},
    {partialFilterExpression: {"stars": {"$gte": 4}}}
    )
```

- Sparse index is a case of partial indexes. sparse: true ignores the document if it doesn't have the index key instead of creating an index with null
- Recommended to user partialFilterExpression because it is more expressive.
- Shard key indexes cannot be partial indexes
- _id cannot be partial indexes
- When using partial indexes, include them in the predicate! otherwise a COLLSCAN will still need to be performed

### QUIZ Partial Indexes

Problem:

Which of the following is true regarding partial indexes?
Attempts Remaining:∞Unlimited Attempts

Check all answers that apply:

- [X] Partial indexes represent a superset of the functionality of sparse indexes.
- [X] Partial indexes can be used to reduce the number of keys in an index.
- [ ] Partial indexes don't support a uniqueness constraint.
- [X] Partial indexes support compound indexes.

### TEXT INDEXES

- By default, are case insensitive.

```js
db.example.find(
    {$text: {$search: "MongoDB Best" }},
    {score: {$meta: "textScore"}})
    .sort(
        {$meta: "textScore"}
    )
```

### Quiz Text Indexes

Problem:

Which other type of index is mostly closely related to text indexes?
Attempts Remaining:∞Unlimited Attempts

- [ ] Single-key indexes
- [ ] Compound indexes
- [X] Multi-key indexes
- [ ] Partial indexes

### COLLATIONS

- Language specific rules
  
```js
  {
      local
      caseLevel
      caseFirst
      strength
      numericOrdering
      alternate
      maxVariable
      backwards
  }
```

Collations can be defined at 7 different levels.

- Collection level
- Index level (the query must match the collation of the index)
- For using the index collation, we need to specify it in the query
- Otherwise it will use the collection's.

```js
db.something.find({}).collation({locale:it})
```

- Marginal performance impact
- We can also make a collection case insenstive if we specify strength=1 in the collation

### Collations

Problem:

Which of the following statements are true regarding collations on indexes?
Attempts Remaining:∞Unlimited Attempts

Check all answers that apply:

- [ ] MongoDB only allows collations to be defined at collection level
- [X] Collations allow the creation of case insensitive indexes
- [ ] Creating an index with a different collation from the base collection implies overriding the base collection collation.
- [X] We can define specific collations in an index

### Wildcard Indexes

- Indexes need to be mantained.
- Indexes all fields!
- db.coll.createindex({"$**": 1})
- Useful for unpredictable worloads

### QUIZ WILDCARD INDEXES

Using the wildcardProjection flag with Wildcard Indexes, we can:
Attempts Remaining:∞Unlimited Attempts

Check all answers that apply:

- [X] include a set of fields in the Wildcard Index.
- [X] exclude a set of fields from the Wildcard Index.
- [ ] specify a set of fields in the Wildcard Index to project with $project.

### WILDCARD INDEXES USES

- Arbitrary queries
- Attribute pattern!

### QUIZ  Wildcard Index Use Cases

Which of the following are good reasons to use a Wildcard Index?
Attempts Remaining:Correct Answer

Check all answers that apply:

- [ ] A collection has documents with many different fields.
- [X] An application consistently queries against document fields that use the Attribute Pattern.
- [X] The query pattern on documents of a collection is unpredictable.

### Lab 2.1: Using Indexes to Sort

```js
{ "first_name": 1, "address.state": -1, "address.city": -1, "ssn": 1 }
```

Check all answers that apply:

- [X] db.people.find({ "address.state": "South Dakota", "first_name": "Jessica" }).sort({ "address.city": -1 })
- [ ] db.people.find({ "first_name": { $gt: "J" } }).sort({ "address.city": -1 })
- [X] db.people.find({ "first_name": "Jessica" }).sort({ "address.state": 1, "address.city": 1 })
- [X] db.people.find({ "address.city": "West Cindy" }).sort({ "address.city": -1 })
- [X] db.people.find({ "first_name": "Jessica", "address.state": { $lt: "S"} }).sort({ "address.state": 1 })

### LAB 2.2

In this lab you're going to examine several example queries and determine which compound index will best service them.

> db.people.find({
    "address.state": "Nebraska",
    "last_name": /^G/,
    "job": "Police officer"
  })
> db.people.find({
    "job": /^P/,
    "first_name": /^C/,
    "address.state": "Indiana"
  }).sort({ "last_name": 1 })
> db.people.find({
    "address.state": "Connecticut",
    "birthday": {
      "$gte": ISODate("2010-01-01T00:00:00.000Z"),
      "$lt": ISODate("2011-01-01T00:00:00.000Z")
    }
  })

If you had to build one index on the people collection, which of the following indexes would best service all 3 queries?

Attempts Remaining:Incorrect Answer

Choose the best answer:

- [ ] { "job": 1, "address.state": 1, "first_name": 1 }
- [X] { "address.state": 1, "last_name": 1, "job": 1 }
- [ ] { "job": 1, "address.state": 1, "last_name": 1 }
- [ ] { "job": 1, "address.state": 1 }
- [ ] { "address.state": 1, "job": 1 }
- [ ] { "address.state": 1, "job": 1, "first_name": 1 }

## CHAPTER 3

### Building Indexes

- Hybrid index build
- db.coll.createIndex => No locking from 4.2
- No background or foreground index creation, now is automatic

### QUIZ: Building Indexes

Problem:

Which of the following are true of index build operations?

Choose the best answer:

- [ ] Hybrid index builds block all reads and writes to the collection being indexed.
- [ ] Hybrid index builds block all reads and writes to the database that holds the collection being indexed.
- [ ] Background index builds are now faster.
- [X] MongoDB now only has one index buid type available
- [ ] Foreground index builds are now non-blocking.

### QUERY PLANS

- Stages to perform the query.
- IXSCAN - FETCH - SORT - COLLSCAN
- Candidate indexes: the indexes that might be used for the query. Not all the indexes are going to be picked as candidates.
- Each candidate indexes, will have generated query plans. Each query plan will be executed over a short period of time. And the mongo query planner will choose which one perform better.
- Mongodb caches the best query plan for that query
- Cache is cleaned on restart, index rebuild, index drop.

### QUIZ Query Plans

Problem:

Which of the following is/are true concerning query plans?
Attempts Remaining:∞Unlimited Attempts

Check all answers that apply:

- [ ] MongoDB's query optimizer is statistically based, where collection heuristics are used to determine which plan wins.
- [X] Query plans are cached so that plans do not need to be generated and compared against each other every time a query is executed.
- [ ] When query plans are generated, for a given query, every index generates at least one query plan.
- [ ] If an index can't be used, then there is no query plan for that query.

### Force indexes with hing

- When having several indexes, to be explicit.
- attach .hint (it is a cursor method)
- db.col.find({}).hint({name:1, }) // pass the shape or the string of the name of the index.
- Use with caution. MongoDB usually does a good job
- When there are a LOT of index.
- Better to remove unused index.
- Overrides mongodb default selection.
  
### Forcing Indexes with Hint

Problem:

What is the method that forces MongoDB to use a particular index?
Attempts Remaining:∞Unlimited Attempts

Check all answers that apply:

- [ ] force()
- [ ] suggest()
- [X] hint()
- [ ] index()

### Resource allocation for indexes part 1, 2, 3

- Determine index size. Mongodb compass. You can see it on the indexes tab or db.stats()
- Resource allocation: disk and memory.
- Disk is usually not a problem. Not enough space? index won't be crated (you have bigger issues)
- RAM should be enought to cover all of our indexes. Or FIFO.
- Edge cases
- db.coll.stats({indexDetails: true})
- Always have enough memory to allocate your indexes.

### QUIZ Resource Allocation for Indexes Part 3

Which of the following statements apply to index resource allocation?
Attempts Remaining:Correct Answer

Check all answers that apply:

- [X] For the fastest processing, we should ensure that our indexes fit entirely in RAM
- [ ] Index information does not need to completely allocated in RAM since MongoDB only uses the right-end-side to the index b-tree, regardless of the queries that use index.
- [X] Indexes are not required to be entirely placed in RAM, however performance will be affected by constant disk access to retrieve index information.

### Benchmarking

Problem:

What type of strategy and tools should we be using to performance benchmark a MongoDB installation

Check all answers that apply:

- [X] Publicly available tools, including correct database variations
- [X] Mongo shell to test read performance
- [X] Test transfer ratio using mongodump

### LAB 3.1

- The right answer is:

```js
{ "address.state": 1, "stars": 1, "name": 1 }
```

Since stars is not a specific value, but a RANGE, mongodb has no way to ensure the proper order. So an in memory sort happens even tough name is indexed afterwards the stars.

## CHAPTER 4

### OPTIMIZING CRUD OPERATIONS

- Index selectiviy
- Equality, sort, range
- Performance tradeoffs.

This lecture is one of the most importants of the chapter, and also explains lab 3.1

- When you have a compound index, the sort will only be able to be used if the query is querying for equality. If not, the sort won't be able to be used, and an in memory sort will happen.
- For that reason, sometimes it is preferable to have as second index the sort one, rather than having a 3 key multi index where the sort is on the third.
- In that case, if the second index uses a range, the third index won't be able to be used for sorting.

### QUIZ Optimizing your CRUD Operations

Problem:

When building indexes to service your queries, which of the following is the general rule of thumb you should keep when ordering your index keys?

Note, use the following definitions to for this question:

equality: indexed fields on which our queries will do equality matching
range: indexed fields on which our queries will have a range condition
sort: indexed fields on which our queries will sort on
Attempts Remaining:∞Unlimited Attempts

Check all answers that apply:

- [ ] equality, range, sort
- [ ] sort, range, equality
- [ ] sort, equality, range
- [X] equality, sort, range
- [ ] range, sort, equality
- [ ] range, equality, sort

### COVERED QUERIES

- Very performant
- Satisfied only by using the index keys.
- 0 Documents examined.
- When filtering in project, beware that using {fieldA: 0, _id: 0 } will perform document examination. Mongodb has no way to know if the documents contain any other fields.
- So, better to use {fieldA: 1, fieldB: 1, _id: 0}

**We can't run covered queries if:**

- Any of the indexed fields are arrays
- Any of the indexed fields are embedded documents
- When run agianst a mongos if the index does not contain the shard key

### QUIZ Covered Queries

Problem:
Given the following indexes:

```js
{ _id: 1 }
{ name: 1, dob: 1 }
{ hair: 1, name: 1 }
```

Which of the following queries could be covered by one of the given indexes?

Check all answers that apply:

- [ ] ```db.example.find({_id : 1117008 }, {_id : 0, name : 1, dob : 1 } )```
- [ ] ```db.example.find({name : { $in : ["Alfred", "Bruce" ] } }, { name : 1, hair : 1 } )```
- [ ] ```db.example.find({name : { $in : ["Bart", "Homer" ] } }, {_id : 0, hair : 1, name : 1} )```
- [X] ```db.example.find({name : { $in : ["Bart", "Homer" ] } }, {_id : 0, dob : 1, name : 1})```

### LECTURE: REGEX PERFORMANCE

- Utilizing indexes on regex conditions.
- Just add a ^ at the beginning in the regex.. that will do the job.

### QUIZ REGEX

Problem:
Given the following index:

```js
db.products.createIndex({ productName: 1 })
// And the following query:
db.products.find({ productName: /^Craftsman/ })
```

Which of the following are true?

Check all answers that apply:

- [ ] The query will need to do a collection scan.
- [X] The query will do an index scan.
- [ ] The query will likely need to look at all index keys.
- [ ] The query would match a productName of "Screwdriver - Craftsman Brand"

### AGGREGATION PERFORMANCE

- Index usage
- Memory Constrains
- "Realtime" processing queries => Data for apps. Performance most important.
- Batch processing queries => For analytics. Performance less important.
- Once a query encounters a stage that is not able to use indexes, the following stages won't use the index either.
- we can attach {explain: true} afterwards: db.coll.aggregate([], {explain: true})
- Operators that use indexes, try to move them to the top (match, sort, limit)
- Memory constraints: 16mb per document at the return time.
- use $limit and $project
- 100mb ram per stage.
- Last resource: db.col.aggregate([], {allowDiskUse: true}) => Used more in batch queries

### QUIZ Aggregation Performance

Problem:

With regards to aggregation performance, which of the following are true?

Check all answers that apply:

- [ ] You can increase index usage by moving $match stages to the end of your pipeline
- [ ] Passing allowDiskUsage to your aggregation queries will seriously increase their performance
- [X] When $limit and $sort are close together a very performant top-k sort can be performed
- [X] Transforming data in a pipeline stage prevents us from using indexes in the stages that follow

### LAB 4.1

Answer: ```{city: 1, lastName: 1, firstName: 1, accountBalance: 1}```

### LAB 4.2

Answer: ```{cuisine: 1}```

## CHAPTER 5

### WORKING WITH DISTRIBUTED SYSTEMS

- Distributed systems: shards, cluster
- Consider latency
- Data is spread across diferent nods
- Read implications
- Write implications
- Replica set: High avaliability. Avoids downtimes in system failures
- Sharded cluster: for horizontal scalability. Contain config servers.
- Sharded cluster: will have mongos daemons responsible for routing the client requests to the designated nodes.
- Config server: mapping, general configurations.
- Shard nodes: replica set.
- Sharding: when you reached your limit for vertical scaling.

Client => Mongos (several)  -> config servers
                            -> sharded nodes

- Collocating the mongos in the same server as the application will reduce the latency.
- Two types of reads in sharded clusters:
  - Scattered gathered, where we ping all nodes of our shard cluster for the information
  - Routed queries: To a specific shard. Obviously performs better. Uses the sharded key. Mongos can pinpoint the cluster
  - Sort happens in the primary shard
  - Once the sort merge is performed in the main shard, it comes back to the mongos and then to the client.

### QUIZ Performance Considerations in Distributed Systems Part 2

Problem:

From a performance standpoint, when working with a distributed database it's important to consider...

Check all answers that apply:

- [X] Routed Queries
- [X] Latency
- [ ] Reading from secondaries only

### INCREASING WRITE PERFORMANCE WITH SHARDING

- Chunk limit: 64 mb
- Inclusive lower band, exlucsive upper band
- Cardinality: Number of distinct values. Determinates the max number of chunks.
- Compound shard keys increase cardinality.
- Jumbo chunks: upper and lower bands are the same (due to low cardinality)
- Rate of change: avoid monotonically increasing / decreasing values. Solution: hashing the key. _id will fall in this category.
- Bulk write: ordered-> Slower (more latency)
- Bulk write: unordered-> Faster (writing in parallel)

### QUIZ Increasing Write Performance with Sharding Part 2

Problem:

Which of the following is/are true?

Check all answers that apply:

- [ ] Vertical scaling is generally cheaper than horizontal scaling.
- [X] Picking a good shard key is one of the most important parts of sharding.
- [ ] Ordered bulk operations are faster than unordered.

### READING FROM SECONDARIES

- Read preference default in repllica set: primary node
- Primary, primaryPreferred, secondary, secondaryPreferred, nearest
- Writes can only be routed to the primary.
- Reading from the secondary has a higher risk of stale data.
- Reading to secondaries: good for analytics queries, local reads.
- Reading from secondaries doesn't provide extra throughput on the primary, since at some point the data is going to be replicated to the secondary.

### QUIZ Reading from Secondaries

Problem:

When should you ever read from secondaries?

Check all answers that apply:

- [X] To provide reads with lower latency.
- [ ] To increase performance in a write-heavy system.
- [X] When doing ad-hoc queries and analytic jobs.

### Replica sets with different idexes

- Not very common
- Only usefull for some cases: some analytics, text search, reporting on delayed consistency data.
- Specific indexes for secondary nodes.
- These nodes should never become primary!! (hide them, or give them a lower priority)

- When creating an index in the primary node, also is created in the secondaries.
- To enable reads from secondaries: db.setSlaveOk() when connected to the secondary.
- Once you create something on the primary, it will be reflected in the secondary nodes
- When connected to the secondary, We can create more indexes.
- rs.status()
- The index will only be utilized if it is running on the designated secondary node.
- To create this index, we need to shutdown the secondary, restart it on standalone and then create it. Then put it down, and restart it in replica set.

### QUIZ Replica Sets with Differing Indexes Part 3

Problem:

Which of the following conditions apply when creating indexes on secondaries?

Check all answers that apply:

- [X] A secondary should never be allowed to become primary
- [ ] These indexes can only be set on secondary nodes
- [ ] We can create specific indexes on secondaries, even if they are not running in standalone mode

### AGGREGATION PIPELINES ON SHARDED CLUSTERS

- $out, $facet, $lookup, $graphlookup => the primary will merge
- When performing a query, which will ping several shards, each shard will calculate it internal 'part' of the response, and then the merge will happen in a random shard.

### QUIZ Aggregation Pipeline on a Sharded Cluster

Problem:

What operators will cause a merge stage on the primary shard for a database?

Check all answers that apply:

- [x] $out
- [ ] $group
- [X] $lookup