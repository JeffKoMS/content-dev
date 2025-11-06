Azure Managed Redis provides an in-memory data store based on the Redis Enterprise software. Redis Enterprise improves the performance and reliability of the community edition of Redis, while maintaining compatibility. Microsoft operates the service, hosted on Azure, and usable by any application within or outside of Azure.

Azure Managed Redis can improve the performance and scalability of an application that heavily uses backend data stores. It's able to process large volumes of application requests by keeping frequently accessed data in the server memory, which can be written to and read from quickly.

## Data cache

Databases are often too large to load directly into a cache. It's common to use the cache-aside pattern to load data into the cache only as needed. When the system makes changes to the data, the system can also update the cache, which is then distributed to other clients. Additionally, the system can set an expiration on data, or use an eviction policy to trigger data updates into the cache.

## Content cache

Many web pages are generated from templates that use static content such as headers, footers, banners. These static items shouldn't change often. Using an in-memory cache provides quick access to static content compared to backend datastores. This pattern reduces processing time and server load, allowing web servers to be more responsive. It can allow you to reduce the number of servers needed to handle loads. Azure Managed Redis provides the Redis Output Cache Provider to support this pattern with ASP.NET.

## Session store

This pattern is commonly used with shopping carts and other user history data that a web application might associate with user cookies. Storing too much in a cookie can have a negative effect on performance as the cookie size grows and is passed and validated with every request. A typical solution uses the cookie as a key to query the data in a database. When you use an in-memory cache, like Azure Managed Redis, to associate information with a user is faster than interacting with a full relational database.

## Choosing the right tier

Selecting the right Redis tier determines your application's availability guarantees, performance ceiling, and monthly costs. There are four tiers of Azure Managed Redis available, each with different performance characteristics and price levels.

Three tiers store in-memory data:

- **Memory Optimized** Ideal for memory-intensive use cases that require a high memory-to-vCPU ratio (8:1) but don't need the highest throughput performance. It provides a lower price point for scenarios where less processing power or throughput is necessary, making it an excellent choice for development and testing environments.
- **Balanced (Memory + Compute)** Offers a balanced memory-to-vCPU (4:1) ratio, making it ideal for standard workloads. This tier provides a healthy balance of memory and compute resources.
- **Compute Optimized** Designed for performance-intensive workloads requiring maximum throughput, with a low memory-to-vCPU (2:1) ratio. It's ideal for applications that demand the highest performance.

One tier stores data both in-memory and on-disk:

- **Flash Optimized (preview)** Enables Redis clusters to automatically move less frequently accessed data from memory (RAM) to NVMe storage. This reduces performance, but allows for cost-effective scaling of caches with large datasets.

For a detailed feature comparison, visit [About Azure Managed Redis](/azure/redis/overview#choosing-the-right-tier)

## Additional resources

- For pricing information, visit [Azure Managed Redis Pricing](https://aka.ms/amrpricing)
