##  Creating True Cache Manually {#GUID-8580479A-7DD5-4B64-A3DE-70A6E25654AF} 

Follow these steps to create True Cache manually. 

###  Edit the tnsnames.ora File for True Cache and the Primary Database {#GUID-EA45DDEB-6335-4916-AC67-E81864EDF692} 

Configure the database network connections by editing the ` tnsnames.ora ` file on both True Cache and the primary database. 

On most Linux platforms, you find the ` tnsnames.ora ` file in the ` $ORACLE_HOME/network/admin ` directory. 

You can create this file manually or by using Oracle Net Configuration Assistant (NETCA). 

Add the following information to all primary database and True Cache ` tnsnames.ora ` files if the information isn't already present: 

  * Network names for the primary database and all True Caches. These are needed to send redo to True Cache, and they enable redo shipping to work even if the database application services are not yet created. 
  * All primary database and True Cache database application service names. One True Cache can support multiple database application services that are started simultaneously. 
  * All participating listener network aliases, if you're using a remote listener. 



Here are examples of the entries in the ` tnsnames.ora ` files for True Cache and the primary database: 

``` # True Cache network name tcdb1i = (DESCRIPTION = (ADDRESS_LIST = (ADDRESS = (PROTOCOL = TCP)(HOST = <span class="variable" translate="no">true_cache_host</span>)(PORT = 1521)) ) (CONNECT_DATA = (SID = tcdb1i) ) ) # True Cache database application service name sales_tc = (DESCRIPTION = (ADDRESS_LIST = (ADDRESS = (PROTOCOL = TCP)(HOST = <span class="variable" translate="no">true_cache_host</span>)(PORT = 1521)) ) (CONNECT_DATA = (SERVICE_NAME = sales_tc) ) ) # Primary database network name primdb1i = (DESCRIPTION = (ADDRESS_LIST = (ADDRESS = (PROTOCOL = TCP)(HOST = <span class="variable" translate="no">primary_database_host</span>)(PORT = 1521)) ) (CONNECT_DATA = (SID = primdb1i) ) ) # Primary database application service name sales = (DESCRIPTION = (ADDRESS_LIST = (ADDRESS = (PROTOCOL = TCP)(HOST = <span class="variable" translate="no">primary_database_host</span>)(PORT = 1521)) ) (CONNECT_DATA = (SERVICE_NAME = sales) ) ) listener_primary = (DESCRIPTION = (ADDRESS = (PROTOCOL = TCP)(HOST = <span class="variable" translate="no">primary_database_host</span>)(PORT=1521)) ) listener_true_cache = (DESCRIPTION = (ADDRESS = (PROTOCOL = TCP)(HOST = <span class="variable" translate="no">true_cache_host</span>)(PORT=1521)) ) ``` 

###  Configure and Start the Local Listener on the True Cache Node {#GUID-0DCD96B1-1C66-404B-BF1B-9E3D2EAC5512} 

Configure and start the local listener to enable the True Cache node to receive redo from the primary database. 

  1. Create the ` listener.ora ` file manually or by using NETCA. 

Here's an example of ` listener.ora ` on a True Cache node: ``` SID_LIST_LISTENER = (SID_LIST = (SID_DESC = (GLOBAL_DBNAME = tcdb1i) (ORACLE_HOME = <span class="variable" translate="no">local_oracle_home_on_true_cache</span>) (SID_NAME = tcdb1i) ) ) LISTENER = (DESCRIPTION = (ADDRESS = (PROTOCOL = TCP)(HOST = <span class="variable" translate="no">true_cache_host</span>)(PORT = 1521)) ) ``` 

  2. In a command window, ensure that the ` ORACLE_HOME ` and ` ORACLE_SID ` environment variables are set properly. 

