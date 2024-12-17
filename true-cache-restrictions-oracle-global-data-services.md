##  True Cache Restrictions with Oracle Global Data Services {#GUID-64735014-9D14-41AD-9A5D-168A7DBAED7B} 

Deploying True Cache with Oracle GDS has the following restrictions. 

  * When adding True Cache services in ` GDSCTL ` , the  ` -failover_primary ` option requires the patch for bug 36740927\. 
  * If the application uses the JDBC programming model, both the primary database service and True Cache service names must be fully qualified with the domain name (for example, ` sales.example.com ` and ` sales_tc.example.com ` ). This is because GDS has a default domain name and is different from the database's ` domain_name ` parameter. This also limits the fully qualified service name to a maximum of 64 characters. 


