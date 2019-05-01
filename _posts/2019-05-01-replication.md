---
title: "Replication"
categories:
  - scalability
tags:
  - database
  - notes
toc: true
use_math: true
---

Advantages of replication include

- Reduce access latency - keep data geographically close to users
- Increase system availability - allow system to continue working even if some nodes failed
- Increase read efficiency - more machines can serve read queries

As the main difficulty in replicating data lies in *handling changes to replicated data*, there are three popular methods to deal with it

- Single-leader
- Multi-leader
- Leaderless

# Leader-Based Replication

As every write to the database needs to be processed by every replica, the most common solution for this is called *leader-based replication*. It works as follows

1. When clients want to write to the database, they first send their requests to the leader, which first writes the new data to its local storage. Only the leader accepts writes from clients.
2. When the leader writes new data to its local storage, it also sends the data change to all of its followers as part of a *replication log* or *change stream*.

For read-only queries, both leader and followers can serve.


## Synchronous vs Asynchronous Replication

After the leader receives the write request, shortly afterward, it forwards the data change to the followers. Eventually, the leader notifies the client that the update was successful. The difference between synchronous and asynchronous lies in

- *Synchronous*: The leader waits until follower confirms the reception of the write before reporting success or making the write visible to clients.
- *Asynchronous*: The leader sends the message to the followers and report immediately to clients.

For *synchronous* replication, the follower is guaranteed to have an up-to-date copy of the data that is consistent with the leader, but if one follower doesn't respond, the leader will get blocked until that follower respond again.

In practice, a system can be configured as *semi-synchronous*, that is **one** of the followers is synchronous and all others are asynchronous. This guarantees that an up-to-date copy of data is at least on two nodes.

Complete asynchronous configuration is most common, especially there are many followers of if they are geographically distributed. The leader can continue to write even all of its followers fail.


## Setting Up New Followers

1. Take a consistent snapshot of the leader's database at some point in time, without lock if possible.
2. Copy the snapshot to the new follower node.
3. The follower connects to he leader and requests all the data changes that happened since the snapshot.
4. When the follower finally caught up, it can now continue to process data changes from the leader as they happen.

## Handling Node Outage

### Follower Failure

After the follower restarts, it can locate in the log the last transaction it has processed before the fault. It then connects to the leader to requests all the changes since and apply these changes till it catches up. 

### Leader Failure

When there is a failure of the leader, one of the followers will be promoted to be the new leader and all writes from clients will be on the new leader. This process is called *failover*.

1. Determine that the leader has failed. Most systems use a timeout.
2. Take the replica with the most up-to-date data as the new leader.
3. Reconfiure the system to use the new leader.



