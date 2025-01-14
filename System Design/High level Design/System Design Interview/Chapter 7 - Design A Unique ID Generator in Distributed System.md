- Primary key with auto increment fails in distributed system, because a single databse server is not large enough and generating unique IDs across multiple databses with minimal delay is challenging.


### Requirements
1. IDs must be unique and sortable.
2. ID increments by time but not necessarily by increments of 1. IDs created in the evening are larger than those created in the morning the same day.
3. IDs only contains numerical value
4. IDs length should fit into 64-bit
5. System should be able to generate 10000 IDs/sec


### Approaches
1. Multi-master replication
2. Universally unique identifier (UUID)
3. Ticket server
4. Twitter snowflake approach


##### 1. Multi-master replication
This approach increments the ID in each DB by *k*, where *k* = number of DBs
In this way you can create *1,3,5,....* & *2,4,6,...* for 2 databases.

###### Drawbacks


