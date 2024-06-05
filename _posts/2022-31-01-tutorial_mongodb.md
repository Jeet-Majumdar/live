---
layout: post
title: MongoDB: A Tutorial
categories: [Tech]
---


## Chapter - 1: Basics

```docker-compose -f stack.yml up```

```sudo docker exec -it docker_mongo_1 bash```

``` mongo -u admin -p password ```

```db.help()```

``` db.version()```

- mongo functions are written in javascript. so to get the function definition, just type db.help instead of db.help() or, db.version instead of db.version()

```use learn```

```db.collection_name.help()```

- A selector is a JSON object, the simplest of which is {} which matches all documents. If we wanted to find all 'item' in a collection, we could use {attribute: 'item'}.

  - {field: value} is used to find any documents where field is equal to value.

  - {field1: value1, field2: value2} is how we do an and statement.

- individual document is currently limited to 16 megabytes in size

- The special $lt , $lte , $gt , $gte and $ne are used for less than, less than or equal, greater than, greater than or equal and not equal operations:
  - {field1: {$gt: 'value1'}, field2: {$gte: 'value2'}}

- MongoDB supports arrays as first class objects

- $exists operator is used for matching the presence or absence of a field:

  ```db.unicorns.find({vampires: {$exists: false }})```

- ‘$in’ operator is used for matching one of several values that we pass as an array, for example:

  ```db.unicorns.find({loves: {$in:['apple','orange']}})```

- If we want to OR rather than AND several conditions on different fields, we use the $or operator and assign to it an array of selectors we want or’d:

  ```db.unicorns.find({gender: 'f',  $or: [{loves: 'apple'}, {weight: {$lt: 500}}]})```

   The above will return all female unicorns which either love apples or weigh less than 500 pounds.

- The ObjectId which MongoDB generated for our _id field can be selected like so:

  ```db.unicorns.find({_id: ObjectId("TheObjectId")})```

----------------------------------

## Chapter - 2: Updating

- update takes two parameters: 

  1. the selector (where) to use
  2. what updates to apply to fields

  ```db.unicorns.update({name: 'Roooooodles'}, {weight: 590})```

  The above command will replace the entire document found with {name: 'Roooooodles'}, and replace it with a new document{weight: 590}



- When you want to change the value of one, or a few fields, you
  must use MongoDB’s $set operator:

  ```db.unicorns.update({name: 'Roooooodles'},  {$set: {weight: 590}})```

- Update Operators---

  All update operators work on fields - so your entire document won’t be wiped out.

  - $inc operator is used to increment a field by a certain
    positive or negative amount:

    ```db.unicorns.update({name: 'Pilot'}, {$inc: {vampires: -2}})```

  - $push operator is used to add a value to a field:

    ```db.unicorns.update({name: 'Aurora'}, {$push: {loves: 'sugar'}})```

- Upserts:

  - upsert updates the document if found or inserts it if not:

    ```db.hits.update({page: 'unicorns'}, {$inc: {hits: 1}}, {upsert: true }); db.hits.find();```



- Multiple Updates:

  - by default, 'update' updates a single document. To update multiple documents:

    ```
    	db.unicorns.update({},
    	{$set: {vaccinated: true }},
    	{multi: true });
    	db.unicorns.find({vaccinated: true });
    ```

------------------------------------

## Chapter 3 - Mastering Find

- Field Selection:

  By default, the _id field is always returned.
  Thus, ```db.unicorns.find({}, {name: 1}); ```will return all the documents with name=1 along with their _id field.

  We can explicitly exclude it by specifying {name:1, _id: 0}  i.e:

  ```db.unicorns.find({}, {name: 1, _id: 0});```



- Ordering:

  - We specify the fields we want to sort on as a JSON document, using 1 for ascending and -1 for descending

    ```
    //heaviest unicorns first
    db.unicorns.find().sort({weight: -1})
    
    //by unicorn name then vampire kills:
    db.unicorns.find().sort({name: 1,
    vampires: -1})
    ```

- MongoDB limits the size of your sort without an index. 
  That is, if you try to sort a very large result set which can’t use an index, you’ll get an error. 