See [ Configuring the Operating System Environment Variables ](https://docs.oracle.com/pls/topic/lookup?ctx=dblatest&id=GUID-EC18C4A6-3BA5-4C14-9D76-B0DD62FEFFF2) . 

  3. Ensure that the ` $ORACLE_HOME/bin ` directory is in your ` PATH ` environment variable. 
  4. Start the listener and verify that it started correctly. 

``` lsnrctl start lsnrctl status ``` 




###  Copy the Password File or Wallet from the Primary Database to the True Cache Node {#GUID-BD4B03CA-B1E0-44B6-9B8F-2D1B0C8E87B4} 

To provide authentication for redo transport sessions, copy the primary database's password file or wallet to the True Cache node. 

True Cache uses Oracle Net to transport redo data and control messages between the members of a True Cache configuration. These redo transport sessions are authenticated using a password file, so every True Cache in the configuration needs an up-to-date copy of the password file from the primary database. 

Copy the primary databaseâs password file to the appropriate directory on the True Cache node. You can find the password file in the ` V$PASSWORDFILE_INFO ` view, and on Linux the file is usually in ` $ORACLE_HOME/dbs/orapw *`SID`* ` . 

In the examples in this documentation, the primary database's password file is ` $ORACLE_HOME/dbs/orapwprimdb1i ` and would be copied to a True Cache node and renamed to ` $ORACLE_HOME/dbs/orapwtcdb1i ` . 

You need to recopy the password file in the following situations: 

  * Whenever an administrative privilege ( ` SYSDG ` , ` SYSOPER ` , ` SYSDBA ` , and so on) is granted or revoked. 
  * After the password of any user with administrative privileges is changed. 



Copy the wallet instead of the password file in the following situations: 

  * If the primary database uses TLS certificate authentication instead of a password file. 
  * If the primary database configures Transparent Data Encryption (TDE). Include only the ` ROOT ` container's master key in the copy for True Cache, which is used to decrypt encrypted redo that's shipped from the primary database. 

> **note:** When using a TDE wallet, Oracle recommends configuring an auto-login or local auto-login wallet for True Cache. For auto-login wallets, you an create the wallet on the primary database and copy both the password wallet (ewallet.p12) and auto-login wallet (cwallet.sso) to True Cache. Local auto-login wallets are host-specific, so you can't copy them to another node. Instead, for local auto-login wallets, copy the password wallet to True Cache, open it on True Cache, and create the local auto-login wallet there with the wallet password. The local auto-login wallet offers more security because it prevents someone from copying both wallets and using them without knowing the wallet password. To learn more about wallet types, see [ About Oracle Database Wallets ](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/oracle-database/23&id=DBSEG-GUID-B24417A4-ED1D-428C-AB18-E3E7D32D9005) . To learn more about wallet types, see [ About Oracle Database Wallets ](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/oracle-database/23&id=DBSEG-GUID-B24417A4-ED1D-428C-AB18-E3E7D32D9005)




###  Prepare a PFILE for True Cache {#GUID-329AF735-212B-4FF8-BC09-D57F4B6CCE51} 

For each True Cache, prepare a ` PFILE ` with the following parameters. 

**Table: Initialization Parameters** 

Parameter  |  Description   
---|---  
` TRUE_CACHE=TRUE ` |  This tells the server that this is a True Cache.   
` DB_NAME ` |  Enter the same value as the one for the primary database.   
` DB_UNIQUE_NAME ` |  Specify a unique name for this True Cache.   
` DB_FILES ` |  Enter the same value as the one for the primary database.   
` SGA_TARGET ` |  Enter the SGA memory size of this True Cache.   
` REMOTE_LISTENER ` |  Enter the primary database listener. This is not needed for the ` CREATE TRUE CACHE ` command, but it's needed to use JDBC.  Note:  For Oracle RAC primary databases, set the  ` REMOTE_LISTENER ` parameter to ` SCAN:port ` .  Note:  Check to see if Valid Node Checking for Registration (VNCR) is configured for the listener. If so, add True Cache to the ` REGISTRATION_INVITED_NODES_LISTENER ` parameter in the ` listener.ora ` file of the remote listener.  (In the example following this table, this is the primary database's ` listener.ora ` file.)  Note:  To simplify configuration and avoid connection issues, you can use the ` LISTENER_NETWORKS ` parameter instead of specifying ` REMOTE_LISTENER ` and ` LOCAL_LISTENER ` separately. All listeners within the same ` network_name ` will cross-register. For example:  ``` LISTENER_NETWORKS='((NAME=network_name)(LOCAL_LISTENER=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=true_cache_host)(PORT=1521))))(REMOTE_LISTENER=primary_database_host:1521))' ```  For more information, see [ Best Practices for Load Balancing in a Uniform Configuration ](best-practices-load-balancing-uniform-configuration.html#GUID-9B482C68-28E9-4210-AC51-37A3FFC25EDC) .   
` FAL_SERVER ` |  Enter the network name for the primary database.  Note:  For Oracle RAC primary databases, set the  ` FAL_SERVER ` parameter to ` SCAN:port/service_name ` .  If you have standby databases that could become a primary after a primary-standby switchover, True Cache automatically adds the existing standby databases to the ` FAL_SERVER ` list. However, if you add a new standby database after configuring True Cache, you need to append the new standby database to the ` FAL_SERVER ` parameter for True Cache. To do this, use the following command with a comma-separated list of databases:  ``` ALTER SYSTEM SET FAL_SERVER='<span class="variable" translate="no">existing_databases</span>,<span class="variable" translate="no">new_standby_database</span>' SCOPE=BOTH ```   
` FAL_CLIENT ` |  Enter the True Cache network name. When you start True Cache, it automatically adds that network name to the corresponding primary database ` LOG_ARCHIVE_DEST_  n  ` initialization parameter so that the primary database knows where to send online redo log blocks.   
` DB_CREATE_FILE_DEST ` |  Enter a directory to store the internal True Cache files (for example, control files and temp files).   
` DB_DOMAIN ` |  This might be necessary if the primary database also sets ` DB_DOMAIN ` . Set this to the domain where the True Cache node resides.   
` WALLET_ROOT ` and ` TDE_CONFIGURATION ` |  If the primary database configures TDE with the recommended ` WALLET_ROOT ` and ` TDE_CONFIGURATION ` parameters, configure them accordingly on this True Cache.   
  
For example, the following ` PFILE ` is named ` init_tcdb1i.ora ` and stored in the ` $ORACLE_HOME/dbs/ ` directory on the True Cache node: 

``` true_cache=true db_name=primaryd db_unique_name=tcdb1 db_files=200 sga_target=20G fal_server=primdb1i fal_client=tcdb1i instance_name=tcdb1i db_create_file_dest=<span class="variable" translate="no">local_directory_on_true_cache_node</span> local_listener=<span class="variable" translate="no">listener</span> remote_listener=<span class="variable" translate="no">listener_primary</span> ``` 

The preceding example assumes that the primary database's ` DB_UNIQUE_NAME ` initialization parameter is set to ` primarydb ` and ` DB_FILES ` is set to ` 200 ` . 

**Related Topics**

  * [ TRUE_CACHE ](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/oracle-database/23&id=REFRN-GUID-A3FBCE70-87F8-498F-8DF0-14BB094F41D3)
  * [ Initialization Parameters ](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/oracle-database/23&id=REFRN-GUID-FD266F6F-D047-4EBB-8D96-B51B1DCA2D61)



###  Create and Start True Cache {#GUID-F23E4B0D-A4D8-4F02-B914-5E1F7070C3B9} 

Follow these steps to create and start True Cache for the first time. 

  1. Start up True Cache in ` NOMOUNT ` mode using SQL*Plus. 

``` export ORACLE_SID=tcdb1i ``` ``` sqlplus / as SYSDBA ``` ``` STARTUP NOMOUNT PFILE=$ORACLE_HOME/dbs/init_tcdb1i.ora; ``` 
  2. Verify that the password file points to the right file. 

``` SELECT file_name FROM v$passwordfile_info; FILE_NAME \---------------------------------------------------- /u01/example/oracle/product/main/db_1/dbs/orapwtcdb1i ``` 
  3. Create and start True Cache. 

``` CREATE TRUE CACHE; ``` 
  4. To verify that True Cache is working, see [ Verifying That True Cache Is Working as Expected ](verifying-true-cache-configuration.html#GUID-2E0C8595-DD6D-4A6B-BA6B-E78786279EF7) . 



When you create True Cache, it automatically creates standby redo logs with the appropriate size, control files, temp files (when needed), and an internal ` SPFILE ` based on the first ` PFILE ` that you created. This ` SPFILE ` is used automatically on later ` STARTUP ` commands without the need to specify any ` PFILE ` values. 

At this point, your applications can use separate physical connections to the primary database and True Cache and choose which connection to use based on whether it's reading or writing. 

To use True Cache with the JDBC Thin driver, continue to the next topic to configure the database application services for True Cache. 
