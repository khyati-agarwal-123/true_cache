##  Monitoring True Cache with the V$TRUE_CACHE View {#GUID-467A231B-F567-4FC1-91AE-0CAEA45195CC} 

Use the ` V$TRUE_CACHE ` view to monitor the overall relationship and health of a True Cache configuration. 

You can query the ` V$TRUE_CACHE ` view on the primary database and True Cache. On True Cache, the view displays a single row for the primary database that it connects to. On the primary database, the view shows one row for each True Cache that's connected to the primary database. Each row displays the status for the True Cache. 

To use this view, enter the following query: 

``` SELECT * FROM v$true_cache; ``` 

The following example shows output on True Cache: 

``` MY_DG_ID REMOTE_DG_ID DEST_ID TRUE_CACHE_NAME PRIMARY_NAME STATUS REMOTE_VERSION CON_ID \---------- ------------ ------- --------------- ------------ ------- -------------- ------ 2976625076 626116455 0 TCDB1 PRIMARYD HEALTHY 23.0.0.0.0 0 ``` 

The following example shows output on a primary database: 

``` MY_DG_ID REMOTE_DG_ID DEST_ID TRUE_CACHE_NAME PRIMARY_NAME STATUS REMOTE_VERSION CON_ID \--------- ------------ ------- --------------- ------------ ------- -------------- ------ 626116455 2976625076 2 TCDB1 PRIMARYD HEALTHY 23.0.0.0.0 0 626116455 2499211322 3 TCDB2 PRIMARYD HEALTHY 23.0.0.0.0 0 ``` 

###  V$TRUE_CACHE Columns {#GUID-CAEC2C9F-A9C6-404A-9EBF-E82F11435843} 

The following table describes the columns in the ` V$TRUE_CACHE ` view. 

**Table: V$TRUE_CACHE Columns** 

Column  |  Description   
---|---  
` MY_DG_ID ` |  The Data Guard ID ( ` DGID ` ) that uniquely identifies the True Cache or primary database in the True Cache configuration.   
` REMOTE_DG_ID ` |  On True Cache, this is the primary databaseâs ` DGID ` . On the primary database, it's the True Cache ` DGID ` .   
` DEST_ID ` |  On True Cache, this is always ` 0 ` . On the primary database, it's the corresponding destination ID ( ` n  ` ) of the ` LOG_ARCHIVE_DEST_  n  ` initialization parameter. The ` LOG_ARCHIVE_DEST_  n  ` initialization parameter specifies the network name of the True Cache so that the primary database knows where to send online redo log blocks.   
` TRUE_CACHE_NAME ` |  The unique name of the True Cache ( ` DB_UNIQUE_NAME ` in the ` PFILE ` ).   
` PRIMARY_NAME ` |  The unique name of the primary database ( ` DB_UNIQUE_NAME ` in the ` PFILE ` ).   
` STATUS ` |  Describes the current status of the True Cache. If no issues are found, it displays ` HEALTHY ` . Otherwise, it displays an error description. For example: ` ORA-01034: The Oracle instance is not available for use. Start the instance. `  
` REMOTE_VERSION ` |  On True Cache, this is the database version of the primary database. On the primary database, this is the database version of the True Cache.   
` CON_ID ` |  The root container ID.   
  
**Related Topics**

  * [ V$TRUE_CACHE ](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/oracle-database/23&id=REFRN-GUID-1F916DF5-ECEB-4DD0-AFFA-BE663B6FAFA7)


