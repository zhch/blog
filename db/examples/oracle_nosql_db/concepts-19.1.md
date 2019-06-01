
Storage Node's capacity: A Storage Node's capacity serves as a rough measure of the hardware resources associated with it.  can contain Storage Nodes with different capacities, and Oracle NoSQL Database ensures that a Storage Node is assigned a proportional load size to its capacity. each Storage Node contains monitoring software that captures information ensuring running and healthy.




A Replication Node, in turn, contains a subset of the store's data. Storage node data is automatically divided evenly into logical collections called partitions. Every Replication Node contains at least one, and typically many, partitions. Partitions are described in greater detail in Partitions.

At a high level, you can think of a Replication Node as a single database containing tables or key-value pairs. Storage Nodes host one or more Replication Nodes.




Shard:
Your store's Replication Nodes are organized into shards. A single shard contains multiple Replication Nodes. Each shard has a replica master node. The master node performs all database write activities. Each shard also contains one or more read-only replicas. The master node copies all new write activity data to the replicas. The replicas are then used to service read-only operations.



Replication Factor
The number of nodes belonging to a shard is called its Replication Factor. 


Zone
A store can be installed across multiple physical locations called zones. You set a Replication Factor on a per-zone basis. Once you set the Replication Factor for each zone in the store, Oracle NoSQL Database makes sure the appropriate number of Replication Nodes are created for each shard residing in every zone in your store.



Key

All data in the store is accessed by one or more keys. A key might be a column in a table, or it might be the key portion of a key/value pair.



Partitions
Keys are placed in logical containers called partitions, and each shard contains one or more partitions. Once a key is placed in a partition, it cannot be moved to a different partition. Oracle NoSQL Database distributes records evenly across all available partitions by hashing each record's key.
As part of your planning activities, you must decide how many partitions your store should have. You cannot configure the number of partitions after the store has been installed. 

You can expand and change the number of Storage Nodes in use by the store. The store is then reconfigured to take advantage of the new resources by adding new shards. When this happens, existing data is spread across new and old shards by redistributing partitions from one shard to another. For this reason, it is desirable to have a large number of partitions to support fine-grained reconfiguration of your store.

it is reasonable to specify a partition number that is 100 times the maximum number of shards you expect your store to contain.


READ HERE:
https://docs.oracle.com/en/database/other-databases/nosql-database/19.1/concepts/architecture.html#GUID-83D7EF83-3F09-4EC5-A0DC-EFAF543A28B1