- Paging:

  Paging results can be accomplished via the limit and skip cursor methods. To get the second and third heaviest unicorn, we could do:

  ```
  db.unicorns.find()
  .sort({weight: -1})
  .limit(2)
  .skip(1)
  ```

  

- Count:

  ```db.unicorns.count({vampires: {$gt: 50}})```

  or,

  ```
  db.unicorns.find({vampires: {$gt: 50}})
  .count()
  ```

----------------------------------

## Chapter 4 - Data Modeling



- MongoDB’s lack of joins

  We have to do joins ourselves within our application’s code

- Essentially we need to issue a second query to find the relevant data in a second collection.

  - Setting our data up isn’t any different than declaring a foreign key in a relational database.

  - _id can be any unique value.

  ```
  db.employees.insert({_id: ObjectId(
  "4d85c7039ab0fd70a117d730"),
  name: 'Leto'})
  
  db.employees.insert({_id: ObjectId(
  "4d85c7039ab0fd70a117d731"),
  name: 'Duncan',
  manager: ObjectId(
  "4d85c7039ab0fd70a117d730")});
  db.employees.insert({_id: ObjectId(
  "4d85c7039ab0fd70a117d732"),
  name: 'Moneo',
  manager: ObjectId(
  "4d85c7039ab0fd70a117d730")});
  ```

  

- To find all of Leto’s employees, one simply executes:

  ```
  db.employees.find({manager: ObjectId(
  "4d85c7039ab0fd70a117d730")})
  ```

  

- If an employee could have two managers, we could simply store these in an array:

  ```
  db.employees.insert({_id: ObjectId(
  "4d85c7039ab0fd70a117d733"),
  name: 'Siona',
  manager: [ObjectId(
  "4d85c7039ab0fd70a117d730"),
  ObjectId(
  "4d85c7039ab0fd70a117d732")] })
  
  db.employees.find({manager: ObjectId( "4d85c7039ab0fd70a117d730")}, {_id: 0})
  ```

  

- Arrays and Embedded Documents:

  - MongoDB also supports embedded documents.

    ```
    db.employees.insert({_id: ObjectId(
    "4d85c7039ab0fd70a117d734"),
    name: 'Ghanima',
    family: {mother: 'Chani',
    father: 'Paul',
    brother: ObjectId(
    "4d85c7039ab0fd70a117d730")}})
    ```

  

  - embedded documents can be queried using a dot-notation:

    ```
    db.employees.find({
    'family.mother': 'Chani'})
    ```

  

  - we can even embed arrays of documents:

    ```
    db.employees.insert({_id: ObjectId(
    "4d85c7039ab0fd70a117d735"),
    name: 'Chani',
    family: [ {relation:'mother',name: 'Chani'},
    {relation:'father',name: 'Paul'},
    {relation:'brother', name: 'Duncan'}]})
    
    db.employees.find({ 'family.name': 'Chani'})
    
    
    ```

    

- Denormalization:

  The traditional way to associate a specific user with a post is via a userid column within posts.
  With such a model, you can’t display posts without retrieving (joining to) users.
  A possible alternative is simply to store the name as well as the userid with each post.
  You could even do so with an embedded document, like user: {id: ObjectId('Something'), name: 'Leto'} .
  Yes, if you let users change their name, you may have to update each document (which is one multi-update).



- Which Should You Choose?

  Arrays of ids can be a useful strategy when dealing with one-to-many or many-to-many scenarios.
  Embedded documents are frequently leveraged, but mostly for smaller pieces of data which we want to always pull with the parent document.

  A real world example may be to store an addresses documents with each user, something like:

  ```
  db.users.insert({name: 'leto',
  email: 'leto@dune.gov',
  addresses: [{street: "229 W. 43rd St",
  city: "New York", state:"NY",zip:"10036"},
  {street: "555 University",
  city: "Palo Alto", state:"CA",zip:"94107"}]})
  ```

  

