## 1. Database creation using bash
- creating a file and adding kv pairs is simple db
- having grep and finding the last line will be the select query
- it keeps all possible states of a key but shows only the latest one
- write speed is great as its just appending
- no seeking, just sequential writing
- no rewrites
    - makes conflicts less and make concurrency high
- but read is slow as things scale, grep will let you down
    - O(n)

## 2. Evolution of DBs from simple files
- Since read is slow we need to mainatin the write speed and work on read
    1. Index optimization : map metadata structure of the primary data
        - simplest : hashmap in ram handles kv pairs, not the whole data but just the pointer
        - if the location changes of the value, it needs to be updated in hashmap
        - O(1)
        - bitcask, distributed db for this purpose, production grade
- but what if the metadata cant fit in the memory as mentioned above
- moving it to disk will increase latency (due to random seeks)
- hashmap is also useless for range queries (select something from this to this)
    - since it doesn store things in order
### SS (Sorted string) table
- unsorted log to sorted one
- the files are written into but in an order
- while writing into the kv pair db, it needs to be sorted
- this helps because instead of storng the keys (string) in hashmap, now we can justs tore it using the numbers so its sparser and takes less space
- having pointer for every key is not required, has pointer for each 4kb of data
- so now if we knwo where to look for , we will be aprooximately there and need not search the entire data
- read is fast now too
- but the writes needs to be sorted
    - hence slow
    - need ot move around
    - no more only appeneding
### LSM tree (log structured merge) 12:00
- write is divided into 2 parts
1. memory 
    - 
2. disk