[Previous](complementary-caching-features.md) [Next](keep-buffer-pool.md)
JavaScript must be enabled to correctly display this content

  1. [Oracle True Cache User's Guide](index.md)
  2. [Complementary Caching Features](complementary-caching-features.md)
  3. Server-Side Result Set Cache

## 7.1 Server-Side Result Set Cache

The server-side result cache is used to cache the results of the current
queries, query fragments, and PL/SQL functions in memory. The cached results
are used in future uses of the query, query fragment, or PL/SQL function.

The cached results stay in the result cache memory part of the SGA. A cached
result is automatically invalidated whenever a database object used in its
creation is successfully changed. See [Tuning the Result
Cache](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/oracle-
database/23/odbtc&id=TGDBA-GUID-FA30CC32-17AB-477A-9E4C-B47BFE0968A1) for more
information about configuring the server-side result set cache.

To enable the server-side result cache on True Cache, issue the following
command on the primary database:

    
    
    ALTER TABLE table_name RESULT_CACHE (STANDBY ENABLE);

Support for server-side result set caching is available for both JDBC Thin and
JDBC Oracle Call Interface (JDBC OCI) drivers.

**Related Topics**

  * [Using the Result Cache on Physical Standby Databases](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/oracle-database/23&id=SBYDB-GUID-05508445-ED4C-4C5B-91EC-07B8C010BC4F)


[← Previous](complementary-caching-features.md)

[Next →](keep-buffer-pool.md)
