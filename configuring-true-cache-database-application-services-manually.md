 

## Configuring True Cache Database Application Services Manually {#ODBTC-GUID-AA56E2C9-CE4B-403C-99B9-9ADC44305E4B}

To use True Cache with the JDBC Thin driver, for each primary database application service that you want to cache, create a corresponding True Cache database application service.

This makes it easy for applications to switch an existing JDBC connection from a primary database to True Cache without having to change the JDBC connection URL. This is possible with the 23ai JDBC Thin driver by setting the `ReadOnly` parameter of the connection to `TRUE` or `FALSE`. 

### Create Database Application Services on the Primary Database {#ODBTC-GUID-1F990837-4B60-4119-8119-AFC87F77EC38}

You can associate one primary database service with one True Cache service.

From the primary database, create or modify the True Cache and primary database services using one of the following methods.

True Cache and Single Instance Primary Databases

For True Cache and single instance primary databases, use the `DBMS_SERVICE` PL/SQL package.For services that are specific to a pluggable database (PDB), connect to the specific PDB, or set the correct PDB container in your session before starting the service using `DBMS_SERVICE`.

Here's an example of creating these two services using the `DBMS_SERVICE` package for a single instance primary database: 
    
    
    DECLARE
        db_params dbms_service.svc_parameter_array;
    BEGIN
       -- create a database application service for True Cache called SALES_TC using SALES_TC tnsname
       DBMS_SERVICE.CREATE_SERVICE('SALES_TC', 'SALES_TC', db_params);
    
       -- create a database application service SALES for primary database using SALES tnsname and associate it to the SALES_TC service name using TRUE_CACHE_SERVICE attribute
       db_params('true_cache_service') := 'SALES_TC';
       DBMS_SERVICE.CREATE_SERVICE('SALES', 'SALES', db_params);
    
       -- or, modify an already existing primary database application service called SALES to associate it to the SALES_TC service name using the TRUE_CACHE_SERVICE attribute
       db_params('true_cache_service') := 'SALES_TC';
       DBMS_SERVICE.MODIFY_SERVICE('SALES', db_params);
    
    END;

Oracle RAC Primary Databases

For Oracle RAC primary databases, use the `srvctl` command line utility to add the services. Start the service on the primary instance. Use `srvctl` only for Oracle Clusterware-managed instances.

To create both a True Cache service and the primary database service:
    
    
    srvctl add service -db primary_db_unique_name -service true_cache_service_name -preferred primary_db_instance_list -pdb primary_pdb_name
    
    
    srvctl add service -db primary_db_unique_name -service primary_db_service_name -preferred primary_db_instance_list -pdb primary_pdb_name -true_cache_service true_cache_service_name

For example:
    
    
    srvctl add service -db primdb1i -service sales_tc -preferred primdb1i1,primdb1i2 -pdb sales_pdb
    srvctl add service -db primdb1i -service sales -preferred primdb1i1,primdb1i2 -pdb sales_pdb -true_cache_service sales_tc

To create a True Cache service for an existing primary database service (`db_service_name`): 
    
    
    srvctl add service -db primary_db_unique_name -service true_cache_service_name -preferred primary_db_instance_list -pdb primary_pdb_name
    
    
    srvctl modify service -db primary_db_unique_name -service primary_db_service_name -true_cache_service true_cache_service_name

For example:
    
    
    srvctl add service -db primdb1i -service sales_tc -preferred primdb1i1,primdb1i2 -pdb sales_pdb
    srvctl modify service -db primdb1i -service sales -true_cache_service sales_tc

Note:

Also disable the True Cache service on the cluster where it was added to prevent errors when stopping and restarting the primary database services. For example:
    
    
    srvctl start service -d primdb1i -s sales_tc
    
    
    srvctl stop service -d primdb1i -s sales_tc
    
    
    srvctl disable service -d primdb1i -s sales_tc

This doesn't affect standby databases or True Caches because they run outside the cluster where the True Cache service is disabled. The disabled status is not stored in the primary dictionary and `DBA_SERVICES`, but only in Cluster Ready Services (CRS), and only in the cluster where the True Cache service was added. Other databases outside the cluster can start the service with `DBMS_SERVICE.START_SERVICE` and aren't affected by this setting. 

**Related Topics**

  * [CREATE_SERVICE Procedure](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/oracle-database/23&id=ARPLS-GUID-386E183E-D83C-48A7-8BA3-40248CFB89F4)
  * [MODIFY_SERVICE Procedure](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/oracle-database/23&id=ARPLS-GUID-E790BE65-023F-4016-8A5F-DF43B324834C)
  * [Server Control Utility Reference](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/oracle-database/23&id=RACAD-GUID-3ED4DBCE-A148-462B-8A79-534A3F0D6E7D)



### Verify That the Database Application Services Are Created {#ODBTC-GUID-D2D78FB7-C59B-42AB-AB57-3DF953078597}

After you create services on the primary database, True Cache automatically inherits their definitions. Using SQL*Plus, make sure that you can see the same results on both the primary database and True Cache.

For example:
    
    
    connect / as SYSDBA
    
    
    SELECT name, true_cache_service FROM DBA_SERVICES WHERE name='SALES' or name='SALES_TC';
    
    NAME        TRUE_CACHE_SERVICE
    --------    ------------------
    SALES_TC
    SALES       SALES_TC

### Start the Database Application Services {#ODBTC-GUID-DA74EF33-517E-4327-9531-4EFF0ED46AF8}

Using SQL*Plus, start the database application services on both the primary database and True Cache.

Example 2-1 True Cache
    
    
    connect / as SYSDBA
    
    
    SELECT database_role FROM v$database;
    
    DATABASE_ROLE
    -------------
    TRUE CACHE
    
    
    EXEC DBMS_SERVICE.START_SERVICE('SALES_TC');
    
    
    SELECT service_id, name FROM v$active_services WHERE name='SALES_TC';
    
    SERVICE_ID  NAME
    ----------  ---------
            28  SALES_TC

Example 2-2 Primary Database
    
    
    connect / as SYSDBA
    
    
    SELECT database_role FROM v$database;
    
    DATABASE_ROLE
    -------------
    PRIMARY
    
    
    EXEC DBMS_SERVICE.START_SERVICE('SALES');
    
    
    SELECT service_id, name, true_cache_service FROM v$active_services WHERE name='SALES';
    
    SERVICE_ID   NAME    TRUE_CACHE_SERVICE
    ----------   ------  ----------------
            29   SALES   SALES_TC

**Related Topics**

  * [Verify That the Database Application Services Are Created](configuring-true-cache-database-application-services-manually.html#GUID-D2D78FB7-C59B-42AB-AB57-3DF953078597 "After you create services on the primary database, True Cache automatically inherits their definitions. Using SQL*Plus, make sure that you can see the same results on both the primary database and True Cache.")
  * [START_SERVICE Procedure](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/oracle-database/23&id=ARPLS-GUID-140B93AC-9021-4091-B797-7CA3AAB446FE)
  * [V$ACTIVE_SERVICES](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/oracle-database/23&id=REFRN-GUID-488BCD15-3125-4CD1-BE26-9E5CA6BC8AE9)
  * [V$DATABASE](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/oracle-database/23&id=REFRN-GUID-C62A7B96-2DD4-4E70-A0D9-26EE4BFBE256)


