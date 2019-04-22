---
title: "Log-structured Storage Engines"
categories:
  - scalability
tags:
  - scalability
  - notes
toc: true
---

Many databases internally use a *log*, which is an append-only data file. A record is written as one line in a file. When one record is updated, the old line is not overwritten, but the latest one is appended in the end of the file.

An *index* is an additional structure derived from the primary data. It works as a metadata that helps you to locate the data you want, so you don't need to scan the whole database for it. Maintaining an index usually incurs overhead especially for writes, but a well-chosen index can speed up read queries.

# Hash Indexes
For a key-value database, a simple indexing strategy is to keep an in-memory hash map where every key is mapped to a byte offset in the data file, that is the location at which the data can be found. Whenever you append a key-value pair, you also update the hash map.

A storage engine like this is well suited to situations where the value for each key is updated frequently, since both reads and writes are efficient.

## Write

To avoid wasting disk space, we can perform *compaction*: throw away duplicate keys in the log and keep only the most recent update for each key.

To avoid running out of disk space, we can break the log into segments of a certain size. We close and **freeze** a segment file when it reaches the size. Then while we serve reads and writes as normal, we can use a background thread to *compact* and *merge* the frozen segments into a new file. Once it finishes, we switch the read and write to the new segment file and the old segments can just be deleted.

## Read

Each segment has its in-memory hash table. For reads, we will search the hash map from the most recent segment to the oldest.

## Limitations

- Hash table must fit in memory, so if you have a very large number of keys, this solution doesn't work.
- Range queries are not so efficient as you need to look up the keys in the hash map one by one.

### Append-only vs update-in-place

Append-only is better solution for several reasons

- Appending and segment merging are sequential write operations. They are much faster then random writes, especially on magnetic spinning-disk hard drives.
- Concurrency and crash recovery are simpler if segments are **immutable**.
- Merging avoids the problem of datafiles getting fragmented over time.


# SSTable and LSM-Tree

With previous model, each log-structured storage segments is a sequence of key-values pairs listed in chronological order. With *Sorted String Table* (SSTable), we require that the sequence of key-value pairs is **sorted by key**.

This filesystem was originally described as *Log-Structured Merge Tree* and databases based on this principle of merging and compacting sorted files are often called LSM storage engines.

## Write

With SSTable, write is no longer immediate. When a write comes in, it is first added to an in-memory balanced tree data structure like a red-black tree or AVL tree, this is sometimes called a *memtable*. When a memtable gets bigger than a certain size (usually a few megabytes), it is writen to disk as an SSTable file. The new SSTable becomes the most recent segment of the database. When one memtable is writen to disk, writes can continue to a new memtable instance.

A merging and compaction thread can run in background from time to time. Merging in this case is much simpler as it will be just merging several key-sorted files. If the same key appears in several segments, only the value in the most recent segment is kept.


## Read

A read query first searches the memtable, then searches from the most recent segment to the oldest.

We just need a *sparse* in-mermory index table to tell us the location of some keys. Given a key, we first search for the lower-bound of the key and scan from there till reach the required key.


## Limitations

When there is a database crash, we will lose the in-memory SSTable.
