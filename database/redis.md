# Redis Notes

- Key-value store
- Data structure server

Written in C.

Key features:

- Holds the entire database in memory.
	- Fast
	- May use disk for persistence.
- Replicate data to slaves.
- Rich set of data types.
- All operations are atomic.

Usage:

- Multi-utility tool
	- Caching
	- Messaging-queues 
		- Also in publish/subscribe with multiple subscribers.
	- Short-lived app data

Data types:

- Strings: 
	- Defination: a sequence of bytes:
		- Binary safe: known length not determined by any special terminating characters.
		- Maximal size: 512 MB.
	- Commands: `SET key "value"`, `GET key`.
- Hashes: 
	- Defination: a collection of key-value pairs.
	- Commands: `HMSET key field value [field value ...]`, `HGET key field`, `HGETALL key`.
- Lists: 
	- Defination: a list of strings sorted by insertaion order.
	- Commands: `LPUSH key value`, `LRANGE key start stop`.
- Sets
	- Defination: an unordered collection of strings.
	- Commands: `SADD key value [value ...]`, `SMEMBERS key`.
- Sorted sets
	- Defination: a set that every member is associated with a score to sort the set order
	- Commands: `ZADD key score value`, `ZRANGEBYSCORE key min max`

Other commands:

- Key commands: `DEL key`, `DUMP key`, ...
- HyperLogLog:
	- An algorithm counts the approximate number of unique elements in a set.
	- `PFADD key value [value ...]`, `PFCOUNT key [key ...]`, `PFMERGE` *(So completely no way to get the values back again??)*
- Messanging system:
	- Redis provides its implementation of publish/subscribe architecture. *(Through its arbitrary of slaves?)*
	- `SUBSCRIBE`, `PUBLISH`
- Transactions: 
	- Defination: Execute a group of commands in a single step.
	- Atomic
	- `MULTI`, `EXEC`
- Scripting:
	- Through Lua interpreter.
	- `EVAL`
- Backup:
	- `SAVE`

## References

1. [Tutorials Point](https://www.tutorialspoint.com/redis/index.htm)
1. Redis [commands](https://redis.io/commands/) in official website