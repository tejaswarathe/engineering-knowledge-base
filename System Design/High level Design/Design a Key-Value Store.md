- Stores like Redis, DyamoDB, Memcached, Cassandra
- is a Non-relational database
- Keys are plain text or hashed value
- Values are Opaque? object

## Problem

Design k-v store with features

- Size of K-V is small <10KB
- Store Big Data
- High Availability
- High Scalability
- Automatic Scaling
- Tunable consistency
- Low Latency

# CAP theorem

1. Consistency
2. Availability
3. Partition Tolerance

Any two of the above three can be achieved at a time in any system

# Consistent Hashing

For automatic scaling, this is used, also including virtual nodes ensure equal distribution of data.

N nodes clockwise from the key can be selected and data can be replicated on them.

To ensure same server do not get all the data due to virtual nodes, we only select unique nodes.

# Quorum consensus

This model states the number of responses required for an Operation to be successful.

Example:

N = number of replicas

W = number of responses required from different servers in a write operation for it to be marked success

R = number of responses required from different servers in a read operation for it to be marked success

### Different cases:

W+R > N : this means there is at least one server which has the latest data and can read and write on it, strong consistency guaranteed

W+R ≤ N : strong consistency not guaranteed

W = 1, R = N : optimised for write operations

W = N, R = 1 : optimised for read operations

## Consistency models

1. Strong
2. Weak
3. Eventual

### Resolving inconsistencies

1. Versioning
2. To Learn more: Vector clocks

### Handling failures

**Failure Detection - using heartbeats**

**Temporary failures -using sloppy qourum**

**Permanent failures - Merkle trees**

**Data center outage**

### More topics to read on

1. Bloom filter
2. Merkle tree
3. SSTable - Sorted string Table