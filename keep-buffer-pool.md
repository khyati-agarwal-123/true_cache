 

## KEEP Buffer Pool {#ODBTC-GUID-ABB39450-BCC1-40B7-8B07-B9468B845297}

You can use the `KEEP` buffer pool to keep frequently accessed tables persistent in the buffer pool. 

### Overview of Using the KEEP Buffer Pool with True Cache {#ODBTC-GUID-7398C7CD-2792-4CA9-9C8E-BEF3AA7E0679}

You can assign different objects to the `KEEP` buffer pool on different True Caches. 

At a high level, this involves the following steps:

  1. Configure `DB_KEEP_CACHE_SIZE` on True Cache. 
  2. Assign objects on True Cache to the `KEEP` buffer pool. 



As with the primary database, when an object is assigned to the `KEEP` buffer pool on True Cache, the object's blocks are loaded for a query and then kept in the `KEEP` buffer pool. When new data is inserted into the object on the primary database, that new data is automatically propagated to the `KEEP` buffer pool on True Cache through the redo apply mechanism. 

To propagate direct load data to True Cache, specify the `LOGGING` clause in the corresponding DDL or DML, or alter the corresponding object property to `LOGGING`. The `LOGGING` clause lets you specify whether certain operations will be logged in the redo log file (`LOGGING`) or not (`NOLOGGING`). 

**Related Topics**

  * [Configuring the KEEP Pool](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/oracle-database/23&id=TGDBA-GUID-DE6B6C6E-D947-45FF-B189-F0271B86DB10)
  * [logging_clause](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/oracle-database/23&id=SQLRF-GUID-C4212274-5595-4045-A599-F033772C496E)



#### How True Cache Works with the Primary Database Buffer Cache {#ODBTC-GUID-8ED20D84-2448-4C9E-9FC5-FADBE4DA5D17}

By default, marking something `KEEP` on the primary database also marks it `KEEP` on True Cache. 

To prevent objects that are intended to be `KEEP` objects only on the primary database from filling up the True Cache `KEEP` buffer pool, you can use the `DBMS_CACHEUTIL.TRUE_CACHE_KEEP` procedure to mark objects as `KEEP` on True Cache. `DBMS_CACHEUTIL.TRUE_CACHE_KEEP` takes precedence and overrides objects that are marked as `KEEP` on the primary database. 

Also consider the following points:

  * `ALTER TABLE KEEP` assignments on the primary database are persistent because `ALTER TABLE` is a DDL. The `DBMS_CACHEUTIL.TRUE_CACHE_KEEP` procedure isn't persistent when True Cache restarts. 
  * You can't use the `DBMS_CACHEUTIL.TRUE_CACHE_UNKEEP` procedure to unkeep a primary `ALTER TABLE KEEP` assignment. Instead, either don't configure `DB_KEEP_CACHE_SIZE` on True Cache or assign a different object with `DBMS_CACHEUTIL.TRUE_CACHE_KEEP` on True Cache. 
  * All scans on True Cache use the `CACHE` path instead of the direct (`NOCACHE`) path, except temporary tables that are local to True Cache. 



### Configuring DB_KEEP_CACHE_SIZE on True Cache {#ODBTC-GUID-3C75D40C-4C39-48AC-9B03-6058C22E4CFC}

To configure the `KEEP` buffer pool on True Cache, set the `DB_KEEP_CACHE_SIZE` initialization parameter to a large size (such as 10 GB). 

For example:
    
    
    ALTER SYSTEM SET DB_KEEP_CACHE_SIZE=size SCOPE=BOTH;

**Related Topics**

  * [DB_KEEP_CACHE_SIZE](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/oracle-database/23&id=REFRN-GUID-E304447C-C9A1-4F58-97BE-75566754DA15)
  * [Setting or Changing Initialization Parameter Values](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/oracle-database/23&id=MULTI-GUID-4FFA4EB6-F69F-4ACE-B88F-5FBF346B516F)



### Assigning Objects to the KEEP Buffer Pool for True Cache {#ODBTC-GUID-E0BBB1DB-32B6-428F-A567-2169DA48C8B4}

After configuring the `KEEP` buffer pool, assign the object to the `KEEP` buffer pool for True Cache. 

To do this, call the `DBMS_CACHEUTIL.TRUE_CACHE_KEEP()` procedure on True Cache. 

Example: Nonpartitioned Object
    
    
    EXECUTE DBMS_CACHEUTIL.TRUE_CACHE_KEEP('SYS', 'TABLE1');

Example: Partition of a Partitioned Object
    
    
    EXECUTE DBMS_CACHEUTIL.TRUE_CACHE_KEEP('SYS', 'TABLE2', 'TABLE2_PART1');

