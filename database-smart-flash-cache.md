[Previous](keep-buffer-pool.md) [Next](deleting-true-cache.md) JavaScript
must be enabled to correctly display this content

  1. [Oracle True Cache User's Guide](index.md)
  2. [Complementary Caching Features](complementary-caching-features.md)
  3. Database Smart Flash Cache

## 7.3 Database Smart Flash Cache

If the data that you want to cache doesn't fit in memory, you can expand the
capacity of True Cache by adding flash devices. You do this by configuring
Database Smart Flash Cache.

To enable Database Smart Flash Cache, see [Configuring Database Smart Flash
Cache](https://docs.oracle.com/pls/topic/lookup?ctx=db23&id=ADMIN-GUID-31E0885D-53DB-442D-BC5F-DDE0588C915A).

Note:

When configuring Database Smart Flash Cache on True Cache, spilling TDE
encrypted data to local flash devices is not supported. If a data block is TDE
encrypted on the primary database, it stays in the DRAM buffer cache on True
Cache.


[← Previous](keep-buffer-pool.md)

[Next →](deleting-true-cache.md)
