 

## Using the Automatic Workload Repository for True Cache {#ODBTC-GUID-3F7BD0AE-A891-46B4-9C9E-4EDA98DBFCD9}

You can use the Automatic Workload Repository (AWR) to gather performance statistics for True Cache.

AWR snapshots for True Cache are enabled by default. Snapshots are automatically captured hourly.

You can also manually create a snapshot. For example:
    
    
    // Create snapshots (snap_id 0 means failure)
    SELECT DBMS_WORKLOAD_REPOSITORY.CREATE_SNAPSHOT FROM dual;

To generate an AWR snapshot report, complete the following steps on either the primary database or the True Cache:

  1. Go to the `$ORACLE_HOME` directory and run the following script: 

     * On True Cache: 
        
                @?/rdbms/admin/awrrpt.sql

     * On the primary database:
        
                @?/rdbms/admin/awrrpti.sql

  2. Specify whether you want an HTML or a text report. For example:
    
        Enter value for report_type: text

The output displays a list of available database identifiers and instance numbers.

  3. If you're generating the report on the primary database, enter the database identifier (`dbid)` and instance number (`inst_num)` for True Cache. For example: 
    
        Enter value for dbid: 3309173529
    Using 3309173529 for database Id
    Enter value for inst_num: 1

To get the `dbid` for True Cache, run the following query on the True Cache: 
    
        SELECT DBMS_WORKLOAD_REPOSITORY.GET_AWR_ID() FROM dual;

This step is not required if you're generating the report on True Cache because the `awrrpt.sql` script automatically derives the `dbid` of the local node. 

  4. Specify the number of days for which you want to list snapshot IDs. For example:
    
        Enter value for num_days: 2

The output displays a list of existing snapshots for the specified time range.

  5. Specify beginning and ending snapshot IDs for the workload repository report. For example:
    
        Enter value for begin_snap: 150
    Enter value for end_snap: 160
    

  6. Enter a report name or accept the default report name. For example:
    
        Enter value for report_name: 
    Using the report name awrrpt_1_150_160
    




**Related Topics**

  * [Automatic Workload Repository (AWR)](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/oracle-database/23&id=TGDBA-GUID-EF58B2F0-E380-4783-9592-A67F42D73997)
  * [Managing Snapshots](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/oracle-database/23&id=TGDBA-GUID-144711F9-85AE-4281-B548-3E01280F9A56)
  * [Generating an AWR Report Using the Command-Line Interface](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/oracle-database/23&id=TGDBA-GUID-4B5CB76B-7A8E-4A31-8850-1BB77B13EEE2)
  * [DBMS_WORKLOAD_REPOSITORY](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/oracle-database/23&id=ARPLS-GUID-6FFD52F0-BB67-44E0-A95F-A81E4125547D)


