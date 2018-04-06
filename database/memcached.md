# Memcached Notes

A huge key/value pair middle layer.

## Verbs

### Storage

- `set`: rewrite if key exists, otherwise create a new key.
- `add`: Only write if key doesn't exist. If key already exists raises failure `NOT_STORED`.
- `replace`: Only write if key exists. If key doesn't exist raises failure `NOT_STORED`.
- `cas`: Need to include a per-key dependent "unique CAS key". The CAS key get updated when the data is refreshed, and can be read using `gets`. If CAS key is corrent, it can be updated; otherwise fail by `EXISTS`. If use with the CAS key previous in the application (not in Memcached), it will update only if the CAS key is not changing (the data is not modified).

Alter:

- `append`: Append to the value of an existing key (suffix). If key doesn't exist raises failure `NOT_STORED`. *(Will it extend the expired time of the existing key)*
- `prepend`: Similar to `append`, just add the new data as prefix rather than suffix.
- `incr`/`decr`: plus/minus certain ammount for numeric values. Return `CLIENT_ERROR` if the value is not numeric.

Delete:

- `delete`: Delete an existing key. Return `NOT_FOUND` if not exist. The associated data is also deleted.
- `flush_all`: Erase all data.

### Retrieval

- `get`: Return value only. *(What happens if the key does not exist??)*
- `gets`: Return key, flag, value length, CAS token (and value).

### Statistics

- `stats`: Systemwise information.
- `stats items`: Statistics of existing data (count, age, ...).
- `stats slabs`: Statistics of computer resource usage.
- `stats size`: Overall data size and count. *(Count duplicates to what's in `stats items`??)*

## APIs

### Python

#### `pymemcache` 

([Code](https://github.com/pinterest/pymemcache) and [documentation](https://pymemcache.readthedocs.io/en/latest/) in here)

#### pylibmc

#### Python-memcache

#### memcache_client

### Java

## References

1. [Tutorials Point](https://www.tutorialspoint.com/memcached/index.htm)