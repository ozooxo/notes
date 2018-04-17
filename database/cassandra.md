# Cassandra Notes

Data properties:

+ Very large amount (but have relatively simple structure)
+ Structured/semi-structured/unstructured

Database features:

+ Peer-to-peer distributed cross multiple nodes
	+ Nodes are equals
	+ Nodes are independent
	+ All nodes can accept read and write requests
	+ Nodes replicate data (and automatically update/repair out-of-date data value) -- through Gossip Protocol
+ (Linear) scalable
+ Column-oriented/flexible schema
	+ The attributes are flattened (no build-in relationship)
	+ Friendly to SQL null values: individual row doesn't need to have the same columns.
	+ If needed, relationship can only be setup by super columns.
+ Atomicity, Consistency, Isolation, and Durability (ACID)

Write: Content first saves to **mem-table** (with the query/operation itself is written into **commit log**). Content saves to **SSTable** when **mem-table** is full.

Read: Content reads from **mem-table**, or if not exist from **SSTable** through **bloom filter**.

Container layers (outermost to inner)

+ **Keyspace**
	+ Configure replication setups:
		+ Replication factor
		+ Node setup strategy
+ **Column family** *(Is it called a table in CQL??)*
+ **Row**
	+ Individual row doesn't need to have the same columns (So it is not column and then row).
+ **Column**
	+ Act as a nested key-value(-timestamp) pair
	+ Always have three values
		+ Dey/column name
		+ Value
		+ Time stamp
	+ A special column called **super column** which saves a set of columns (one like to query together).

## Cassandra Query Language (CQL)

Command line shell: `cqlsh`

Actually query is really like SQL.

`[CREATE|ALTER|DROP] [KEYSPACE|TABLE|INDEX]`

`SELECT ... FROM ... WHERE ... ORDERBY ...`

`[INSERT|UPDATE|DELETE|BATCH]`

## Reference

1. [TutorialsPoint](https://www.tutorialspoint.com/cassandra/index.htm)