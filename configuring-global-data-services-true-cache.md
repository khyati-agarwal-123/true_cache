 

## Configuring Global Data Services for True Cache {#ODBTC-GUID-CD2F923B-5105-4AD4-AB56-054CE7C3A8A7}

These are the basic steps to configure Oracle Global Data Services (GDS) for True Cache.

For additional GDS configuration options, see [Configuring the Global Data Services Framework](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/oracle-database/23&id=GSMUG-GUID-04D33448-2CB4-40C7-9DA0-1CFC6EC5E101). 

### Set Up the Environment {#ODBTC-GUID-24D85936-1135-443F-BA2A-BFC6E8AC4F49}

Before you install any software, review the hardware, network, operating system, and other software requirements for Linux.

  * The primary database and all True Caches in the GDS pool must be able to reach (in both directions) every global service managerâs (GSM's) listener and Oracle Notification Service (ONS) ports. The GSM listener ports and ONS ports must also be opened to the application tier, the primary database and all True Caches in the GDS pool, the GDS catalog, and all other GSMs.
  * The TNS listener port (default: 1521) of the primary database and all True Caches in the GDS pool must be opened (in both directions) to the GSMs and the GDS catalog.
  * If you run `GDSCTL` from a separate machine, you also must have a port opened (in both directions) from that machine directly to the primary database and all True Caches in the GDS pool. 



For detailed information about memory, physical storage, kernel versions and packages required by Global Data Services see [Database Installation Guide for Linux](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/oracle-database/23/gsmug&id=LADBI-GUID-7273645F-03DA-4B92-A8E8-6ABE82D674B9). 

**Related Topics**

  * [Planning an Installation](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/oracle-database/23&id=GSMUG-GUID-B7010949-4EAE-4AB1-A136-D5A4CD2AE688)



### Install the Global Service Manager Software {#ODBTC-GUID-7E0CF1EF-17D9-4E60-8E1D-12B335428EF5}

The global service manager (GSM) is the central component of the GDS framework, and it includes the `GDSCTL` command-line interface. 

Install at least one GSM in each region. For high availability, include two GSMs in each region.

To install GSMs, see the following topics in the Oracle Database Global Data Services Concepts and Administration Guide: 

  * [What You Need to Know About Installing a Global Service Manager](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/oracle-database/23&id=GSMUG-GUID-0ECB7334-D39B-4F8F-B952-062F21FE2259)
  * [Installing a Global Service Manager](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/oracle-database/23&id=GSMUG-GUID-38660413-FE7F-4DF2-917D-D0F8A03984A6)



### Configure True Cache {#ODBTC-GUID-1899EA0E-1B23-43C3-9E3E-D4FAC3AB4EEB}

Create True Caches using Oracle DBCA or manually.

See [Creating True Cache with DBCA](configuring-true-cache-oracle-dbca.html#GUID-A534C50F-3A84-4C04-9765-F85F99F0E52F "Follow these steps to create True Cache with Oracle DBCA.") or [Creating True Cache Manually](creating-true-cache-manually.html#GUID-8580479A-7DD5-4B64-A3DE-70A6E25654AF "Follow these steps to create True Cache manually."). 

### Configure the GDS Catalog Database {#ODBTC-GUID-5B6C1BC3-7156-44B1-A882-9E3940C40A15}

Configure the database where the GDS catalog will reside.

Note:

In addition to these steps, ensure that the GDS catalog is protected for high availability and disaster recovery. 

  1. Create the GDS administrator account and grant `GSMADMIN_ROLE` privileges. 

For example:
    
        CREATE USER gsm_admin IDENTIFIED BY ****
    
        GRANT gsmadmin_role TO gsm_admin

  2. Unlock the GDS system accounts on the catalog database.
    
        sqlplus / as SYSDBA
    
        ALTER USER gsmuser ACCOUNT UNLOCK IDENTIFIED BY ****
    
        ALTER USER gsmcatuser ACCOUNT UNLOCK IDENTIFIED BY ****

  3. Start `GDSCTL`. 
    
        gdsctl




**Related Topics**

  * [What You Need to Know About Creating the Global Data Services Catalog](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/oracle-database/23&id=GSMUG-GUID-DF0A8CF0-5542-4798-9948-03106F8B2BBE)



### Create the GDS Catalog {#ODBTC-GUID-842C8996-ABE5-460C-BAB7-AC3D2A24F858}

Create the GDS catalog on the primary database or a database outside the GDS configuration.

For large-scale GDS configurations, Oracle recommends that the GDS catalog be hosted outside the databases in the GDS configuration.
    
    
    create catalog -database db_name –region region_list -user user_name/password

For example:
    
    
    create catalog -database gdscatdb -region DCX, DC_Y -user gsmcatuser/****

If you don't specify a region when setting up the catalog, then a default region named `REGIONORA` is created. You can optionally add more regions later. 

### Set Up Global Service Managers {#ODBTC-GUID-AA18D3E9-6E0F-4356-A0A9-659BACCFE654}

Before you can start a global service manager (GSM), you need to register the GSM with the GDS catalog.

For high availability, include two GSMs in each region.

  1. Add a GSM to the region.
    
        add gsm -gsm gsm_name -pwd password -catalog catalog_db_name -region region_name

For example: 
    
        add gsm -gsm gsm_dc_x1 -catalog primdb1i -pwd **** -region DC_X

Note:

You can run this command only on the system that hosts the GSM. Repeat the command for each GSM on each system that runs the GSM. 

  2. Start the GSM.
    
        start gsm -gsm gsm_name

For example:
    
        start gsm -gsm gsm_dc_x1

  3. Check the status of the GSM.
    
        status gsm




### (Optional) Set Up Regions {#ODBTC-GUID-D93A645B-E37E-405E-8870-0FDAC8966346}

If you didn't already set up regions when creating the GDS catalog, add a GDS region for each data center.

For high availability, assign a buddy region for each region. Only one buddy region is allowed for each region.

Note:

At least one GSM must be added and running before you can add regions. 
    
    
    add region –region region_list
    
    
    modify region -region region_name -buddy region_name

For example:
    
    
    add region -region DC_X, DC_Y
    
    
    modify region -region DC_X -buddy DC_Y

### Set Up the GDS Pool {#ODBTC-GUID-2ECD5A11-2EEC-48AE-99CF-FF7B1D23CF49}

Add a GDS pool and then add the primary database and True Caches to the pool.
    
    
    add gdspool -gdspool db_pool_list
    
    
    add database -connect db_name -region region_name -gdspool gds_pool_name

For example:
    
    
    add gdspool -gdspool sales
    
    
    add database -connect primdb1i -pwd **** -gdspool sales –region DC_X  // primary
    
    
    add database -connect tcdb1 -pwd **** -gdspool sales –region DC_X  // True Cache

### Set Up Global Services {#ODBTC-GUID-AAFD413A-04E7-4468-917C-7D035F0BEFCD}

Create and start the global database application services for the primary database and True Caches.

Note:

If the application uses the JDBC programming model, both the primary database service and True Cache service names must be fully qualified with the domain name (for example, `sales.example.com` and `sales_tc.example.com`). This is because GDS has a default domain name and is different from the database's `domain_name` parameter. This also limits the fully qualified service name to a maximum of 64 characters.

Note:

The PDB name is required to create a service for a PDB. If you don't specify the PDB name, the service is created in `CDB$ROOT`. 

  1. Create the global services for the primary database.
    
        add service -service service_name.domain_name -gdspool gdspool_name -preferred dbname_list -pdbname primary_pdb_name -role primary

For example:
    
        add service -service sales.example.com -gdspool sales -preferred primdb1i -pdbname sales_pdb -role primary

  2. Create the global services for True Cache.
    
        add service -service service_name.domain_name -gdspool gdspool_name -preferred_all -pdbname primary_pdb_name -clbgoal SHORT –rlbgoal SERVICE_TIME –locality LOCAL_ONLY –region_failover -lag lag_value -role true_cache -failover_primary

For example:
    
        add service -service sales_tc.example.com -gdspool sales -preferred_all -pdbname sales_pdb -clbgoal SHORT –rlbgoal SERVICE_TIME –locality LOCAL_ONLY –region_failover -lag 15 -role true_cache -failover_primary

Note:

The `-failover_primary` option requires the patch for bug 36740927.

  3. Associate the True Cache service with the primary database service.

     * For True Cache and single instance primary databases, use the `DBMS_SERVICE_PRVT PL/SQL` package. 

On the primary database, connect to the PDB first. The following example uses the `sales.example.com` and `sales_tc.example.com` services.
        
                ALTER SESSION SET CONTAINER=primary_pdb_name;
        
                DECLARE
            db_params sys.dbms_service_prvt.svc_parameter_array;
            cl_params sys.dbms_service_prvt.svc_parameter_array;
        BEGIN
            -- modify an already existing primary service SALES to set the TRUE_CACHE_SERVICE attribute and associate with SALES_TC
            db_params('true_cache_service') :='sales_tc.example.com';
            DBMS_SERVICE_PRVT.MODIFY_SERVICE('sales.example.com', cl_params, db_params, FALSE, 1);
        END;

     * For Oracle RAC primary databases, use the `srvctl` command line utility with the `-global_override` option to alter global services. 

If you configure True Cache with Oracle DBCA, use the following command:
        
                srvctl add service -db primary_db_unique_name -service primary_db_service_name.domain_name -preferred primary_db_instance_list -pdb primary_pdb_name -global_override

For example:
        
                srvctl add service -db primdb1i -service sales.example.com -preferred primdb1i1,primdb1i2 -pdb sales_pdb -global_override

For manual configuration options, see [Create Database Application Services on the Primary Database](configuring-true-cache-database-application-services-manually.html#GUID-1F990837-4B60-4119-8119-AFC87F77EC38 "You can associate one primary database service with one True Cache service."). 

Note:

Also disable the True Cache service on the cluster where it was added to prevent errors when stopping and restarting the primary database services. For example:
        
                srvctl start service -d primdb1i -s sales_tc.example.com
        
                srvctl stop service -d primdb1i -s sales_tc.example.com
        
                srvctl disable service -d primdb1i -s sales_tc.example.com

This doesn't affect standby databases or True Caches because they run outside the cluster where the True Cache service is disabled. The disabled status is not stored in the primary dictionary and `DBA_SERVICES`, but only in Cluster Ready Services (CRS), and only in the cluster where the True Cache service was added. Other databases outside the cluster can start the service with `DBMS_SERVICE.START_SERVICE` and aren't affected by this setting. 

  4. Start the services.
    
        start service -service service_name.domain_name

The following example starts the services on the primary database and on all True Caches:
    
        start service -service sales.example.com
    
        start service -service sales_tc.example.com




Note:

If you receive the following warning when you add a service to a GDS pool with existing True Caches and services running, you can ignore it: ORA-44311: service service_name not running. 

#### Global Service Best Practices for JDBC {#ODBTC-GUID-9321FFB1-CFDE-4109-83F5-75335970A906}

To use global services with the JDBC `setReadOnly(TRUE)` flag, follow these best practices. 

  * Create the global services for the primary database and True Cache using fully qualified names (for example, `sales.example.com` and `sales_tc.example.com`). This is because GDS has a default domain name and is different from the database's `domain_name` parameter. This also limits the fully qualified service name to a maximum of 64 characters. 
  * Use `DBMS_SERVICE_PRVT` instead of `DBMS_SERVICE` to associate the True Cache service with the primary database service. 



#### GDSCTL add service Options {#ODBTC-GUID-E592A19E-219B-4748-9526-026C6FF93503}

This table describes the GDSCTL add service options that apply to True Cache.

Option | Description  
---|---  
-available dbname_list | Specify a comma-delimited list of available databases or True Caches on which the service runs if the preferred ones are not available. You can't specify a list of available instances, only databases and True Caches. You can use the modify service command with the -server_pool parameter to specify instance-level preferences. The list of available databases or True Caches must be mutually exclusive with the list of preferred ones. You cannot use this option with the -preferred_all option.  
-clbgoal {SHORT \| LONG} | For True Cache services, set the connection load balancing goal to SHORT for runtime load balancing.  
-failover_primary | If the -role is TRUE_CACHE, you can use this option to enable the service to failover to the primary database if no True Caches are available. Also include the primary database in the -preferred or -available list, or use the -preferred_all option. Note:The -failover_primary option requires the patch for bug 36740927.  
-gdspool gdspool_name | Specify the name of the global data services pool to which you want to add a service. If the pool name is not specified and there is only one gdspool with access granted to the user, then the gdspool with access granted is used as the default gdspool.  
-lag {lag_value \| ANY} | Specify the maximum acceptable lag for the service in seconds. Enter ANY if there is no upper threshold. If a True Cache falls behind the primary database beyond the specified lag time, the service stop forwarding requests to that True Cache until it catches up. The default value for lag, if not specified, is ANY.  
-locality {ANYWHERE \| LOCAL_ONLY} | For True Cache services, set the locality to LOCAL_ONLY. This indicates that GDS only routes to True Caches in the same region, regardless of load. Use this option together with -region_failover so that client connections and requests are routed to another region if all True Caches in a region have failed.  
-pdbname primary_pdb_name | Enter the primary pluggable database (PDB) name. Note:The PDB name is required to create a service for a PDB. If you don't specify the PDB name, the service is created in CDB$ROOT.  
-preferred dbname_list | Specify a comma-delimited list of preferred databases or True Caches on which the service runs. You can't specify preferred instances, only databases and True Caches. You can use the modify service command to specify instance-level preferences. The list of preferred databases or True Caches must be mutually exclusive with the list of available ones. You cannot use this option with the -preferred_all option.  
-preferred_all | For True Cache services, indicates that this service will be started on all existing and future True Caches and, if used with -failover_primary, the primary database.  
-region_failover | Indicates that the service is enabled for region failover. You can only use this option when you specify LOCAL_ONLY for the -locality option.  
-rlbgoal {SERVICE_TIME \| THROUGHPUT} | For True Cache services, set the runtime load balancing goal to SERVICE_TIME to balance connections by response time instead of by throughput.  
-role {PRIMARY] \| [PHYSICAL_STANDBY \| TRUE_CACHE} | Specify the database role that the database must have for this service to start on that database. For primary database services, use PRIMARY. For True Cache services, use TRUE_CACHE. This ensure that the service only runs on other True Caches, not on the primary database.  
-service service_name.domain_name | Specify the name of the global service. Note:If the application uses the JDBC programming model, both the primary database service and True Cache service names must be fully qualified with the domain name (for example, sales.example.com and sales_tc.example.com). This is because GDS has a default domain name and is different from the database's domain_name parameter. This also limits the fully qualified service name to a maximum of 64 characters. A global service name must be unique within a GDS pool and when qualified by domain, must also be unique within a GDS configuration. A global service cannot be created at a database if a local or global service with the same name already exists at that database. A global service name can contain alphanumeric characters, underscore (_), and period (.). The first character must be alphanumeric. The maximum length of a global service name is 64 characters. The maximum length of a domain qualified global service name is 250 characters. An Oracle Net connect descriptor used to connect to a global service must contain a domain qualified service name. For True Cache service names, see Best Practices for Database Application Services.  
  
Note:

This table lists the add service options that are specific to True Cache. For the full syntax and parameters, see [add service](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/oracle-database/23&id=GSMUG-GUID-4409D426-7F68-4729-9BE9-7A7C268C907B) in the Oracle Database Global Data Services Concepts and Administration Guide. 

### Set Up the Client TNS Entry {#ODBTC-GUID-E9C512AD-4AB9-4AF4-ACA0-E4EF348C83D4}

Update the `tnsnames.ora` files for the primary database and True Caches based on region. 

Example 9-1 Primary Database

In this example, the `ADDRESS_LIST` points to the GSM in the region where the primary database is located. 
    
    
    sales =
    (DESCRIPTION =(CONNECT_TIMEOUT=90)(RETRY_COUNT=30)(RETRY_DELAY=3)(TRANSPORT_CONNECT_TIMEOUT=3)
     (FAILOVER=ON)
      (ADDRESS_LIST = //Lists DC_X's GSM listeners because primary is in DC_X
       (LOAD_BALANCE=ON)
        (ADDRESS = (PROTOCOL = TCP)(HOST = gsm_dc_x1)(PORT = 1572)))
      (CONNECT_DATA =
        (SERVICE_NAME = sales.example.com)))

Example 9-2 True Cache

In this example, the first `ADDRESS_LIST` points to the GSM in the region where the True Cache is located. The second `ADDRESS_LIST` points to the GSM in the associated buddy region. 
    
    
    sales_tc =
    (DESCRIPTION =(CONNECT_TIMEOUT=90)(RETRY_COUNT=30)(RETRY_DELAY=3)(TRANSPORT_CONNECT_TIMEOUT=3)
     (FAILOVER=ON)
      (ADDRESS_LIST = //DC_X's GSM listeners
       (LOAD_BALANCE=ON)
        (ADDRESS = (PROTOCOL = TCP)(HOST = gsm_dc_x1)(PORT = 1572)))
      (ADDRESS_LIST = //buddy DC_Y's GSM listeners
       (LOAD_BALANCE=ON)
        (ADDRESS = (PROTOCOL = TCP)(HOST = gsm_dc_y1)(PORT = 1572)))
      (CONNECT_DATA =
        (SERVICE_NAME = sales_tc.example.com) (REGION=DC_X)))   // this client is in DC_X
