### Basics

A typical **three-tier** system: Client -> Server -> DB

For web applications, client is referred to browsers. Browsers send out requests to application server. Between application server and browsers, normally there is a **load balancer**, which distributes the requests evenly among a cluster of servers. Such configuration not only increases the capacity of the system, but also provides **redundancy** to the system in a manner that if one server fails, other servers could help to process the requests.

There are a number of **load balancing schemes**, including:

1. **Even** task distribution: LB randomly distributes tasks to servers (round robin). This works if the capacity of each server is the same and statistically, the complexity of the tasks received by each server is the same. DNS load balancing is an example of even task distribution, although computers normally caches the IP address and use it for the next visit before doing another IP address lookup.
2. **Even** task distribution: This scheme takes into consideration of the capacity of each server. More powerful servers will receive more tasks.
3. Even size task **queue**: LB keeps track of the task queue of each server. Once a task is received by LB, it will find out the server with the shortest task queue and assign the task accordingly. This implicitly takes into consideration of the capacity of each server and the complexity of the tasks.
4. **Autonomous** scheme: LB does not have to know the servers. All the tasks are stored in a queue inside LB. Each server would connect to LB and download the task. After finishing processing the task, the server would connect LB again and return the response.

Also, it is recommended to store user data and **session** data of requests in a centralised place where each server could access. This would avoid the problem that if user lands on different servers at different times, their data might not be accessible.

Relational and non-relational databases have been widely used. For **relational** DB, e.g., MySQL, it might involve master-slave replication, sharing, denormalisation and SQL tuning to scale up. For **non-relational** DB, i.e., NoSQL, it is often easier to scale up, but this would require joining tables to be done in the application code.

To speed up data retrieval, **cache** (memcached, redis) can be applied between servers and database. A cache is simply a key-value pair stored in memory, which is faster to retrieve if compared with getting data from the main data source. Cache key is normally hashed query, while cache values can be simply datasets from query or an object. If cache values datasets from the corresponding query, it is less flexible to manage since even a tiny change to a cell of a table would require to actively expire all the cached entries related to that table. However, if we store values as an object and separate the properties/attributes that are easy and hard to cache, it would make it much easier to manage the cache. Also storing cache values as objects allow **asynchronous** operations.

When it comes to cache, there are normally three aspects we need to deal with:

1. **Populate** the cache: We can either populate it upfront when the system starts or we could lazily populate it when needed. Also it is possible to combine two ways and populate those popular data upfront and then lazily load others.
2. **Sync**: We need to make sure the data in the cache is in sync with the data in the remote systems. If the data in the remote system can only be updated by the computer keeping the cache, then we can use write-through cache to make them in sync. However, if the update of data on the remote system is independent of the computer keeping the cache, we need to expire the cache to ensure the data is in sync. We could either do time-based expiry or active expiry.
3. **Size**: There are a few schemes for maintaining the size of the cache: Time-based, FIFO, FILO, LRU and LFU.

### Other terms to look at

1. DB master-slave replication
2. DB indexing to speed up looking up
3. DB sharing, partition
4. Hashed cache key: salt?