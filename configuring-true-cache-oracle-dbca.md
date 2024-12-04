[Previous](overview-true-cache-configuration.md) [Next](configuring-true-
cache-manually.md) JavaScript must be enabled to correctly display this
content

  1. [Oracle True Cache User's Guide](index.md)
  2. [Configuring True Cache](configuring-true-cache.md)
  3. Configuring True Cache with Oracle DBCA

## 2.2 Configuring True Cache with Oracle DBCA

The simplest way to configure a True Cache environment is to use Oracle
Database Configuration Assistant (Oracle DBCA).

Oracle DBCA is an assistant tool that comes with Oracle Database to simplify
the process of creating, configuring, and managing Oracle databases and True
Caches.

Note:

True Cache for Oracle Database Free has a simplified configuration process.
Get started with the Oracle Database Free Installation Guide for your
platform. See [Configuring True Cache on Oracle Database Free
(Linux)](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/oracle-database/23&id=XEINL-
GUID-DB0B52B9-F18C-4101-807A-CD0C3B5679D5) or [Configuring True Cache on
Oracle Database Free
(Windows)](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/oracle-
database/23&id=XEINW-GUID-09A22E0D-EB2D-4785-9471-A0FEE2D04605).

### 2.2.1 Prerequisites

Before configuring True Cache, complete the following prerequisites.

  * Set up a network path to access the primary (source) database with an Easy Connect (EZConnect) string from the True Cache node.
  * Install the Oracle Database software on the True Cache nodes.
  * Ensure that a primary database is running on the primary node in archive logging (`ARCHIVELOG)` mode.

The primary database must be in `ARCHIVELOG` mode to ship redo log files to
the True Cache node. Oracle DBCA verifies that the primary database is in
`ARCHIVELOG` mode.

If the primary database is not in `ARCHIVELOG` mode, restart it in mount mode,
run the `ALTER DATABASE ARCHIVELOG` command, and open the primary database
again.

  * Don't set `LOG_ARCHIVE_CONFIG` and `LOG_ARCHIVE_DEST_n` on the primary database. True Cache automatically configures these for the primary database.

### 2.2.2 Creating True Cache with DBCA

Follow these steps to create True Cache with Oracle DBCA.

  1. Copy the password file, and optionally the Transparent Data Encryption (TDE) wallet, from the primary database to the True Cache node. You can use either of the following methods:

     * Manually copy the primary database's password file (and optional TDE wallet) to any location on the True Cache node.
     * Run a DBCA command on the primary database to package the password file (and optional TDE wallet) into a configuration BLOB file. Then copy the file to the True Cache node and ensure that the file is owned by the Oracle Home user.

Use the following command to prepare the configuration BLOB file on the
primary database:

        
                ORACLE_HOME/bin/dbca -configureDatabase -prepareTrueCacheConfigFile -sourceDB primary_db_sid_or_db_unique_name -trueCacheBlobLocation primary_db_config_blob_path -silent

Example:

        
                $ORACLE_HOME/bin/dbca -configureDatabase -prepareTrueCacheConfigFile -sourceDB primdb1i -trueCacheBlobLocation /tmp/blob_loc2 -silent

For descriptions of the parameters, see [configureDatabase](configuring-true-
cache-oracle-dbca.md#GUID-0E86301A-4DBF-4AF1-A85D-20D100C26F28 "The
configureDatabase command configures the primary database for True Cache. Run
this command on the primary database.").

  2. On the True Cache node, run the `-createTrueCache` command to complete the True Cache configuration and start True Cache. 

Choose one of the following command formats, depending on the method that you
used to copy the primary database's password file (and optional TDE wallet) to
the True Cache node in step 1.

     * If you manually copied the password file (and optional TDE wallet), use this command:
        
                ORACLE_HOME/bin/dbca -createTrueCache -gdbName true_cache_global_name -sid true_cache_sid -sourceDBConnectionString primary_db_easy_connect_string -passwordFileFromSourceDB password_file_path -sgaTargetInMB sga_memory_size -pgaAggregateTargetInMB pga_memory_size -silent

Example using a password file:

        
                $ORACLE_HOME/bin/dbca -createTrueCache -gdbName tcdb1 -sid tcdb1 -sourceDBConnectionString primary.example.com:1522/primdb1i.example.com -passwordFileFromSourceDB /tmp/password_loc2/orapwddbmc1 -sgaTargetInMB 20000 -pgaAggregateTargetInMB 4000 -silent

     * If you created the configuration BLOB file, use this command:
        
                ORACLE_HOME/bin/dbca -createTrueCache -gdbName true_cache_global_name -sid true_cache_sid -sourceDBConnectionString primary_db_easy_connect_string -trueCacheBlobFromSourceDB true_cache_config_blob_path -sgaTargetInMB sga_memory_size -pgaAggregateTargetInMB pga_memory_size -tdeWalletLoginType AUTO_LOGIN -listeners listener_name -silent

Example using a TDE wallet:

        
                $ORACLE_HOME/bin/dbca -createTrueCache -gdbName tcdb1 -sid tcdb1 -sourceDBConnectionString primary.example.com:1522/primdb1i.example.com -trueCacheBlobFromSourceDB /tmp/blob_loc2/blob.tar.gz -sgaTargetInMB 20000 -pgaAggregateTargetInMB 4000 -tdeWalletLoginType AUTO_LOGIN -listeners LISTENER -silent

For descriptions of the parameters, see [createTrueCache](configuring-true-
cache-oracle-dbca.md#GUID-7F7C04FD-6C41-4E8E-A479-A49AD6EAC848 "The
createTrueCache command configures True Cache. Run this command on the True
Cache node.").

Note:

For Oracle RAC primary databases, set the `-sourceDBConnectionString`
parameter to `SCAN:port/service_name`.

**Related Topics**

  * [Rerunning Oracle DBCA After Creating True Cache](rerunning-oracle-dbca.md#GUID-EAB50DC8-DD91-4343-B937-1E45BD89477D "If you need to rerun Oracle DBCA after creating True Cache, clean up the True Cache node first.")

### 2.2.3 Configuring True Cache Database Application Services with DBCA

To use True Cache with the JDBC Thin driver, for each primary database
application service that you want to cache, create a corresponding True Cache
database application service.

This makes it easy for applications to switch an existing JDBC connection from
a primary database to True Cache without having to change the JDBC connection
URL. This is possible with the 23ai JDBC Thin driver by setting the `ReadOnly`
parameter of the connection to `TRUE` or `FALSE`.

On the primary database, complete the following steps:

  1. Configure the remote listener.

Note:

Check to see if Valid Node Checking for Registration (VNCR) is configured for
the listener. If so, add True Cache to the
`REGISTRATION_INVITED_NODES_LISTENER` parameter in the `listener.ora` file of
the remote listener.

Note:

For Oracle RAC primary databases, add the True Cache node to the invited node
list for the SCAN listener by using the `srvctl` command line utility. (Don't
manually edit the `listener.ora` file as the grid owner user.)

For example:

    
        srvctl modify scan_listener -invitednodes true_cache_host -endpoints TCP:port

  2. Create and start a primary database application service to use with the True Cache service, and then verify that the service is running.

Choose one of the following options based on the primary database
configuration:

     * For single instance primary databases, use the `DBMS_SERVICE` PL/SQL package. For services that are specific to a pluggable database (PDB), connect to the specific PDB, or set the correct PDB container in your session before starting the service using `DBMS_SERVICE`.

Here's an example using the `DBMS_SERVICE` package for a single instance
primary database:

        
                BEGIN
           DBMS_SERVICE.CREATE_SERVICE('SALES', 'SALES');
           DBMS_SERVICE.START_SERVICE('SALES');
        END;
        /
        PL/SQL procedure successfully completed.
        
        
                SELECT service_id, name, true_cache_service FROM v$active_services WHERE name='SALES';
        
        SERVICE_ID NAME               TRUE_CACHE_SERVICE
        ---------- ------------------ ------------------
                 6 SALES
        
        // TRUE_CACHE_SERVICE will be filled in by this (dbca -configureTrueCacheInstanceService) command

     * For Oracle RAC primary databases, use the `srvctl` command line utility to add the primary database service. Start the service on the primary instance. Use `srvctl` only for Oracle Clusterware-managed instances. 
        
                srvctl add service -db primary_db_unique_name -service primary_db_service_name -preferred primary_db_instance_list -pdb primary_pdb_name

For example:

        
                srvctl add service -db primdb1i -service sales -preferred primdb1i1,primdb1i2 -pdb sales_pdb

  3. Run the `dbca -configureDatabase` command with the `-configureTrueCacheInstanceService` parameter to configure True Cache. 

This configures the True Cache database application service on the primary
database and then starts the True Cache service on True Cache. Run this
command for each True Cache database application service.

Note:

The primary database application service must already exist before you run
this command.

Note:

For a uniform True Cache configuration with multiple True Caches serving the
same service, DBCA starts the service on the first True Cache and then you
manually start the service on the other True Caches.

    
        ORACLE_HOME/bin/dbca -configureDatabase -configureTrueCacheInstanceService -sourceDB primary_db_sid_or_db_unique_name -trueCacheConnectString true_cache_easy_connect_string -trueCacheServiceName true_cache_service_name -serviceName primary_db_service_name -pdbName primary_pdb_name -silent

Example:

    
        $ORACLE_HOME/bin/dbca -configureDatabase -configureTrueCacheInstanceService -sourceDB primdb1i -trueCacheConnectString tc.example.com:1522/tcdb1.example.com -trueCacheServiceName sales_tc -serviceName sales -pdbName sales_pdb -silent

For descriptions of the parameters, see [configureDatabase](configuring-true-
cache-oracle-dbca.md#GUID-0E86301A-4DBF-4AF1-A85D-20D100C26F28 "The
configureDatabase command configures the primary database for True Cache. Run
this command on the primary database.").

  4. After the database application service starts on True Cache, use the `lsnrctl` command to validate the remote listener services for the True Cache service registration. 

See [Verifying the Remote Listener Configuration](verifying-true-cache-
configuration.md#GUID-2AB880B8-B8BA-4E16-A924-E9764E907255 "After the
database application service starts on True Cache, use the lsnrctl command to
validate the remote listener services for the True Cache service
registration.").

  5. For a uniform True Cache configuration with multiple True Caches serving the same service, start the service on all additional True Caches. For example:
    
        EXEC DBMS_SERVICE.START_SERVICE('SALES_TC');

**Related Topics**

  * [CREATE_SERVICE Procedure](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/oracle-database/23&id=ARPLS-GUID-386E183E-D83C-48A7-8BA3-40248CFB89F4)
  * [MODIFY_SERVICE Procedure](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/oracle-database/23&id=ARPLS-GUID-E790BE65-023F-4016-8A5F-DF43B324834C)
  * [srvctl modify scan_listener](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/oracle-database/23&id=RACAD-GUID-8E829323-D14F-40E4-AE39-CB23885EED85)
  * [Uniform Configuration](overview-true-cache-configuration.md#GUID-CC75EDE3-11B2-447F-ADD0-9ED5E83D22D3 "In a uniform configuration, you can deploy multiple, identical True Caches that use the same True Cache database application service.")
  * [Rerunning Oracle DBCA After Configuring True Cache Database Application Services](rerunning-oracle-dbca.md#GUID-EBB7FEC6-12BB-4DE8-8603-DD1C91A260FC "If you need to rerun Oracle DBCA after configuring True Cache database application services, you might need to delete the True Cache service from the primary pluggable database \(PDB\) using the DBMS_Service package.")

### 2.2.4 Oracle DBCA Commands and Parameters for True Cache

Use the following Oracle DBCA commands and parameters to configure True Cache.

#### 2.2.4.1 configureDatabase

The `configureDatabase` command configures the primary database for True
Cache. Run this command on the primary database.

Parameters

For True Cache, use the `dbca` `-configureDatabase` command with the following
syntax.

Note:

This table lists the `configureDatabase` parameters that are specific to True
Cache. For the full syntax and parameters, see
[configureDatabase](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/oracle-
database/23&id=MULTI-GUID-23781683-79F3-437B-ADB3-17BD1ED5BE5D) in the Oracle
Multitenant Administrator's Guide.

Table 2-1 configureDatabase Parameters

Parameter | Required/Optional | Description  
---|---|---  
\--prepareTrueCacheConfigFile | Required for True Cache | Use this option to prepare a configuration BLOB file that contains the primary database's password file or wallet. Enter the following additional parameters for this option: -sourceDB: Enter the primary database system identifier (SID) or database unique name (DB_UNIQUE_NAME). -tdeWalletPassword: If the primary database uses a Transparent Data Encryption (TDE) wallet, enter the password for the wallet. This parameter is optional. -trueCacheBlobLocation: Enter the path where you want to save the configuration BLOB file on the primary database. This parameter is optional.  
-configureTrueCacheInstanceService | Required for True Cache | Use this option to configure the True Cache database application service on the primary database and start the service on True Cache. Enter the following additional parameters for this option: -serviceName: Enter the primary database application service name. -sourceDB: For a single instance database, enter the primary database SID. For an Oracle RAC database, enter the database unique name (DB_UNIQUE_NAME). -trueCacheConnectString: Enter the Easy Connect (EZConnect) string to connect to True Cache. Example: host:port/service_name -trueCacheServiceName: Enter a name for the True Cache database application service. -pdbName: Enter the primary pluggable database (PDB) name. This parameter is required to create the True Cache service for a PDB. In this case serviceName is an existing PDB service name in the primary database.  
-cleanupTrueCacheInstanceService | Required for True Cache | Use this option to remove the True Cache database application services from the primary database configuration if, for example, you delete True Cache. Enter the following additional parameters for this option: -serviceName: Enter the primary database application service name. -sourceDB: Enter the primary database SID or database unique name (DB_UNIQUE_NAME). -trueCacheConnectString: Enter the Easy Connect (EZConnect) string to connect to True Cache. Example: host:port/service_name -trueCacheServiceName: Enter a name for the True Cache database application service.  
  
#### 2.2.4.2 createTrueCache

The `createTrueCache` command configures True Cache. Run this command on the
True Cache node.

Syntax and Parameters

Use the `dbca` `-createTrueCache` command with the following syntax:

    
    
    dbca -createTrueCache
       -dbUniqueName true_cache_unique_name | -gdbName true_cache_global_name
       -sourceDBConnectionString primary_db_easy_connect_string
       -trueCacheBlobFromSourceDB true_cache_config_blob_path | -passwordFileFromSourceDB password_file_path
          [-tdeWalletFromSourceDB tde_wallet_path]
       [-createListener new_database_listener]
       [-datafileDestination true_cache_control_file_path
       [-initParams initialization_parameters_list
          [-initParamsEscapeChar initialization_parameters_escape_character]]
       [-listeners listener_list]
       [-pgaAggregateTargetInMB pga_memory_size]
       [-sgaTargetInMB sga_memory_size]
       [-sid true_cache_sid]
       [-sourceTdeWalletPassword primary_db_wallet_password]
       [-tdeWalletLoginType {PASSWORD | AUTO_LOGIN | LOCAL_AUTO_LOGIN}]
       [-tdeWalletRoot tde_wallet_root_init_parameter]
       [-useWalletForDBCredentials {true | false}
          -dbCredentialsWalletLocation wallet_files_directory
          [-dbCredentialsWalletPassword wallet_account_password]]

Table 2-2 createTrueCache Parameters

Parameter | Required/Optional | Description  
---|---|---  
-dbUniqueName true_cache_unique_name or -gdbName true_cache_global_name | Required | Enter either the unique name for this True Cache or the global database name.  
-sourceDBConnectionString primary_db_easy_connect_string | Required | Enter the Easy Connect (EZConnect) string to connect to the primary database. Example: host:port/service_name Note: For Oracle RAC primary databases, set the -sourceDBConnectionString parameter to SCAN:port/service_name.  
-trueCacheBlobFromSourceDB true_cache_config_blob_path or -passwordFileFromSourceDB password_file_path | Required | Enter one of the following: The full path and file name for the configuration BLOB file that contains the primary database's password file or wallet. This is the path where the file is located on the True Cache node. The path to the primary database's password file that was copied to the True Cache node. If you use -passwordFileFromSourceDB, you can also enter the following additional parameter: -tdeWalletFromSourceDB: Enter the path to the primary database's Transparent Data Encryption (TDE) wallet file that was copied to the True Cache node. You can copy and use the TDE wallet file only if the primary (source) database has TDE enabled. Otherwise, the wallet file isn't required.  
-createListener new_database_listener | Optional | Enter a new database listener to be created and to register the database in the form LISTENER_NAME:PORT.  
-datafileDestination true_cache_control_file_path | Optional | Enter the location where the True Cache control file, standby redo log groups, and temporary data files are to be stored. If Oracle Automatic Storage Management (ASM) storage is used for True Cache, enter the disk group name for -datafileDestination and use the following additional parameter: -useOMF: Enter true to use Oracle-Managed Files (OMF). Otherwise, enter false.  
-initParams initialization_parameters_list | Optional | Enter a comma-separated list of name=value pairs with additional initialization parameter values for this True Cache. You can also provide the -initParamsEscapeChar parameter for using a specific escape character between multiple values of an initialization parameter. If an escape character is not specified, backslash (\\) is used as the default escape character.  
-listeners listeners_list | Optional | If one or more listeners already exist, enter a comma-separated list of existing listeners that the database can be configured with. If you don't specify the existing listeners, DBCA creates a new listener.  
-pgaAggregateTargetInMB pga_memory_size | Optional | Enter a value in MB for the target aggregate Program Global Area (PGA) memory to make available to all server processes that are attached to this True Cache.  
-sgaTargetInMB sga_memory_size | Optional | Enter a value in MB for the System Global Area (SGA) memory size for this True Cache.  
-sid true_cache_sid | Optional | Enter the system identifier (SID) for this True Cache.  
-sourceTdeWalletPassword primary_db_wallet_password | Optional | If the primary database uses a TDE wallet, enter the password for the wallet.  
-tdeWalletLoginType {PASSWORD \| AUTO_LOGIN \| LOCAL_AUTO_LOGIN} | Optional | Oracle recommends the AUTO_LOGIN or LOCAL_AUTO_LOGIN wallet for True Cache to avoid having to manually open a password wallet for each True Cache startup. To learn more about wallet types, see About Oracle Database Wallets  
-tdeWalletRoot tde_wallet_root_init_parameter | Optional | Enter the location for the True Cache TDE wallet root initialization parameter, which specifies where to place the wallet for True Cache.  
-useWalletForDBCredentials {true \| false} | Optional | If the primary database uses Oracle Wallet for database credentials, enter true. The default is false. If you enter true, also enter the following additional parameters: -dbCredentialsWalletLocation: Enter the path of the directory that contains the Oracle Wallet files. -dbCredentialsWalletPassword: Enter the password for the Oracle Wallet account to open the wallet with auto-login disabled. This parameter is optional.  
  
**Related Topics**

  * [createTrueCache](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/oracle-database/23/odbtc&id=MULTI-GUID-7F7C04FD-6C41-4E8E-A479-A49AD6EAC848)


[← Previous](overview-true-cache-configuration.md)

[Next →](configuring-true-cache-oracle-dbca.md)
