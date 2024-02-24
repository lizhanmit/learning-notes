# Redis Note

[Redis - the What, Why and How to use Redis as a primary database](https://dev.to/techworld_with_nana/redis-the-what-why-and-how-to-use-redis-as-a-primary-database-2c55)

- [Redis Note](#redis-note)
  - [Redis vs. Other Key-value Stores](#redis-vs-other-key-value-stores)

---

## Redis vs. Other Key-value Stores

- Redis is a different evolution path in the key-value databases where values can contain more complex data types, with atomic operations defined on those data types.
- Redis is an in-memory but persistent on disk database.
  - Two on-disk storage formats (RDB and AOF) don't need to be suitable for random access, so they are compact and always generated in an append-only fashion. (AOF: Append Only File)