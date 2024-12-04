[Previous](database-smart-flash-cache.md) [Next](deploying-oracle-true-
cache-oracle-global-data-services.md) JavaScript must be enabled to
correctly display this content

  1. [Oracle True Cache User's Guide](index.md)
  2. Deleting True Cache

## 8 Deleting True Cache

You can delete True Cache with Oracle DBCA or manually.

### 8.1 Using Oracle DBCA to Delete True Cache

You can use Oracle DBCA to clean up the True Cache services and delete True
Cache.

#### 8.1.1 Cleaning Up the True Cache Services from the Primary Database

To delete True Cache, you need to clean up its corresponding services on the
primary database.

Run the following command on the primary database to delete all the services
that correspond to the True Cache that you're planning to delete.

    
    
    ORACLE_HOME/bin/dbca -configureDatabase -cleanupTrueCacheInstanceService -sourceDB primary_sid_or_db_unique_name -trueCacheConnectString true_cache_easy_connect_string -serviceName primary_service_name -trueCacheServiceName true_cache_service_name -silent

Example:

    
    
    $ORACLE_HOME/bin/dbca -configureDatabase -cleanupTrueCacheInstanceService -sourceDB primdb1i -trueCacheConnectString tc.example.com:1522/tcdb1.example.com -serviceName sales -trueCacheServiceName sales_tc -silent

For descriptions of the parameters, see [configureDatabase](configuring-true-
cache-oracle-dbca.md#GUID-0E86301A-4DBF-4AF1-A85D-20D100C26F28 "The
configureDatabase command configures the primary database for True Cache. Run
this command on the primary database.").

#### 8.1.2 Deleting True Cache from the True Cache Node

Run the following command to delete True Cache from the True Cache node.

    
    
    ORACLE_HOME/bin/dbca -deleteDatabase -sourceDB primary_sid_or_db_unique_name -silent

Example:

    
    
    $ORACLE_HOME/bin/dbca -deleteDatabase -sourceDB tcdb1 -silent

### 8.2 Deleting True Cache Manually

To delete True Cache manually, use the following commands.

    
    
    connect / as SYSDBA;
    
    
    shutdown immediate;
    
    
    startup mount exclusive restrict;
    
    
    drop true cache;

Note:

This does not drop the primary database. It deletes the True Cache `SPFILE`;
drops the True Cache control, standby log, and temporary files; and removes
`LOG_ARCHIVE_DEST` entries from the primary database.


[← Previous](database-smart-flash-cache.md)

[Next →](deleting-true-cache.md)
