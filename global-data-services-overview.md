##  Global Data Services Overview {#GUID-58C520B8-B91C-43B2-974F-EED4C0BAECAC} 

Oracle Global Data Services (GDS) provides workload routing based on load, locality, or lag, as well as service failover across replicas. 

This helps enterprises maximize application performance, mitigate downtime during planned and unplanned outages, and manage resources of replicas with one interface. 

GDS supports Oracle Databases, standby databases, GoldenGate replicated databases, Oracle globally distributed databases, True Caches, and more. 

You create and manage the GDS configuration and global services with the ` GDSCTL ` command-line interface, which is similar to the ` SRVCTL ` command-line interface used to manage an Oracle Real Application Clusters (Oracle RAC) database and its services. 

A GDS configuration includes the following components: 

  * The  GDS catalog  stores configuration data for a GDS configuration. The GDS catalog resides in an Oracle Database. For large-scale GDS configurations, Oracle recommends that the GDS catalog be hosted outside the databases in the GDS configuration. 
  * A  GDS region  is a group of databases, True Caches, and clients in close network proximity (for example, east and west). For high availability, assign a buddy region for each region. Only one buddy region is allowed for each region. 
  * A  global service manager (GSM)  is the central software of GDS, providing service-level load balancing, failover, and centralized management of services in the GDS configuration. For high availability, include two GSMs in each region. 
  * A  GDS pool  is a group of databases and True Caches that offer a common set of global services (for example, human resources and sales). A region can contain multiple GDS pools, and these pools can span multiple regions. A database or True Cache can only belong to a single GDS pool. All databases and True Caches that provide the same global service must belong to the same pool. 
  * Global services  are functionally similar to the local database application services that are provided by single-instance or Oracle RAC databases. The main difference between global services and local services is that global services span the instances of multiple databases, whereas local services span the instances of a single database. 
  * A  client connection pool  , such as the Oracle universal connection pool (UCP) JDBC connection pool that can receive load balancing recommendations is required for global runtime connection load balancing. 
  * An  Oracle Notification Service (ONS) server  is located with each GSM. All ONS servers in a region are interconnected in an ONS server network. The GSMs create runtime load balancing advisories and publish them to the client connection pool through the ONS server network. 



**Related Topics**

  * [ Introduction to Global Data Services ](https://docs.oracle.com/pls/topic/lookup?ctx=db23&id=GSMUG-GUID-829434C7-608D-40D4-81C8-A77A63FCD7EB)


