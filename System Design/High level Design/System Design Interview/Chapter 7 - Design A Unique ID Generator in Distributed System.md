- Primary key with auto increment fails in distributed system, because a single database server is not large enough and generating unique IDs across multiple databases with minimal delay is challenging.


## Requirements
1. IDs must be unique and sortable.
2. ID increments by time but not necessarily by increments of 1. IDs created in the evening are larger than those created in the morning the same day.
3. IDs only contains numerical value
4. IDs length should fit into 64-bit
5. System should be able to generate 10000 IDs/sec


## Approaches
1. Multi-master replication
2. Universally unique identifier (UUID)
3. Ticket server
4. Twitter snowflake approach


### 1. Multi-master replication
This approach increments the ID in each DB by *k*, where *k* = number of DBs
In this way you can create *1,3,5,....* & *2,4,6,...* for 2 databases.

##### Drawbacks
- Hard to scale with multiple datacenters
- IDs do not go up with time
- Does not scale when DBs are added or removed

### 2. UUID:
is a 128-bit number.
It has a very low probability of collision.
After generating 1 billions UUID every second for 100 years the probability of duplicate becomes 50%.
Every web server can have an ID generator.

##### Pros
- Generating UUID is simple
- Easy to scale because each server generates its own IDs, and they bound to be unique.

##### Cons
- IDs are 128 bit, we need 64 bit
- They do not go up with time.
- They can be non-numeric.

### 3. Ticket Server
Using a central server to generate unique IDs.
It can use auto_increment feature.

##### Pros
- Numeric IDs
- Easy to implement and works for small to medium-scale apps.

##### Cons
- Single point of failure


### 3. Twitter Snowflake approach
Dividing 64 bits into different parts:
- 1 bit: set to 0 (for future purpose)
- 41 bits: timestamp (milliseconds since epoch)
- 5 bits: datacenter ID (total 2^5 = 32 datacenter)
- 5 bits: machine ID (total 32 machines per datacenter)
- 12 bits sequence number (increment by 1, reset to 0 every millisecond)
-

##### Pros
- Numeric IDs
- Easy to implement and works for small to medium-scale apps.

##### Cons
- Single point of failure



## Deep Dive


## More topics to know
- Clock Synchronization - [[Network time protocol]]
- Section Length tuning
- High Availability
