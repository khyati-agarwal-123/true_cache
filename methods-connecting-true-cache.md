 

## Methods for Connecting to True Cache {#ODBTC-GUID-B8F9674D-2492-4A46-8ADF-318A1FCDD078}

Applications can use True Cache in two ways.

  * The application maintains two physical connections: one to the primary database and one to True Cache. Each connection has a database application service, and the application chooses which connection to use based on whether it's reading or writing. You can use this model with any existing client drivers and any programming language. 

The application sends queries that don't need to see the most current data to True Cache through a True Cache database application service. The application sends other queries and updates to the primary database through the primary database application service.

  * The application maintains one logical connection that uses the database application service for the primary database. The JDBC Thin driver (starting with Oracle Database 23ai) maintains physical connections to the primary database and True Cache. This model only works with Java applications. 

The application switches between the primary database and True Cache without having to specify an instance name. The application uses special calls to flag the logical connection as read-only or read-write. If it's read-only, the query is sent to True Cache. Otherwise, it's sent to the primary database. 

To enable the True Cache functionality for Java applications, set the value of the `oracle.jdbc.useTrueCacheDriverConnection` property to `true`. After you enable True Cache, the JDBC Thin driver uses the standard `java.sql.Connection.setReadOnly(boolean)` and `java.sql.Connection.isReadOnly()` methods to mark a connection as read-only. By default, the read-only mode is `false` for a connection. 




See the following technical architecture diagram for details and examples:

[True Cache Application](../tciad/tc_app.html)

**Related Topics**

  * [Support for Oracle True Cache](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/oracle-database/23&id=JJDBC-GUID-DE0056E3-4E34-4F60-83CC-CE7CD531B3F0)
  * [setReadOnly(boolean) method](https://docs.oracle.com/javase/7/docs/api/java/sql/Connection.md)  * [isReadOnly( method](https://docs.oracle.com/javase/7/docs/api/java/sql/Connection.md)


