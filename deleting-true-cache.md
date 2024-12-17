##  8Deleting True Cache {#GUID-18DB4FF4-8D27-499E-B950-6974D57C60D6} 

You can delete True Cache with Oracle DBCA or manually. 

###  Using Oracle DBCA to Delete True Cache {#GUID-CE633C46-155A-4E56-84E5-61CCA7433380} 

You can use Oracle DBCA to clean up the True Cache services and delete True Cache. 

####  Cleaning Up the True Cache Services from the Primary Database {#GUID-DE77694C-EFEA-4ED6-837A-6EFC156A6C31} 

To delete True Cache, you need to clean up its corresponding services on the primary database. 

Run the following command on the primary database to delete all the services that correspond to the True Cache that you're planning to delete. ``` ORACLE_HOME/bin/dbca -configureDatabase -cleanupTrueCacheInstanceService -sourceDB <span class="variable" translate="no">primary_sid_or_db_unique_name</span> -trueCacheConnectString <span class="variable" translate="no">true_cache_easy_connect_string</span> -serviceName <span class="variable" translate="no">primary_service_name</span> -trueCacheServiceName <span class="variable" translate="no">true_cache_service_name</span> -silent ``` 

Example: 

``` $ORACLE_HOME/bin/dbca -configureDatabase -cleanupTrueCacheInstanceService -sourceDB primdb1i -trueCacheConnectString tc.example.com:1522/tcdb1.example.com -serviceName sales -trueCacheServiceName sales_tc -silent ``` 

For descriptions of the parameters, see [ configureDatabase ](configuring-true-cache-oracle-dbca.html#GUID-0E86301A-4DBF-4AF1-A85D-20D100C26F28) . 

####  Deleting True Cache from the True Cache Node {#GUID-0F115D4A-5514-4026-9A70-FD86B5DDD21B} 

Run the following command to delete True Cache from the True Cache node. 

``` ORACLE_HOME/bin/dbca -deleteDatabase -sourceDB <span class="variable" translate="no">primary_sid_or_db_unique_name</span> -silent ``` 

Example: 

``` $ORACLE_HOME/bin/dbca -deleteDatabase -sourceDB tcdb1 -silent ``` 

###  Deleting True Cache Manually {#GUID-23B3E348-E5E4-497C-8EFB-E492CC61B141} 

To delete True Cache manually, use the following commands. 

``` connect / as SYSDBA; ``` ``` shutdown immediate; ``` ``` startup mount exclusive restrict; ``` ``` drop true cache; ``` 

> **note:** This does  not  drop the primary database. It deletes the True Cache ` SPFILE ` ; drops the True Cache control, standby log, and temporary files; and removes ` LOG_ARCHIVE_DEST ` entries from the primary database. 
