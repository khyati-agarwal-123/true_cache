[Previous](deploying-true-cache-oracle-rac-primary-database.md)
[Next](enabling-dml-redirection.md) JavaScript must be enabled to correctly
display this content

  1. [Oracle True Cache User's Guide](index.md)
  2. [Configuring True Cache](configuring-true-cache.md)
  3. Verifying the True Cache Configuration

## 2.5 Verifying the True Cache Configuration

Verify that True Cache and the database application services are working as
expected.

### 2.5.1 Verifying That True Cache Is Working as Expected

To verify that True Cache is applying redo and making progress, check the
following queries on True Cache.

  1. Run SQL*Plus on the True Cache.
    
        sqlplus / as SYSDBA

  2. Enter the following query:
    
        SELECT database_role, open_mode FROM v$database;

The output should look like this:

    
        DATABASE_ROLE    OPEN_MODE
    ---------------- --------------------
    TRUE CACHE       READ ONLY WITH APPLY

If `OPEN_MODE` is `READ ONLY WITH APPLY`, it means that the True Cache redo
apply is actively working.

  3. Enter the following query multiple times:
    
        SELECT current_scn FROM v$database;

If `CURRENT_SCN` is advancing over time, it means that True Cache is moving
forward as expected.

  4. To find the size of each log file, enter the following query:
    
        SELECT THREAD#, SEQUENCE#, BYTES FROM v$standby_log;

### 2.5.2 Verifying the Remote Listener Configuration

After the database application service starts on True Cache, use the `lsnrctl`
command to validate the remote listener services for the True Cache service
registration.

Note:

For Oracle RAC primary databases, before using the `lsnrctl` command, set your
`$ORACLE_HOME` environment variable to the path for the Oracle Grid
Infrastructure home (Grid home).

The following example shows two True Caches registered with the SCAN listener
for an Oracle RAC primary database:

    
    
    lsnrctl services LISTENER_SCAN1
    
    Service "sales_tc" has 2 instance(s).
        Instance "tcdb1", status READY, has 1 handler(s) for this service...
            Handler(s):
                "DEDICATED" established:0 refused:0 state:ready
                    REMOTE SERVER       
                    (DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=tc1.example.com)(PORT=1521)))
        Instance "tcdb2", status READY, has 1 handler(s) for this service...
            Handler(s):
                "DEDICATED" established:0 refused:0 state:ready
                    REMOTE SERVER       
                    (ADDRESS=(PROTOCOL=TCP)(HOST=tc2.example.com)(PORT=1521))

Note:

If the True Cache service is not registered with the remote listener, validate
the remote listener invited nodes and the True Cache initialization parameters
for `REMOTE_LISTENER` or `LISTENER_NETWORKS`.

### 2.5.3 Verifying the True Cache and Primary Database Application Services

Verify that all database application services are active on both the primary
database and True Cache.

True Cache

  1. Run SQL*Plus on True Cache.
    
        sqlplus / as SYSDBA

  2. Enter the following query:
    
        SELECT service_id, name FROM v$active_services WHERE name='true_cache_service_name';

For example:

    
        SELECT service_id, name FROM v$active_services WHERE name='SALES_TC';
    
    SERVICE_ID  NAME
    ----------  ---------
            28  SALES_TC

Primary Database

  1. Run SQL*Plus on the primary database.
    
        sqlplus / as SYSDBA

  2. Enter the following query:
    
        SELECT service_id, name, true_cache_service FROM v$active_services WHERE name='primary_db_service_name';

For example:

    
        SELECT service_id, name, true_cache_service FROM v$active_services WHERE name='SALES';
    
    SERVICE_ID   NAME    TRUE_CACHE_SERVICE
    ----------   ------  ----------------
            29   SALES   SALES_TC

**Related Topics**

  * [Configuring True Cache Database Application Services Manually](configuring-true-cache-database-application-services-manually.md#GUID-AA56E2C9-CE4B-403C-99B9-9ADC44305E4B "To use True Cache with the JDBC Thin driver, for each primary database application service that you want to cache, create a corresponding True Cache database application service.")
  * [V$ACTIVE_SERVICES](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/oracle-database/23&id=REFRN-GUID-488BCD15-3125-4CD1-BE26-9E5CA6BC8AE9)


[← Previous](deploying-true-cache-oracle-rac-primary-database.md)

[Next →](verifying-true-cache-configuration.md)
