##  4Shutting Down and Starting True Cache {#GUID-77A09974-2827-4791-9E49-6BD471395845} 

You can shut down and start an existing True Cache. 

> **note:** Perform these tasks on the True Cache container database (CDB), not on the pluggable database (PDB). 

###  Shutting Down True Cache {#GUID-4A1E5699-B43E-46D7-8E7D-908BF0A28668} 

To shut down True Cache, use the ` SHUTDOWN ` command. 

> **note:**  Before shutting down True Cache for planned maintenance, stop the database application service on True Cache. 

``` connect / as SYSDBA; ``` ``` SHUTDOWN; ``` 

When you shut down True Cache, the cached content is lost. When you restart True Cache, it fetches the content again from the primary database. 

###  Starting True Cache {#GUID-B9DA81A6-1D87-4DF2-A0A7-DB08193EDD39} 

To start an existing True Cache, use the ` STARTUP ` command. 

``` connect / as SYSDBA; ``` ``` STARTUP; ``` 

Work is automatically routed to True Cache when it declares that it's ready to support the True Cache service. 
