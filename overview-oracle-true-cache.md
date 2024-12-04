[Previous](preface.md) [Next](configuring-true-cache.md) JavaScript must
be enabled to correctly display this content

  1. [Oracle True Cache User's Guide](index.md)
  2. Overview of Oracle True Cache

## 1 Overview of Oracle True Cache

Oracle True Cache is an in-memory, consistent, and automatically managed SQL
and key-value (object or JSON) cache for Oracle Database.

For an interactive tour, see [Oracle True Cache Technical
Architecture](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/oracle-
database/23&id=TCIAD).

### 1.1 About Oracle True Cache

True Cache is an in-memory, read-only cache in front of an Oracle database.

Like Oracle Active Data Guard, True Cache is a fully functional, read-only
replica of the primary database, except that it's mostly diskless.

Modern applications often require a high number of connections and fast, low-
latency access to the data. A popular approach is to place caches in front of
the database because applications typically perform many more reads than
updates, and they can read from the cache without affecting the database
performance. For example, an airline reservation system reads data frequently
as people shop for flights, and it's okay if the data isn't the most current
compared to what's in the database. The application only needs to access the
most current data when someone reserves a flight.

Unlike conventional caches, True Cache automatically keeps the most frequently
accessed data in the cache, and it keeps the cache consistent with the primary
database, other objects in the same cache, and other caches. It caches all
Oracle Database objects and data types, including JSON.

Because True Cache implements Oracle Database security policies, you can
control access to the cache. This enables you to cache sensitive data, like
private personal information, that you might not ordinarily cache.

### 1.2 Benefits of Oracle True Cache

Oracle True Cache provides several business benefits related to application
development and performance.

  * Improves scalability and performance by offloading queries from the primary database.
  * Reduces application response time and network latency by deploying True Cache closer to the application. This especially benefits situations where a database is in a different location than the application due to data residency requirements.
  * Creates a large, in-memory storage area by dividing data across multiple True Caches. The total size of the cached data across all True Caches can be much larger than it would be for a single primary database or cache.
  * Automatically maintains the cache contents.
  * Simplifies development and maintenance by being transparent to the application.

### 1.3 How True Cache Works

At a high level, here's how Oracle True Cache works.

  * An application decides whether to query data from True Cache or the primary database. For details on how this works, see [Application Usage Models](overview-oracle-true-cache.md#GUID-516B11EB-A48F-4682-A203-B80BED778CC7 "Applications can use True Cache in two ways."). 
  * True Cache satisfies queries by using data that's cached in its memory. When the data isn't in the cache, True Cache fetches the data from the primary database.
  * True Cache is empty when it starts up, so it reads large chunks of data to populate the cache. After a block is cached, it's updated automatically through redo apply from the primary database. This is similar to the update mechanism used in Oracle Active Data Guard.
  * A query to True Cache returns only committed data, and the data is always consistent.
  * Like all caches, the True Cache data might not be the most current data as it exists in the primary database.
  * If multiple True Caches exist and serve the same database application service, the listener automatically distributes and load balances sessions to each cache.

### 1.4 Considerations for Using True Cache

Consider these points when deciding whether to use Oracle True Cache.

  * You need to configure enough memory for True Cache so your most frequently accessed data will stay in memory.
  * True Cache only needs a small amount of storage for the standby redo log files. Configuration files like control files, the `SPFILE`, and temporary data files are automatically created. 
  * True Cache is a read-only cache, so you can't directly update the cache, but you can use DML redirection to indirectly update the cache.

**Related Topics**

  * [Enabling DML Redirection](enabling-dml-redirection.md#GUID-FC9A3203-B55B-4BC2-879D-A7DF2CAD3B2B "True Cache is a read-only cache, so you can't directly update the cache, but you can use DML redirection to indirectly update the cache.")

### 1.5 Application Usage Models

Applications can use True Cache in two ways.

  * The application maintains two physical connections: one to the primary database and one to True Cache. Each connection has a database application service, and the application chooses which connection to use based on whether it's reading or writing. You can use this model with any existing client drivers and any programming language. 

The application sends queries that don't need to see the most current data to
True Cache through a True Cache database application service. The application
sends other queries and updates to the primary database through the primary
database application service.

  * The application maintains one logical connection that uses the database application service for the primary database. The JDBC Thin driver (starting with Oracle Database 23ai) maintains physical connections to the primary database and True Cache. This model only works with Java applications. 

The application switches between the primary database and True Cache without
having to specify an instance name. The application uses special calls to flag
the logical connection as read-only or read-write. If it's read-only, the
query is sent to True Cache. Otherwise, it's sent to the primary database.

**Related Topics**

  * [Using Oracle True Cache in Your Applications](using-oracle-true-cache-your-applications.md#GUID-BEAC6163-E6A8-4FF2-9028-678310F4D5E0 "When True Cache is configured, applications must decide whether to query data from True Cache or the primary database.")

### 1.6 Lock-Free Concurrency Control

For applications that work with JSON documents, Oracle extends HTTP entity tag
(ETAG) support to implement lock-free, optimistic concurrency control.

A database GET request to True Cache automatically computes the ETAG and
inserts it into the returned document. When the modified document is PUT back
into the primary database, the following occurs:

  1. The database verifies that the underlying document rows still match the ETAG that was computed by the GET request.
  2. If the rows match the ETAG, the rows are atomically updated.
  3. If there's not a match, another user has changed the data and the PUT request is rejected.
  4. The PUT request can be retried using the new data.


[← Previous](preface.md)

[Next →](overview-oracle-true-cache.md)
