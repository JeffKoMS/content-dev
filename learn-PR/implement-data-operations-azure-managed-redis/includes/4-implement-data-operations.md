Redis provides a rich set of commands for storing, retrieving, and managing data. When working with Azure Managed Redis using the redis-py Python library, you can perform various data operations efficiently. Understanding how to implement basic operations, set expiration times, and manage cache invalidation is essential for building performant applications.



## Connect to Azure Managed Redis with redis-py

Before performing data operations, establish a connection to your Azure Managed Redis instance. The redis-py library supports both synchronous and asynchronous operations.

```python
import redis

# Create a Redis client
r = redis.Redis(
    host='your-redis-instance.redis.azure.net',
    port=10000,
    ssl=True,
    decode_responses=True,
    password='your-access-key'
)

# Test the connection
if r.ping():
    print("Connected to Redis successfully")
```

For production environments, consider using Azure Entra ID authentication with the `redis-entraid` package for enhanced security.

## Basic data operations

### Set and Get operations

The most fundamental operations in Redis are storing and retrieving string values using `SET` and `GET` commands.

```python
# Store a value
result = r.set('user:1001:name', 'Alice Smith')
print(f"SET operation successful: {result}")  # Returns True

# Retrieve the value
name = r.get('user:1001:name')
print(f"Retrieved name: {name}")  # Output: Alice Smith
```

### Multiple key operations

When you need to work with multiple keys simultaneously, use `MSET` and `MGET` for better performance.

```python
# Set multiple keys at once
r.mset({
    'user:1001:name': 'Alice Smith',
    'user:1001:email': 'alice@example.com',
    'user:1001:age': '28'
})

# Get multiple values at once
values = r.mget('user:1001:name', 'user:1001:email', 'user:1001:age')
print(f"User data: {values}")  # Returns list of values
```

### Check key existence

Before attempting to retrieve or update data, verify whether a key exists.

```python
# Check if a key exists
if r.exists('user:1001:name'):
    print("Key exists")
    
# Check multiple keys
count = r.exists('user:1001:name', 'user:1001:email', 'user:9999:name')
print(f"Number of existing keys: {count}")  # Returns 2
```

### Delete operations

Remove keys from the cache when data is no longer needed or becomes stale.

```python
# Delete a single key
result = r.delete('user:1001:name')
print(f"Keys deleted: {result}")  # Returns count of deleted keys

# Delete multiple keys
result = r.delete('user:1001:email', 'user:1001:age')
print(f"Keys deleted: {result}")
```

## Working with expiration

Setting expiration times on keys is crucial for automatic cache invalidation and memory management. Redis provides multiple ways to set and manage key expiration.

### Set value with expiration

Use `SETEX` to set a value and expiration time in a single atomic operation.

```python
# Set a key with 60-second expiration
r.setex('session:abc123', 60, 'user_data')

# Alternative: Set value with expiration in milliseconds
r.psetex('temp:data', 5000, 'temporary_value')  # Expires in 5000ms
```

### Add expiration to existing keys

Apply expiration to keys that already exist in the cache.

```python
# Set a key without expiration
r.set('user:1002:preferences', 'dark_mode')

# Add 3600-second (1 hour) expiration
r.expire('user:1002:preferences', 3600)

# Set expiration in milliseconds
r.pexpire('user:1002:preferences', 3600000)

# Set expiration at specific Unix timestamp
import time
expire_at = int(time.time()) + 7200  # 2 hours from now
r.expireat('user:1002:preferences', expire_at)
```

### Check and manage TTL

Monitor the time-to-live (TTL) of keys to understand when they expire.

```python
# Get TTL in seconds
ttl = r.ttl('user:1002:preferences')
if ttl == -1:
    print("Key exists but has no expiration")
elif ttl == -2:
    print("Key does not exist")
else:
    print(f"Key expires in {ttl} seconds")

# Get TTL in milliseconds
ttl_ms = r.pttl('user:1002:preferences')
print(f"Key expires in {ttl_ms} milliseconds")

# Remove expiration (persist the key)
r.persist('user:1002:preferences')
```

## Cache invalidation patterns

Effective cache invalidation ensures your application serves fresh data while maintaining optimal performance.