- Few or Many Collections:

  The example that frequently comes up is a blog. 
  Should you have a posts collection and a comments collection, or should each post have an array of comments embedded within it? 
  Setting aside the 16MB document size limit for the time being (all of Hamlet is less than 200KB, so just how popular is your blog?), most developers should prefer to separate things out.
  It’s simply cleaner, gives you better performance and more explicit.
  MongoDB’s flexible schema allows you to combine the two approaches by keeping comments in their own collection but embedding a few comments (maybe the first few) in the blog post to be able to display them with the post.
  This follows the principle of keeping together data that you want to get back in one query.



--------------------------------------


## Chapter 5 - When To Use MongoDB

Real benefit of dynamic schema is the lack of setup and the reduced friction with OOP.
Ruby developers would see MongoDB as an incremental improvement, whereas C# or Java developers would see a fundamental shift in how they interact with their data.

- You want to save an object? Serialize it to JSON.
  There is no property mapping or type mapping.

- **Writes**

  MongoDB can fit a specialized role is in logging.
  Two aspects of MongoDB which make writes quite fast.
  First, you have an option to send a write command and have it return immediately without waiting for the write to be acknowledged.
  Secondly, you can control the write behavior with respect to data durability.
  These settings, in addition to specifying how many servers should get your data before being considered successful, are configurable per-write, giving you a great level of control over write performance and data durability.

- log data is one of those data sets which can often take advantage of schemaless collections. 
  So far, all of the implicitly created collections we’ve created are just normal collections.

- MongoDB has something called a capped collection.

- We can create a capped collection by using the ```db.createCollection``` command and flagging it as capped:

  ```
  //limit our capped collection to 1 megabyte
  db.createCollection('logs', {capped: true ,
  size: 1048576})
  ```

  

  When our capped collection reaches its 1MB limit, old documents are automatically purged.

  A limit on the number of documents, rather than the size, can be set using max .


  Capped collections have some interesting properties:

  - you can update a document but it can’t change in size.
  - The insertion order is preserved, so you don’t need to add an extra index to get proper time-based sorting. 
  - You can “tail” a capped collection the way you tail a file in Unix via tail -f <filename> which allows you to get new data as it arrives, without having to re-query it.

  

  If you want to “expire” your data based on time rather than overall collection size, you can use TTL Indexes where TTL stands for “time-to-live”.



- **Durability:**

  Since version 2.0 MongoDB enables journaling by default, which allows fast recovery of the server in case of a crash or abrupt power loss. 

- **Full Text Search:**

  True full text search capability is a recent addition to MongoDB. It supports fifteen languages with stemming and stop words. 
  With MongoDB’s support for arrays and full text search you will only need to look to other solutions if you need a more powerful and full-featured full text search engine.



- **Transactions:**

  MongoDB doesn’t have transactions.
  It has two alternatives, one which is great but with limited use, and the other that is cumbersome but flexible.

  The first is its many atomic update operations. These are great, so long as they actually address your problem. We already saw some of the simpler ones, like $inc and $set . There are also commands like findAndModify which can update or delete a document and return it atomically.

  The second, when atomic operations aren’t enough, is to fall back to a two-phase commit. A two-phase commit is to transactions what manual dereferencing is to joins. It’s a storage-agnostic solution that you do in code. Two-phase commits are actually quite popular in the relational world as a way to implement transactions across multiple databases. The MongoDB website has an example illustrating the most typical example (a transfer of funds). The general idea is that you store the state of the transaction within the actual document being updated atomically and go through the init-pending-commit/rollback steps manually. 



- **Data Processing:**

  As of 2.2 it has added a powerful feature called aggregation framework or pipeline, so you’ll only need to use MapReduce in rare cases where you need complex functions for aggregations that are not yet supported in the pipeline. 



- **Geospatial:**

  A particularly powerful feature of MongoDB is its support for geospatial indexes. This allows you to store either geoJSON or x and y coordinates within documents and then find documents that are $near a set of coordinates or $within a box or circle. This is a feature best explained via some visual aids, so I invite you to try the 5 minute geospatial interactive tutorial, if you want to learn more. 


-------------------------------


## Chapter 6 - Aggregating Data

