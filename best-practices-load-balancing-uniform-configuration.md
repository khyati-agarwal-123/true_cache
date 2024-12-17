##  Best Practices for Load Balancing in a Uniform Configuration {#GUID-9B482C68-28E9-4210-AC51-37A3FFC25EDC} 

If multiple True Caches exist and serve the same database application service, the listener automatically distributes and load balances sessions to each cache.  In a uniform configuration, the listener picks a True Cache randomly or based on load, and all True Caches cache the same data. 

A uniform configuration uses runtime connection load balancing (CLB), which routes requests to the True Cache that offers the best performance. For this to work, all True Caches' ` REMOTE_LISTENER ` parameters should point to the same listener, which is also the primary database's listener (whether it's single instance primary database's local listener or an Oracle RAC primary database's SCAN listener). 

The application's JDBC URL should point to the primary database. Then the JDBC Thin driver creates one logical connection and multiple physical connections to the primary database and to each True Cache. Setting the ` setReadOnly(true) ` flag in the Java code reroutes connections to the True Caches automatically. The True Caches are registered with the primary database listener, and they also send their load statistics to the primary database listener. You should see an even distribution between True Caches. 

To simplify configuration and avoid connection issues, consider using the ` LISTENER_NETWORKS ` initialization parameter instead of specifying ` REMOTE_LISTENER ` and ` LOCAL_LISTENER ` separately. With ` LISTENER_NETWORKS ` , all listeners within the same network name will cross-register. For example: 

``` listener_networks='((NAME=net1)(LOCAL_LISTENER=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=tc1.example.com)(PORT=1521))))(REMOTE_LISTENER=scan_primary:1521))', '((NAME=net2)(LOCAL_LISTENER=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=tc1.example.com)(PORT=1521))))(REMOTE_LISTENER=scan_standby:1521))' ``` 

For more about configuring remote listeners and listener networks, see [ Prepare a PFILE for True Cache ](creating-true-cache-manually.html#GUID-329AF735-212B-4FF8-BC09-D57F4B6CCE51) . 

**Related Topics**

  * [ About Run-Time Connection Load Balancing ](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/oracle-database/23&amp;amp;id=JJUCP-GUID-305FD568-95F5-458C-8B52-5A81FA62999C)


