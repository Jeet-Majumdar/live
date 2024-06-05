---
layout: post
title: Redis - A Tutorial
categories: [Tech]
---


## Getting Started

```
wget http://download.redis.io/releases/redis-3.0.3.tar.gz
tar xzf redis-3.0.3.tar.gz
cd redis-3.0.3
make
```



Next start the Redis console by either double clicking redis-cli (Windows) or running ```./redis-cli ``` (*nix/MacOSX).
This will connect to the locally-running server on the default port (6379).

**In Docker**

- ```docker pull redis```

- Run Redis as a container on port 6379 

  ```docker run –name redis -p 6379:6379 -d redis```

- Command to stop Redis

  ```docker stop redis docker rm redis```

- Connect to Redis:

  - ```
    docker exec -it redis redis-cli
    ```

- ### How to test the connection

  Redis-cli will take you to a prompt with the URL (or IP address) and the port number if it can successfully connect to Redis. You can then verify whether the server is responding to your queries with basic commands – 1. Ping – this returns PONG, 2. Set – set foo bar – this returns OK, 3. Get – get foo – this returns bar.

  ```
  $ redis-cli
  127.0.0.1:6379> ping
  PONG
  127.0.0.1:6379> set foo bar
  OK
  127.0.0.1:6379> get foo
  “bar”
  127.0.0.1:6379>
  ```

------------------------------- 

## Chapter 1 - The Basics



- Redis exposes **five** different data structures, <u>only one of which is a typical key-value structure</u>.

  every one of Redis’ five data structures has at least a key and a value.

- In Redis, databases are simply identified by a number with the default database being number 0. 

  If you want to change to a different database you can do so via the select command.

  ```select 1```

  your prompt should change to something like redis ```127.0.0.1:6379[1]>```

  If you want to switch back to the default database, just enter select ```0``` in the command line interface.	



**Commands, Keys and Values**

- a key might look like 

  ```users:leto```

  key to contain information about a user named  leto

  The colon doesn’t have any special meaning, as far as Redis is concerned, but using a separator is a common approach people use to organize their keys.

- Values represent the actual data associated with the key. They can be anything.

  Sometimes you’ll store strings, sometimes integers, sometimes you’ll store serialized objects (in JSON, XML or some other format)

  Redis treats values as a byte array and doesn’t care what they are.

​	Enter the following command:

​		```set users:leto '{"name": "leto", "planet": "dune", "likes": ["spice"]}'```

​	The set command takes two parameters: the key we are setting and the value we are setting it to.
​	how to retrieve this value?	

​		```get users:leto```



**Querying**

- As far as Redis is concerned, keys are everything and values are nothing.

  <u>Redis doesn’t allow you to query an object’s values.</u>

  

**Memory and Persistence**

- By default, Redis snapshots the database to disk based on how many keys have changed.

  By default, Redis will save the database every 60 seconds if 1000 or more keys have changed all the way to 15 minutes if 9 or less keys has changed.

- With respect to memory, Redis keeps all your data in memory. The obvious implication of this is the cost of running Redis: *RAM is still the most expensive part of server hardware.*

- But Redis’ performance tends to be measured in tens of thousands, or hundreds of thousands of operations per second .



### You can always erase all the values in your database by entering:   ```flushdb``` 

--------------------------------


## Chapter 2 - The Data Structures


- **Strings**

  ```
  set users:leto '{"name": leto, "planet": dune, "likes": ["spice"]}'
  ```

  

  - Redis lets you do some common operations.

    - ```strlen <key> ```  can be used to get the length of a key’s value

    - ```getrange <key> <start> <end>```   returns the specified range of a value

    - ```append <key> <value>```   appends the value to the existing value (or creates it if it doesn’t exist already)

    - ```
      incr 
      incrby 
      decr 
      decrby
      ```

      ```
      > incr stats:page:about
      (integer) 1
      > incr stats:page:about
      (integer) 2
      > incrby ratings:video:12333 5
      (integer) 5
      > incrby ratings:video:12333 3
      (integer) 8
      ```
  