- Aggregation Pipeline:

  Aggregation pipeline gives you a way to transform and combine documents in your collection.

  You do it by passing the documents through a pipeline that’s somewhat analogous to the Unix “pipe” where you send output from one command to another to a third, etc. 

  - stages in aggregation framework −

    $project, $match, $group, $sort, $skip, $limit, $unwind

    

  - $group - This is the exact analog of GROUP BY in SQL where we create a new document 
  
    group operators: $sum, $avg, $min, $max, $push, $addToSet, $first, $last, 
  
    '$' before a field name indicates that the value of this field from incoming document will be substituted.
  
    ```
    db.unicorns.aggregate([{$group:{_id:'$gender',
    total: {$sum:1}}}])
    ```
  
    
  
  we create a new document with _id field indicating what field we are grouping by (here it’s gender ) and other fields usually getting assigned results of some aggregation, in this case we $sum 1 for each document that matches a particular gender.
  
  Most common one to use before (and frequently after) $group would be $match - this is exactly like the find method and it allows us to aggregate only a matching subset of our documents, or to exclude some documents from our result.
  
      db.unicorns.aggregate([{$match: {weight:{$lt:600}}},
      {$group: {_id:'$gender',
      total:{$sum:1},
      avgVamp:{$avg:'$vampires'}}},
      {$sort:{avgVamp:-1}} ])
  We do need to be able to “flatten” sometimes to carry our group based on the array element:
  
  ```
  db.unicorns.aggregate([{$unwind:'$loves'},
  {$group: {_id:'$loves',
  total:{$sum:1},
  unicorns:{$addToSet:'$name'}}},
  {$sort:{total:-1}},
  {$limit:1} ])
  ```
  
  

- MapReduce:

  MapReduce is a two-step approach to data processing.
  First you map, and then you reduce.

  The mapping step transforms the inputted documents and emits a key=>value pair (the key and/or value can be complex).
  Then, key/value pairs are grouped by key, such that values for the same key end up in an array.
  The reduce gets a key and the array of values emitted for that key, and produces the final result.
  The map and reduce functions are written in JavaScript.

  mapReduce takes a map function, a reduce function, and an output directive.
  In our shell we can create and pass a JavaScript function.
  From most libraries you supply a string of your functions (which is a bit ugly).
  The third parameter sets additional options, for example we could filter, sort and limit the documents that we want analyzed.
  We can also supply a finalize method to be applied to the results after the reduce step.


--------------------------------------


## Chapter 7 - Performance and Tools

- **Indexes:**

  Indexes help improve query and sorting performance.

  Usually indexes are created for unique fields. You must not create index for every field.

  Indexes are created via ensureIndex :

  // where "name" is the field name
  ```db.unicorns.ensureIndex({name: 1});```


  And dropped via dropIndex :

  ```db.unicorns.dropIndex({name: 1});```


  A unique index can be created by supplying a second parameter and setting unique to true :

  ```
  db.unicorns.ensureIndex({name: 1},
  {unique: true });
  ```


  Indexes can be created on embedded fields (again, using the dot-notation) and on array fields. We can also create compound indexes: 

  ```
  db.unicorns.ensureIndex({name: 1,
  vampires: -1});
  ```

  direction of your index (1 for ascending, -1 for descending) doesn’t matter for a single key index, but it can make a difference for compound indexes when you are sorting on more than one indexed field.

  

- **Explain:**

  To see whether or not your queries are using an index, you can use the explain method on a cursor:

  ```db.unicorns.find().explain()```

  If we change our query to use an index, we’ll see that a BtreeCursor was used, as well as the index used to fulfill the request:

  ```db.unicorns.find({name: 'Pilot'}).explain()```



- **Sharding:**

  Sharding is an approach to scalability which partitions your data across multiple servers or clusters.
  A naive implementation might put all of the data for users with a name that starts with A-M on server 1 and the rest on server 2.
  MongoDB supports auto-sharding.
  MongoDB’s sharding capabilities far exceed such a simple algorithm.
  Sharding is the primary method for scaling MongoDB clusters.
  Combining replication with sharding is the perscribed approach to achieve scaling and high availability.



