##  3Deploying True Cache in Containers {#GUID-967008D7-0EA0-44E0-920D-43FFCEDC3D0E} 

Containers can simplify the deployment of True Cache configurations. 

You can use the scripts that are provided in the True Cache container image to automatically create a True Cache configuration. The scripts complete the following configuration tasks: 

  * Create the primary database and True Cache containers. 
  * Create and start True Cache. 
  * Create and start the database application services for the primary database and True Cache. 



See the complete instructions for downloading the images and creating the containers in the [ Oracle Database Container Images README.md ](https://github.com/oracle/docker-images/blob/main/OracleDatabase/SingleInstance/README.md) . 

Create a separate container for each True Cache. Assign different container names, Oracle SIDs, and database application service names, as needed. 
