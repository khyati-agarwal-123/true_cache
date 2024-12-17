##  Verifying the True Cache Configuration {#GUID-D00A4443-FA03-4CED-9C42-04046CF0CF7E} 

Verify that True Cache and the database application services are working as expected. 

###  Verifying That True Cache Is Working as Expected {#GUID-2E0C8595-DD6D-4A6B-BA6B-E78786279EF7} 

To verify that True Cache is applying redo and making progress, check the following queries on True Cache. 

  1. Run SQL*Plus on the True Cache. 

``` sqlplus / as SYSDBA ``` 
  2. Enter the following query: 

``` SELECT database_role, open_mode FROM v$database; ``` 

The output should look like this: 

``` DATABASE_ROLE OPEN_MODE \---------------- -------------------- TRUE CACHE READ ONLY WITH APPLY ``` 

If ` OPEN_MODE ` is ` READ ONLY WITH APPLY ` , it means that the True Cache redo apply is actively working. 

  3. Enter the following query multiple times: 

``` SELECT current_scn FROM v$database; ``` 

If ` CURRENT_SCN ` is advancing over time, it means that True Cache is moving forward as expected. 

  4. To find the size of each log file, enter the following query: 

``` SELECT THREAD#, SEQUENCE#, BYTES FROM v$standby_log; ``` 



###  Verifying the Remote Listener Configuration {#GUID-2AB880B8-B8BA-4E16-A924-E9764E907255} 

After the database application service starts on True Cache, use the ` lsnrctl ` command to validate the remote listener services for the True Cache service registration. 

> **note:** For Oracle RAC primary databases, before using the ` lsnrctl ` command, set your ` $ORACLE_HOME ` environment variable to the path for the Oracle Grid Infrastructure home (Grid home). 

The following example shows two True Caches registered with the SCAN listener for an Oracle RAC primary database: 

``` lsnrctl services LISTENER_SCAN1 Service "sales_tc" has 2 instance(s). Instance "tcdb1", status READY, has 1 handler(s) for this service... Handler(s): "DEDICATED" established:0 refused:0 state:ready REMOTE SERVER (DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=tc1.example.com)(PORT=1521))) Instance "tcdb2", status READY, has 1 handler(s) for this service... Handler(s): "DEDICATED" established:0 refused:0 state:ready REMOTE SERVER (ADDRESS=(PROTOCOL=TCP)(HOST=tc2.example.com)(PORT=1521)) ``` 

> **note:** If the True Cache service is not registered with the remote listener, validate the remote listener invited nodes and the True Cache initialization parameters for ` REMOTE_LISTENER ` or ` LISTENER_NETWORKS ` . 

###  Verifying the True Cache and Primary Database Application Services {#GUID-5C4B5B84-075D-40E6-A2FD-C09546BBED8C} 

Verify that all database application services are active on both the primary database and True Cache. 

True Cache 

  1. Run SQL*Plus on True Cache. 

``` sqlplus / as SYSDBA ``` 
  2. Enter the following query: 

``` SELECT service_id, name FROM v$active_services WHERE name='<span class="variable" translate="no">true_cache_service_name</span>'; ``` 

For example: 
    
        SELECT service_id, name FROM v$active_services WHERE name='SALES_TC';
    
    SERVICE_ID  NAME
    ----------  ---------
            28  SALES_TC




Primary Database 

  1. Run SQL*Plus on the primary database. 

``` sqlplus / as SYSDBA ``` 
  2. Enter the following query: 

``` SELECT service_id, name, true_cache_service FROM v$active_services WHERE name='<span class="variable" translate="no">primary_db_service_name</span>'; ``` 

For example: 
    
        SELECT service_id, name, true_cache_service FROM v$active_services WHERE name='SALES';
    
    SERVICE_ID   NAME    TRUE_CACHE_SERVICE
    ----------   ------  ----------------
            29   SALES   SALES_TC




**Related Topics**

  * [ Configuring True Cache Database Application Services Manually ](configuring-true-cache-database-application-services-manually.html#GUID-AA56E2C9-CE4B-403C-99B9-9ADC44305E4B)
  * [ V$ACTIVE_SERVICES ](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/oracle-database/23&id=REFRN-GUID-488BCD15-3125-4CD1-BE26-9E5CA6BC8AE9)