- advanced example is the setbit and getbit commands.
  
      [Efficient use of these two commands](http://blog.getspool.com/2011/11/29/fast-easy-realtime-metrics-using-redis-bitmaps/) to answer the question “how many unique visitors did we have today”.  For 128 million users a laptop generates the answer in less than 50ms and takes only 16MB of memory.
  
  
​    
  
- **Hashes**

  - Hashes are like strings. The important difference is that they provide an extra level of indirection: a field

    The hash equivalents of set and get are:

    ```
    hset users:goku powerlevel 9000
    hget users:goku powerlevel
    ```

    

  - We can also set multiple fields at once, get multiple fields at once, get all fields and values, list all the fields or delete a specific field:

    ```
    hmset users:goku race saiyan age 737
    hmget users:goku race powerlevel
    hgetall users:goku
    hkeys users:goku
    hdel users:goku age
    ```

  - Rather than storing a user as a single serialized value, we could use a hash to get a more accurate representation.

    The benefit would be the ability to pull and update/delete specific pieces of data, without having to get or write the entire value.
    
    

- **Sets**

  - Sets are used to store unique values and provide a number of set-based operations, like unions.

  - Sets aren’t ordered but they provide efficient value-based operations.

    - A friend’s list is the classic example of using a set:

      ```
      sadd friends:leto ghanima paul chani jessica
      sadd friends:duncan paul jessica alia
      ```

    - Regardless of how many friends a user has, we can efficiently tell (O(1)) whether userX is a friend of userY or not:

      ```
      sismember friends:leto jessica
      sismember friends:leto vladimir
      ```

      

    - Furthermore we can see whether two or more people share the same friends:

      ```
      sinter friends:leto friends:duncan
      ```

    - and even store the result at a new key:

      ```
      sinterstore friends:leto_duncan friends:leto friends:duncan
      ```

    

- **Sorted Sets**

  - If hashes are like strings but with fields, then sorted sets are like sets but with a score.

  - The score provides sorting and ranking capabilities.

  - If we wanted a ranked list of friends, we might do:

    ```
    zadd friends:duncan 70 ghanima 95 paul 95 chani 75 jessica 1 vladimir
    ```

    

  - Want to find out how many friends duncan has with a score of 90 or over?

    ```
    zcount friends:duncan 90 100
    ```

    

  - How about figuring out chani ’s rank?

    ```
    zrevrank friends:duncan chani
    ```

    

  - We use zrevrank instead of zrank since Redis’ default sort is from low to high (but in this case we are ranking from high to low).

  - The most obvious use-case for sorted sets is a leaderboard system.

  - In reality though, anything you want sorted by some integer, and be able to efficiently manipulate based on that score, might be a good fit for a sorted set.



- **Lists**

  - Lists let you store and manipulate an array of values for a given key
  - You can add values to the list, get the first or last value and manipulate values at a given index. 

  - Lists maintain their order and have efficient index-based operations.

  - We could have a newusers list which tracks the newest registered users to our site:

    ```
    lpush newusers goku
    ltrim newusers 0 49
    ```

    

    First we push a new user at the front of the list, then we trim it so that it only contains the last 50 users

  - ltrim is an O(N) operation, where N is the number of values we are removing. 

  - If we wanted to get the details of the last 10 users, we’d do the following combination:

    ```
    lrange newusers 0 9
    ```

  - Other commands related to lists can be obtained at [Redis Labs](https://redislabs.com/ebook/part-1-getting-started/chapter-1-getting-to-know-redis/1-2-what-redis-data-structures-look-like/1-2-2-lists-in-redis/) and [Tutorials Point](https://www.tutorialspoint.com/redis/redis_lists.htm)

  - You could use lists to store logs or track the path a user is taking through a site.
  - If you were building a game, you might use one to track queued user actions.

--------------------------

## Chapter 3 - Leveraging Data Structures


- **Big O Notation**

  | common functions       | O             |
  | ---------------------- | ------------- |
  | ```sismember```        | O(1)          |
  | ```zadd```             | O(log(N))     |
  | ```ltrim```            | O(N)          |
  | ```zremrangebyscore``` | O(log(N)+M)   |
  | sort                   | O(N+M*log(M)) |



- **Pseudo Multi Key Queries**

  For example, you might want to get a user by email (for when they first log in) and also by id (after they’ve logged in).

  One horrible solution is to duplicate your user object into two string values:

  ```
  set users:leto@dune.gov '{"id": 9001, "email": "leto@dune.gov",...}'
  set users:9001 '{"id": 9001, "email": "leto@dune.gov", ...}'
  ```

  

  This is bad because it’s a nightmare to manage and it takes twice the amount of memory. 

  Using a hash, we can remove the need for duplication:

  ```
  set users:9001 '{"id": 9001, "email": "leto@dune.gov", ...}'
  hset users:lookup:email leto@dune.gov 9001
  ```

  

  What we are doing is using the field as a pseudo secondary index and referencing the single user object. To get a user by id, we issue a normal get :

  ```
  get users:9001
  ```

  

  To get a user by email, we issue an hget followed by a get

  ```ruby
  id = redis.hget('users:lookup:email', 'leto@dune.gov')
  user = redis.get("users:#{id}")
  ```

  

-  **References and Indexes**

  Having to manually deal with references in Redis is unfortunate.

- **Round Trips and Pipelining**

  Normally when a client sends a request to Redis it waits for the reply before sending the next request. 

  With pipelining you can send a number of requests without waiting for their responses. 

  This reduces the networking overhead and can result in significant performance gains.

  Redis will use memory to queue up the commands, so it’s a good idea to batch them.

  How large a batch you use will depend on what commands you are using, and more specifically, how large the parameters are.

- **Transactions**

  Redis is actually single-threaded, which is how every command is guaranteed to be atomic.

  particularly useful when you consider that some commands do multiple things. For example:

  	- ```incr ```is essentially a get followed by a set
  	- ```getset``` sets a new value and returns the original
  	- ```setnx``` first checks if the key exists, and only sets the value if it does not

  What guarantee does Redis make about transactions?

  	- The commands will be executed in order
  	- The commands will be executed as a single atomic operation
  	- That either all or none of the commands in the transaction will be executed

- **Keys Anti-Pattern**

  - The ```keys``` command: This command takes a pattern and finds all the matching keys.

    Say you are building a hosted bug tracking service.

    Each account will have an id and you might decide to store each bug into a string value with a key that looks like ```bug:account_id:bug_id``` 

    If you ever need to find all of an account’s bugs :

    ```
    keys bug:1233:*
    ```

    

  - The better solution is to use a hash. Much like we can use hashes to provide a way to expose secondary indexes, so too can we use them to organize our data:

    ```
    hset bugs:1233 1 '{"id":1, "account": 1233, "subject": "..."}'
    hset bugs:1233 2 '{"id":2, "account": 1233, "subject": "..."}'
    ```

    

  - To get all the bug ids for an account we simply call ```hkeys bugs:1233```

  - To delete a specific bug we can do ```hdel bugs :1233 2``` and to delete an account we can delete the key via ```del bugs:1233```

-----------------------------------------------


## Chapter 4 - Beyond The Data Structures


- **Expiration**

  - Redis allows you to mark a key for expiration.

  - You can give it an absolute time in the form of a Unix timestamp (seconds since January 1, 1970) or a time to live in seconds.

  - This is a key-based command, so it doesn’t matter what type of data structure the key represents.

    ```
    expire pages:about 30
    expireat pages:about 1356933600
    ```

  - You can find out how long an item has to live until via the ```ttl``` command

    ```
    ttl pages:about
    ```

    

  - You can remove the expiration on a key via the persist command

    ```
    persist pages:about
    ```

    

  - ```setex``` is a special <u>string command</u> which lets you set a string and specify a time to live in a single atomic command. 

    ```
    setex pages:about 30 '<h1>about us</h1>....'
    ```

    

- **Publication and Subscriptions**

  - Redis lists have an ```blpop``` and ```brpop``` command which returns and removes the first (or last)  element from the list or blocks until one is available.

    These can be used to power a simple queue.

  - Redis has first-class support for publishing messages and subscribing to channels.

    Open a new ```redis-cli``` window and subscribe to a channel (we’ll call it warnings ) using the command:

    ```
    subscribe warnings
    ```

    

  - The reply is the information of your subscription. Now, in the other window, publish a message to the warnings channel:

    ```
    publish warnings "it's over 9000!"
    ```

    ```publish``` command returned the value 1. 

    This indicates the number of clients that received the message.

    

  - If you go back to your first window you should have received the message to the warnings channel.

  - You can subscribe to multiple channels ( subscribe channel1 channel2 ... ), subscribe to a pattern of channels.  ```( psubscribe warnings:* )``` and use the unsubscribe and punsubscribe commands to stop listening to one or more channels, or a channel pattern.

- **Monitor and Slow Log**

  - ```monitor``` command is a great debugging tool that gives you insight into how your application is interacting with Redis. 

    enter the ```monitor``` command. 

    In another window execute any other type of command (like get or set ). You should see those commands, along with their parameters, in the first window (where ```monitor``` is running).

  

  - ```slowlog``` which acts as a great profiling tool.

    It logs any command which takes longer than a specified number of micro seconds. 

    For now you can configure Redis to log all commands by entering:

    ```
    config set slowlog-log-slower-than 0
    ```

    

    Next, issue a few commands. Finally you can retrieve all of the logs, or the most recent logs via:

    ```
    slowlog get
    slowlog get 10
    ```

    

    You can also get the number of items in the slow log by entering ```slowlog len``` 

    For each command you entered you should see four parameters:

    - An auto-incrementing id
    - A Unix timestamp for when the command happened
    - The time, in microseconds, it took to run the command
    - The command and its parameters

    The slow log is maintained in memory, so running it in production, even with a low threshold, shouldn’t be a problem. 

    By default it will track the last 1024 logs.

  

- **Sort**

  It lets you sort the values within a list, set or sorted set (sorted sets are ordered by score, not the members within the set)

  Which will return the values sorted from lowest to highest. Here’s a more advanced example. It will shows us how to page the sorted records (via limit ), how to return the results in descending order (via desc ) and how to sort lexicographically instead of numerically (via alpha ).

  ```
  sadd friends:ghanima leto paul chani jessica alia duncan
  sort friends:ghanima limit 0 3 desc alpha
  ```

  sort command can dereference those relations and sort by the underlying value. For example, say we have a bug tracker which lets users watch issues. We might use a set to track the issues being watched:

  ```
  sadd watch:leto 12339 1382 338 9338
  ```

  

  It might make perfect sense to sort these by id (which the default sort will do), but we’d also like to have these sorted by severity. To do so, we tell Redis what pattern to sort by. First, let’s add some more data so we can actually see a meaningful result:

  ```
  set severity:12339 3
  set severity:1382 2
  set severity:338 5
  set severity:9338 4
  ```

  

  To sort the bugs by severity, from highest to lowest, you’d do:

  ```
  sort watch:leto by severity:* desc
  ```

  Although you can have millions of keys within Redis, I think the above can get a little messy. Thankfully sort can also work on hashes and their fields. Instead of having a bunch of top-level keys you can leverage hashes:

  ```
  hset bug:12339 severity 3
  hset bug:12339 priority 1
  hset bug:12339 details '{"id": 12339, ....}'
  
  hset bug:1382 severity 2
  hset bug:1382 priority 2
  hset bug:1382 details '{"id": 1382, ....}'
  
  hset bug:338 severity 5
  hset bug:338 priority 3
  hset bug:338 details '{"id": 338, ....}'
  
  hset bug:9338 severity 4
  hset bug:9338 priority 2
  hset bug:9338 details '{"id": 9338, ....}'
  ```

  

  Not only is everything better organized, and we can sort by severity or priority , but we can also tell sort what field to retrieve:

  ```
  sort watch:leto by bug:*->priority get bug:*->details
  ```

  

  The same value substitution occurs, but Redis also recognizes the -> sequence and uses it to look into the specified field of our hash. We’ve also included the get parameter, which also does the substitution and field lookup, to retrieve bug details.

  Over large sets, sort can be slow. The good news is that the output of a sort can be stored:

  ```
  sort watch:leto by bug:*->priority get bug:*->details store watch_by_priority:leto
  ```

  

- **Scan**

  We saw how the ```keys``` command, while useful, shouldn’t be used in production. 

  Redis 2.8 introduces the ```scan``` command which is production-safe. Although ```scan``` fulfills a similar purpose to keys there are a number of important difference. To be honest, most of the differences will seem like idiosyncrasies, but this is the cost of having a usable command.

  Rather than implementing paging through a limit and offset , scan uses a cursor .

  The first time you call scan you supply ```0``` as the cursor. 

  Below we see an initial call to scan with an pattern match (optional) and a count hint (optional):

  ```
  scan 0 match bugs:* count 20
  ```

  

  As part of its reply, scan returns the next cursor to use. Alternatively, scan returns 0 to signify the end of results. Note that the next cursor value doesn’t correspond to the result number or anything else which clients might consider useful.

  A typical flow might look like this:

  ```
  scan 0 match bugs:* count 2
  > 1) "3"
  > 2) 1) "bugs:125"
  scan 3 match bugs:* count 2
  > 1) "0"
  > 2) 1) "bugs:124"
  >    2) "bugs:123"
  ```

  Our first call returned a next cursor (3) and one result. Our subsequent call, using the next cursor, returned the end cursor (0) and the final two results. The above is a typical flow. Since the ```count``` is merely a hint, it’s possible for ```scan``` to return a ```next cursor``` (not 0) with no actual results. In other words, an empty result set doesn’t signify that no additional results exist. Only a 0 cursor means that there are no additional results.