#### TRUE_CACHE_KEEP Procedure {#ODBTC-GUID-83A5F680-9F23-4A62-AA63-09B7BB26F34D}

When you call this procedure on True Cache, it assigns the object to the `KEEP` buffer pool on that cache. 

To use this procedure, the `DB_KEEP_CACHE_SIZE` initialization parameter must be configured on True Cache. 

**Syntax**
    
    
    DBMS_CACHEUTIL.TRUE_CACHE_KEEP(
       schema                 IN VARCHAR2,
       obj                    IN VARCHAR2,
       partition              IN VARCHAR2 := NULL);
    

**Parameters**

**Table: TRUE_CACHE_KEEP Procedure Parameters**

Parameter | Description  
---|---  
schema | The name of the schema for the object.  
obj | The name of the object.  
partition | If the object is not partitioned, then this is NULL. If the object is partitioned, use the name of the partition segment. If it's a composited partitioned object, use the name of the subpartition segment.  
  
**Related Topics**

  * [TRUE_CACHE_KEEP Procedure](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/oracle-database/23/odbtc&id=ARPLS-GUID-83A5F680-9F23-4A62-AA63-09B7BB26F34D)



### Removing an Object's KEEP Buffer Pool Assignment for True Cache {#ODBTC-GUID-D288770D-1A23-4742-994E-0E8B673EBDCA}

You can use `DBMS_CACHEUTIL.TRUE_CACHE_UNKEEP()` to remove objects from the `KEEP` buffer pool. 

Note that the block is not removed immediately. Instead, it will be naturally aged out as new blocks for other objects are brought into the `KEEP` buffer pool. Also, the `KEEP` buffer pool assignment on True Cache is only remembered while True Cache is up. 

Example: Nonpartitioned Object
    
    
    EXECUTE DBMS_CACHEUTIL.TRUE_CACHE_UNKEEP('SYS', 'TABLE1');

Example: Partition of a Partitioned Object
    
    
    EXECUTE DBMS_CACHEUTIL.TRUE_CACHE_UNKEEP('SYS', 'TABLE2', 'TABLE2_PART1');

#### TRUE_CACHE_UNKEEP Procedure {#ODBTC-GUID-DA67D7B6-A287-4B1A-8BDA-D94E3588AFEA}

When an object on True Cache no longer needs to be in the `KEEP` buffer pool, use this procedure to remove the object's `KEEP` assignment. 

**Syntax**
    
    
    DBMS_CACHEUTIL.TRUE_CACHE_UNKEEP(
       schema                 IN VARCHAR2,
       obj                    IN VARCHAR2,
       partition              IN VARCHAR2 := NULL);
    

**Parameters**

**Table: TRUE_CACHE_UNKEEP Procedure Parameters**

Parameter | Description  
---|---  
schema | The name of the schema for the object.  
obj | The name of the object.  
partition | If the object is not partitioned, then this is NULL. If the object is partitioned, use the name of the partition segment. If it's a composited partitioned object, use the name of the subpartition segment.  
  
**Related Topics**

  * [TRUE_CACHE_UNKEEP Procedure](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/oracle-database/23/odbtc&id=ARPLS-GUID-DA67D7B6-A287-4B1A-8BDA-D94E3588AFEA)



### Viewing a List of KEEP Objects on True Cache {#ODBTC-GUID-A17EF2EA-7855-4388-956B-5E1EDABAAEDA}

Use the `V$TRUE_CACHE_KEEP` view to see which objects are assigned to the `KEEP` buffer cache for True Cache. 

Example 1
    
    
    SELECT * FROM v$true_cache_keep;
    
    TS_NUMBER  DATA_OBJECT_ID  CON_ID
    ---------- --------------  ------
             5          72948       3
             5          72950       3

Example 2
    
    
    SELECT owner as schema, object_name as keepobj, subobject_name as partition, o.data_object_id
    FROM dba_objects o, sys_objects so, v$true_cache_keep vtck
    WHERE o.data_object_id = so.object_id
      AND vtck.con_id = sys_context('USERENV', 'CON_ID')
      AND so.ts_number=vtck.ts_number
      AND o.data_object_id = vtck.data_object_id;
    
    SCHEMA  KEEPOBJ  PARTITION     DATA_OBJECT_ID
    ------  -------  ------------  --------------
    SYS     TABLE1                          72948
    SYS     TABLE2   TABLE2_PART1           72950

#### V$TRUE_CACHE_KEEP Columns {#ODBTC-GUID-23DBFF6B-4F49-4946-9EEE-EDD71CDEE41C}

The following table describes the columns in the `V$TRUE_CACHE_KEEP` view. 

Column | Description  
---|---  
TS_NUMBER | The tablespace number.  
DATA_OBJECT_ID | The dictionary object number of the segment that contains the object.  
CON_ID | The root container ID.
