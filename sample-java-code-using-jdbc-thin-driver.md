[Previous](methods-connecting-true-cache.md) [Next](sample-java-code-
applications-using-native-connection-database.md) JavaScript must be enabled
to correctly display this content

  1. [Oracle True Cache User's Guide](index.md)
  2. [Using Oracle True Cache in Your Applications](using-oracle-true-cache-your-applications.md)
  3. Sample Java Code Using the JDBC Thin Driver

## 6.2 Sample Java Code Using the JDBC Thin Driver

The JDBC method is illustrated by the following Java code using the JDBC Thin
driver.

WARNING:

Do not use this code in a production environment.

The core of this basic code is in the `test1` function, which does the
following:

  1. Creates a connection to the primary database by using the JDBC Thin driver with either a default user name and password or values that are specified on the command line when invoking the program.
  2. Prints some basic information about the primary database connection to verify that the application is connected to the database.
  3. Changes the `isReadOnly` connection parameter to `TRUE` to automatically switch the connection to True Cache and verify that it's connected to it. 

    
    
    import java.sql.*;
    import java.io.*;
    import java.util.*;
    import java.util.logging.FileHandler;
    import java.util.logging.Level;
    import java.util.logging.Logger;
    
    import oracle.jdbc.driver.OracleLog;
    import oracle.jdbc.pool.OracleDataSource;
    
    public class TrueCache {
      static String url_primary = "jdbc:oracle:thin:@primary_database_host:1521/SALES";
      static String user = "username";
      static String password = "password";
    
      public static void main(String args[]) {
        try {
          TrueCache t = new TrueCache();
          if(args != null && args.length >0 ) {
            url_primary = args[0];
            user = args[1];
            password = args[2];
          }
          t.test1();
        } catch (Exception e) {
          e.printStackTrace();
        }
      }
    
      public void test1() throws SQLException {
        show("Basic test to connect to primary and True Cache");
        try {
          show("Get Connection from True Cache instance using primary db and properties");
          OracleDataSource ods1 = new OracleDataSource();
          show("URL = " + url_primary);
          ods1.setURL(url_primary);
          ods1.setUser(user);
          ods1.setPassword(password);
          ods1.setConnectionProperty("oracle.jdbc.useTrueCacheDriverConnection", "true");
    
          Connection conn1 = ods1.getConnection();
    
          show("isReadonly " + conn1.isReadOnly());
          verifyConnection(conn1);  // This is connected to Primary
          
          conn1.setReadOnly(true);
          show("isReadonly " + conn1.isReadOnly());
          verifyConnection(conn1); // This is connected to True Cache
    
          conn1.close();
        } catch (SQLException sqex) {
          show("test1 -- failed" + sqex.getMessage()+":"+sqex.getCause());
          sqex.printStackTrace();
        }
        show("The end");
      }
    
      public void verifyConnection(Connection conn) {
        try {
          Statement statement = conn.createStatement();
          ResultSet rs = statement.executeQuery("SELECT database_role from v$database");
          ResultSetMetaData rsmd = rs.getMetaData();
          int columnsNumber = rsmd.getColumnCount();
          rs.next();
          show("Database role : " + rs.getString(1));
          rs.close();
          ResultSet resultSet = statement.executeQuery("SELECT SYS_CONTEXT('userenv', 'instance_name') as instance_name"
              + ", SYS_CONTEXT('userenv', 'server_host')" + " as server_host" + ", SYS_CONTEXT('userenv', 'service_name')"
              + " as service_name" + ", SYS_CONTEXT('USERENV','db_unique_name')" + " as db_unique_name" + " from sys.dual");
          resultSet.next();
          show("instance_name : " + resultSet.getString("instance_name"));
          show("server_host : " + resultSet.getString("server_host"));
          show("service_name : " + resultSet.getString("service_name"));
          show("db_unique_name : " + resultSet.getString("db_unique_name"));
          resultSet.close();
          statement.close();
        } catch (SQLException sqex) {
          show("verifyConnection failed " + sqex.getMessage());
        }
      }
    
      public void show(String msg) {
        System.out.println(msg);
      }
    }


[← Previous](methods-connecting-true-cache.md)

[Next →](sample-java-code-applications-using-native-connection-database.md)