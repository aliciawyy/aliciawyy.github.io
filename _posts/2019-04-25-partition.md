---
title: "Partition"
categories:
  - scalability
tags:
  - database
  - notes
toc: true
use_math: true
---

For very large datasets, we need to break the data into *partitions*. Different partitions can be placed on different nodes in a shared-nothing cluster.

Partition is usually associated with replication. Each node can store several different partitions.

# Partitioning of Key-Value Data

The goal of partitioning is to spread the data and query load evenly across nodes.

## Random Assignment

One simple solution can be assign records to nodes randomly, but this leads to a problem for reading later. It's hard to know which node has the record.

## Key-range Partition

Another solution is to assign by key range like the volumes of encyclopedia. Within each partition, we can also keep keys in sorted order. The partition boundaries can be decided either manually by an administrator or automatically.

The downside of key range partitioning is that sometimes it can lead to hot spots.

## Hash Key Partition

We can use a hash function to determine the partition of a given key, this can help spread evenly the skewed region of the key's distribution. For example, a 32-bit hash function takes a string and returns a *random* number from $0$ to $2^32 - 1$.

For hash function used for partitioning, we don't need it to be too strong, but it should give same hash value given the same key.

The partition boundaries can be evenly spaced or chosen by *consistent hashing*.

**consistent hashing ** is to use randomly chosen partition boundaries to avoid the need for central control. However, this technique is not so used in parctice for it is confusing.
{: .notice--info}


The downside of hash key partition is that we lose the property of sorted key. This will make range queries not as efficient as key-range partition for example.

In practice, *Cassandra* uses a *compound primary key* where the first column is hashed to decide the partition and the other columns are sorted within the partition for storage. This structure makes it very efficient to retrieve a range given a particular key.

### Detect and Compensate Skewed Workload

For highly skewed workload, for example on social media, a celebrity with millions of followers can easily cause a storm of reads and writes on the same partition. It is usually the responsibility of the application to handle this, like to add a random number as the prefix to the key, so the requests can be spread on all partitions.


# Partitioning and Secondary Indexes

- Local index
- Global index: difficult to write

# Rebalancing Partitions

When load or dataset increases, some node fails, it requires to move load from one node in the cluster to another. This process is called *rebalancing*.