### Time-based invalidation

The simplest approach uses TTL to automatically expire cached data after a set period.

```python
def cache_database_query(query_key, query_function, ttl=300):
    """
    Cache database query results with automatic expiration.
    
    Args:
        query_key: Cache key for the query
        query_function: Function that executes the database query
        ttl: Time to live in seconds (default: 5 minutes)
    """
    # Check if result is cached
    cached_result = r.get(query_key)
    if cached_result:
        print("Cache hit")
        return cached_result
    
    # Execute query and cache result
    print("Cache miss - executing query")
    result = query_function()
    r.setex(query_key, ttl, result)
    return result

# Usage example
def get_product_details():
    # Simulate database query
    return "Product: Laptop, Price: $999"

product_data = cache_database_query('product:12345', get_product_details, ttl=600)
```

### Manual invalidation on update

When data changes, explicitly remove or update the cached version to maintain consistency.

```python
def update_user_profile(user_id, new_data):
    """
    Update user profile and invalidate related cache entries.
    """
    # Update database (simulated)
    save_to_database(user_id, new_data)
    
    # Invalidate all related cache keys
    cache_keys = [
        f'user:{user_id}:profile',
        f'user:{user_id}:summary',
        f'dashboard:user:{user_id}'
    ]
    deleted = r.delete(*cache_keys)
    print(f"Invalidated {deleted} cache entries")

def save_to_database(user_id, data):
    # Placeholder for database operation
    pass
```

### Cache-aside pattern with expiration

Implement the cache-aside (lazy loading) pattern with automatic expiration for optimal cache usage.

```python
def get_user_data(user_id, ttl=3600):
    """
    Retrieve user data using cache-aside pattern.
    
    Args:
        user_id: User identifier
        ttl: Cache expiration in seconds (default: 1 hour)
    """
    cache_key = f'user:{user_id}:data'
    
    # Try to get from cache
    cached_data = r.get(cache_key)
    if cached_data:
        return cached_data
    
    # Cache miss - fetch from database
    user_data = fetch_from_database(user_id)
    
    # Store in cache with expiration
    if user_data:
        r.setex(cache_key, ttl, user_data)
    
    return user_data

def fetch_from_database(user_id):
    # Placeholder for database query
    return f"User data for {user_id}"
```

### Pattern-based invalidation

Delete multiple related keys using pattern matching with the `SCAN` command (not `KEYS`, which blocks the server).

```python
def invalidate_user_cache(user_id):
    """
    Invalidate all cache entries for a specific user.
    Uses SCAN for non-blocking pattern matching.
    """
    pattern = f'user:{user_id}:*'
    cursor = 0
    deleted_count = 0
    
    while True:
        cursor, keys = r.scan(cursor, match=pattern, count=100)
        if keys:
            deleted_count += r.delete(*keys)
        if cursor == 0:
            break
    
    print(f"Invalidated {deleted_count} keys for user {user_id}")

# Usage
invalidate_user_cache(1001)
```

## Best practices for data operations

### Use connection pooling

The redis-py library automatically uses connection pooling, but you can configure it for optimal performance.

```python
# Create a connection pool
pool = redis.ConnectionPool(
    host='your-redis-instance.redis.azure.net',
    port=10000,
    ssl=True,
    decode_responses=True,
    password='your-access-key',
    max_connections=50
)

# Create Redis client using the pool
r = redis.Redis(connection_pool=pool)
```

### Handle errors gracefully

Always implement error handling for Redis operations to ensure application resilience.

```python
try:
    r.setex('temp:data', 60, 'value')
except redis.ConnectionError as e:
    print(f"Connection error: {e}")
    # Implement fallback logic
except redis.TimeoutError as e:
    print(f"Operation timed out: {e}")
    # Retry or use fallback
except Exception as e:
    print(f"Unexpected error: {e}")
```

### Choose appropriate TTL values

Select expiration times based on your data's characteristics:

- **Frequently changing data**: 1-5 minutes
- **Moderate change rate**: 15-60 minutes
- **Relatively stable data**: 1-24 hours
- **Static reference data**: 24+ hours

Consider your application's requirements for data freshness versus cache hit rates when determining TTL values.
