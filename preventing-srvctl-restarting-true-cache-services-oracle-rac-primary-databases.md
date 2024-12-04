[Previous](rerunning-oracle-dbca.md) Next JavaScript must be enabled to
correctly display this content

  1. [Oracle True Cache User's Guide](index.md)
  2. [Troubleshooting True Cache](troubleshooting-true-cache.md)
  3. Preventing SRVCTL from Restarting True Cache Services for Oracle RAC Primary Databases

## E.2 Preventing SRVCTL from Restarting True Cache Services for Oracle RAC
Primary Databases

To prevent errors when stopping and restarting primary database services for
Oracle RAC database, disable the True Cache service on the cluster where it
was added.

The following errors might occur:

    
    
    PRCD-1133: failed to start services for database
    PRCR-1095: Failed to start resources using filter
    CRS-2501: Resource 'ora.primary_db.true_cache_service.svc' is disabled

To prevent these errors, disable the True Cache service on the cluster where
it was added. For example:

`srvctl disable service -d primdb1i -s sales_tc`

This doesn't affect standby databases or True Caches because they run outside
the cluster where the True Cache service is disabled. The disabled status is
not stored in `DBMS_SERVICE/service$`, but only in Cluster Ready Services
(CRS), and only in the cluster where the True Cache service was added. Other
databases outside the cluster can start the service with
`DBMS_SERVICE.START_SERVICE` and aren't affected by this setting.

**Related Topics**

  * [Configuring True Cache Database Application Services with DBCA](configuring-true-cache-oracle-dbca.md#GUID-79518DDB-56AA-4D2B-9831-6F737424DEC3 "To use True Cache with the JDBC Thin driver, for each primary database application service that you want to cache, create a corresponding True Cache database application service.")
  * [Configuring True Cache Database Application Services Manually](configuring-true-cache-database-application-services-manually.md#GUID-AA56E2C9-CE4B-403C-99B9-9ADC44305E4B "To use True Cache with the JDBC Thin driver, for each primary database application service that you want to cache, create a corresponding True Cache database application service.")
  * [Deploying True Cache for an Oracle RAC Primary Database](deploying-true-cache-oracle-rac-primary-database.md#GUID-642FD30C-36A8-43DD-A118-985788F3577C "This section summarizes the configuration requirements for deploying True Cache for a primary database in an Oracle Real Application Clusters \(Oracle RAC\) environment. These requirements are also included in context within the detailed configuration steps.")


[← Previous](rerunning-oracle-dbca.md)
