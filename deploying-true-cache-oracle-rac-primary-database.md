##  Deploying True Cache for an Oracle RAC Primary Database {#GUID-642FD30C-36A8-43DD-A118-985788F3577C} 

This section summarizes the configuration requirements for deploying True Cache for a primary database in an Oracle Real Application Clusters (Oracle RAC) environment. These requirements are also included in context within the detailed configuration steps. 

> **note:** All of this information also appears in the context of the configuration steps for [ Configuring True Cache with Oracle DBCA ](configuring-true-cache-oracle-dbca.html#GUID-32AFBE99-73B3-4F79-A138-66007AC8A896) and [ Configuring True Cache Manually ](configuring-true-cache-manually.html#GUID-C52CC00C-3035-4A7C-8AB1-C68648A120F8) . 

  * True Cache needs to be able to access each instance in the Oracle RAC environment. To do this, use the single client access name (SCAN) listener when configuring True Cache. 

    * If you're configuring True Cache with DBCA,  set the ` -sourceDBConnectionString ` parameter to ` SCAN:port/service_name ` . 
    * If you're manually configuring True Cache, use the following settings when preparing the ` PFILE ` for True Cache: 

      * Set the  ` REMOTE_LISTENER ` parameter to ` SCAN:port ` . 
      * Set the  ` FAL_SERVER ` parameter to ` SCAN:port/service_name ` . 
    * Add  the True Cache node to the invited node list for the SCAN listener by using the ` srvctl ` command line utility. (Don't manually edit the ` listener.ora ` file as the grid owner user.) 

For example: 

``` srvctl modify scan_listener -invitednodes <span class="variable" translate="no">true_cache_host</span> -endpoints TCP:<span class="variable" translate="no">port</span> ``` 
  * When creating the database application services on the primary database,  use the ` srvctl ` command line utility to add the services.  Start the service on the primary instance.  Use ` srvctl ` only for Oracle Clusterware-managed instances. 

To create both a True Cache service and the primary database service: 

``` srvctl add service -db <span class="variable" translate="no">primary_db_unique_name</span> -service <span class="variable" translate="no">true_cache_service_name</span> -preferred <span class="variable" translate="no">primary_db_instance_list</span> -pdb <span class="variable" translate="no">primary_pdb_name</span> ``` ``` srvctl add service -db <span class="variable" translate="no">primary_db_unique_name</span> -service <span class="variable" translate="no">primary_db_service_name</span> -preferred <span class="variable" translate="no">primary_db_instance_list</span> -pdb <span class="variable" translate="no">primary_pdb_name</span> -true_cache_service <span class="variable" translate="no">true_cache_service_name</span> ``` 

For example: 

``` srvctl add service -db primdb1i -service sales_tc -preferred primdb1i1,primdb1i2 -pdb sales_pdb srvctl add service -db primdb1i -service sales -preferred primdb1i1,primdb1i2 -pdb sales_pdb -true_cache_service sales_tc ``` 

To create a True Cache service for an existing primary database service ( ` db_service_name ` ): 

``` srvctl add service -db <span class="variable" translate="no">primary_db_unique_name</span> -service <span class="variable" translate="no">true_cache_service_name</span> -preferred <span class="variable" translate="no">primary_db_instance_list</span> -pdb <span class="variable" translate="no">primary_pdb_name</span> ``` ``` srvctl modify service -db <span class="variable" translate="no">primary_db_unique_name </span>-service <span class="variable" translate="no">primary_db_service_name</span> -true_cache_service <span class="variable" translate="no">true_cache_service_name</span> ``` 

For example: 

``` srvctl add service -db primdb1i -service sales_tc -preferred primdb1i1,primdb1i2 -pdb sales_pdb srvctl modify service -db primdb1i -service sales -true_cache_service sales_tc ``` 

> **note:** 

Also disable the True Cache service on the cluster where it was added to prevent errors when stopping and restarting the primary database services. For example: 

``` srvctl start service -d primdb1i -s sales_tc ``` ``` srvctl stop service -d primdb1i -s sales_tc ``` ``` srvctl disable service -d primdb1i -s sales_tc ``` 

This doesn't affect standby databases or True Caches because they run outside the cluster where the True Cache service is disabled. The disabled status is not stored in the primary dictionary and ` DBA_SERVICES ` , but only in Cluster Ready Services (CRS), and only in the cluster where the True Cache service was added. Other databases outside the cluster can start the service with ` DBMS_SERVICE.START_SERVICE ` and aren't affected by this setting. 




**Related Topics**

  * [ srvctl modify scan_listener ](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/oracle-database/23&id=RACAD-GUID-8E829323-D14F-40E4-AE39-CB23885EED85)


