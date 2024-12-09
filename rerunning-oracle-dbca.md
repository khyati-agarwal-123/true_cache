 

## Rerunning Oracle DBCA {#ODBTC-GUID-49051FCB-D57E-4F06-A2E2-7AE05AB33C17}

If you run into issues when configuring True Cache with Oracle DBCA, you need to clean up the True Cache node and then rerun DBCA.

### Rerunning Oracle DBCA After Creating True Cache {#ODBTC-GUID-EAB50DC8-DD91-4343-B937-1E45BD89477D}

If you need to rerun Oracle DBCA after creating True Cache, clean up the True Cache node first.

If you don't do the cleanup, DBCA might say that the instance already exists.

  1. Shut down True Cache if it was started during the creation. See [Shutting Down True Cache](shutting-and-starting-true-cache.html#GUID-4A1E5699-B43E-46D7-8E7D-908BF0A28668 "To shut down True Cache, use the SHUTDOWN command."). 
  2. Remove any newly created files in the `$ORACLE_HOME/dbs` directory for True Cache. File names contain the True Cache name. 
  3. If you used a Transparent Data Encryption (TDE) auto-login wallet, delete the wallet and auto-login file in the `$ORACLE_BASE/admin/true_cache_name/wallet_root/tde/` directory. 
  4. Create True Cache again. See [Creating True Cache with DBCA](configuring-true-cache-oracle-dbca.html#GUID-A534C50F-3A84-4C04-9765-F85F99F0E52F "Follow these steps to create True Cache with Oracle DBCA."). 



### Rerunning Oracle DBCA After Configuring True Cache Database Application Services {#ODBTC-GUID-EBB7FEC6-12BB-4DE8-8603-DD1C91A260FC}

If you need to rerun Oracle DBCA after configuring True Cache database application services, you might need to delete the True Cache service from the primary pluggable database (PDB) using the DBMS_Service package.

Example error message:
    
    
    [DBT-19958] Database service 'trueCacheServiceName'

  1. For services that are specific to a PDB, connect to the specific PDB, or set the correct PDB container in your session.
  2. Connect to True Cache and stop the True Cache service on True Cache if it's running. 

`EXEC DBMS_SERVICE.STOP_SERVICE('trueCacheServiceName');`

  3. Delete the service on the primary database using `DBMS_SERVICE.DELETE_SERVICE`. 

`EXEC DBMS_SERVICE.DELETE_SERVICE('trueCacheServiceName');`

  4. Configuration the True Cache application service again. See [Configuring True Cache Database Application Services with DBCA](configuring-true-cache-oracle-dbca.html#GUID-79518DDB-56AA-4D2B-9831-6F737424DEC3 "To use True Cache with the JDBC Thin driver, for each primary database application service that you want to cache, create a corresponding True Cache database application service."). 


