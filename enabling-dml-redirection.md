[Previous](verifying-true-cache-configuration.md) [Next](deploying-true-
cache-containers.md) JavaScript must be enabled to correctly display this
content

  1. [Oracle True Cache User's Guide](index.md)
  2. [Configuring True Cache](configuring-true-cache.md)
  3. Enabling DML Redirection

## 2.6 Enabling DML Redirection

True Cache is a read-only cache, so you can't directly update the cache, but
you can use DML redirection to indirectly update the cache.

DML redirection writes data to the primary database and then that data is
automatically updated in the cache. This is similar to how Oracle Active Data
Guard works. Because DML redirection uses more resources, it's not recommended
for update-intensive applications.

To enable and use DML redirection, set the `ADG_REDIRECT_DML` initialization
parameter to `TRUE` on True Cache.

Note:

If the user logs in to True Cache with OS authentication, the user can't
enable `ADG_REDIRECT_DML`. This is because the True Cache database
administrator might not actually have the `SYSDBA` password but is allowed to
use OS authentication to manage True Cache as `SYSDBA`. If the user can set
`ADG_REDIRECT_DML`, then that user can update primary data as `SYSDBA` without
knowing the `SYSDBA` password.

For more on enabling DML redirection, see [Managing Physical and Snapshot
Standby Databases](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/oracle-database/23/odbtc&id=SBYDB-GUID-B140C38B-DE01-4252-8422-7154018DDFEC).


[← Previous](verifying-true-cache-configuration.md)

[Next →](deploying-true-cache-containers.md)
