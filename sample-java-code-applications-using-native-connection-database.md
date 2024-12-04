[Previous](sample-java-code-using-jdbc-thin-driver.md) [Next](best-
practices-load-balancing-uniform-configuration.md) JavaScript must be
enabled to correctly display this content

  1. [Oracle True Cache User's Guide](index.md)
  2. [Using Oracle True Cache in Your Applications](using-oracle-true-cache-your-applications.md)
  3. Sample Java Code for Applications Using a Native Connection to the Database

## 6.3 Sample Java Code for Applications Using a Native Connection to the
Database

The following Java code illustrates how to use True Cache with applications
that use an object-relational mapping (ORM) framework.

This is another example of maintaining only one logical connection to a
database application service running on the primary database and letting the
driver handle the underlying physical connections. It shows how to get the
underlying connections from the framework so you can set the necessary
parameter.

WARNING:

Do not use this code in a production environment.

  1. Add the `oracle.jdbc.useTrueCacheDriverConnection=true` connection property to the connection URL in the `persistence.xml` file. 

  2. In the methods that need to use a True Cache connection, unwrap the session, use the `doWork` method to retrieve the underlying JDBC connection, and set the necessary read-only parameter for the connection. For example: 
    
        Session session = em.unwrap(Session.class);
    session.doWork(new Work() {
    
       @Override
       public void execute(Connection con) throws SQLException {
          // if it is connected to primary, I want the connection to switch to true_cache now
          if(!con.isReadOnly()) {
             // The below code demonstrates the connection is to primary now        
             System.out.println("Query getting executed on:");
             Statement statement = con.createStatement();
             ResultSet rs = statement.executeQuery("SELECT database_role from v$database");
             rs.next();        
             System.out.println("Database role : " + rs.getString(1));    
             rs.close();
    
             // Now switching the connections to true cache
             con.setReadOnly(true);
             // The below code demonstrates the connection is to true cache now        
             System.out.println("After set read only true, Query getting executed on:");
             Statement statement2 = con.createStatement();
             ResultSet rs2 = null;
    
             rs2 = statement2.executeQuery("SELECT database_role from v$database");
             rs2.next();        
             System.out.println("Database role : " + rs2.getString(1));        
             rs2.close()
          }
       }
    });


[← Previous](sample-java-code-using-jdbc-thin-driver.md)

[Next →](best-practices-load-balancing-uniform-configuration.md)