- **Profiler:**

  enable the MongoDB profiler by executing:
  ```db.setProfilingLevel(2);```

  With it enabled, we can run a command:

  ```db.unicorns.find({weight: {$gt: 600}});```

  And then examine the profiler:

  ```db.system.profile.find()```

  The output tells us what was run and when, how many documents were scanned, and how much data was returned.

  You disable the profiler by calling setProfilingLevel again but changing the parameter to 0 . Specifying 1 as the first parameter will profile queries that take more than 100 milliseconds. 100 milliseconds is the default threshold, you can specify a different minimum time, in milliseconds, with a second parameter:

  ```db.setProfilingLevel(1, 1000);```



- **Backups and Restore:**

  Within the MongoDB bin folder is a mongodump executable.
  Simply executing mongodump will connect to localhost and backup all of your databases to a dump subfolder.
  You can type mongodump --help to see additional options.

  Common options are --db DBNAME to back up a specific database and --collection COLLECTIONNAME to back up a specific collection.

  You can then use the mongorestore executable, located in the same bin folder, to restore a previously made backup.

  Again, the --db and --collection can be specified to restore a specific database and/or collection.

  For example, to back up our learn database to a backup folder, we’d execute (this is its own executable which you run in a command/terminal window, not within the mongo shell itself): 

  ```mongodump --db learn --out backup```


  To restore only the unicorns collection, we could then do:

  ```
  mongorestore --db learn --collection unicorns \
  backup/learn/unicorns.bson
  ```

  mongoexport and mongoimport are two other executables which can be used to export and import data from JSON or CSV.
  For example, we can get a JSON output by doing:

  ```mongoexport --db learn --collection unicorns```

  And a CSV output by doing:

  ```
  mongoexport --db learn \
  --collection unicorns \
  --csv --fields name,weight,vampires
  ```

  Note that mongoexport and mongoimport cannot always represent your data.

  **Only mongodump and mongorestore should ever be used for actual backups.**


--------------------------------

## Chapter 8 - GridFS


GridFS is the MongoDB specification for storing and retrieving large files such as images, audio files, video files, etc. It is kind of a file system to store files but its data is stored within MongoDB collections. GridFS has the capability to store files even greater than its document size limit of 16MB.

GridFS divides a file into chunks and stores each chunk of data in a separate document, each of maximum size 255k.

GridFS by default uses two collections fs.files and fs.chunks to store the file's metadata and the chunks. Each chunk is identified by its unique _id ObjectId field. The fs.files serves as a parent document. The files_id field in the fs.chunks document links the chunk to its parent.

Following is a sample document of fs.files collection −

```
{
   "filename": "test.txt",
   "chunkSize": NumberInt(261120),
   "uploadDate": ISODate("2014-04-13T11:32:33.557Z"),
   "md5": "7b762939321e146569b07f72c62cca4f",
   "length": NumberInt(646)
}
```

The document specifies the file name, chunk size, uploaded date, and length.

Following is a sample document of fs.chunks document −

```
{
   "files_id": ObjectId("534a75d19f54bfec8a2fe44b"),
   "n": NumberInt(0),
   "data": "Mongo Binary Data"
}
```

Adding Files to GridFS:

Now, we will store an mp3 file using GridFS using the put command. For this, we will use the mongofiles.exe utility present in the bin folder of the MongoDB installation folder.

Open your command prompt, navigate to the mongofiles.exe in the bin folder of MongoDB installation folder and type the following code −
mongofiles.exe -d gridfs put song.mp3

Here, gridfs is the name of the database in which the file will be stored. If the database is not present, MongoDB will automatically create a new document on the fly. Song.mp3 is the name of the file uploaded. To see the file's document in database, you can use find query −
db.fs.files.find()

The above command returned the following document −

```
{
   _id: ObjectId('534a811bf8b4aa4d33fdf94d'), 
   filename: "song.mp3", 
   chunkSize: 261120, 
   uploadDate: new Date(1397391643474), md5: "e4f53379c909f7bed2e9d631e15c1c41",
   length: 10401959 
}
```

We can also see all the chunks present in fs.chunks collection related to the stored file with the following code, using the document id returned in the previous query −
```db.fs.chunks.find({files_id:ObjectId('534a811bf8b4aa4d33fdf94d')})```


