[Previous](configuring-true-cache.md) [Next](configuring-true-cache-oracle-
dbca.md) JavaScript must be enabled to correctly display this content

  1. [Oracle True Cache User's Guide](index.md)
  2. [Configuring True Cache](configuring-true-cache.md)
  3. Overview of the True Cache Configuration

## 2.1 Overview of the True Cache Configuration

A True Cache environment can have a uniform or partitioned configuration.

### 2.1.1 Uniform Configuration

In a uniform configuration, you can deploy multiple, identical True Caches
that use the same True Cache database application service.

Client sessions are evenly distributed among True Caches, which all cache the
same set of data.

See the following technical architecture diagram for details and an example of
a uniform configuration:

[True Cache Uniform Configuration](../tciad/tc_uniform_conf.md)

### 2.1.2 Partitioned Configuration

In a partitioned configuration, the data is divided across multiple True
Caches, which each cache a different subset of the data.

The total size of the cached data across all True Caches can be much larger
than it would be for a single primary database or a single cache in a uniform
configuration.

See the following technical architecture diagrams for details and examples of
partitioned configurations:

  * [True Cache Configuration with colocation_tag](../tciad/tc_part_coloc_conf.md)
  * [True Cache Partitioned Configuration with Multiple Services](../tciad/tc_part_mult_conf.md)

### 2.1.3 True Cache Configuration Process Overview

At a high level, creating a True Cache configuration involves the following
steps.

  1. Configure the network for True Cache and the primary database, and optionally create a remote listener for high availability.
  2. Create True Cache.
  3. Create and start the True Cache database application services.

### 2.1.4 Best Practices for Database Application Services

Associate each primary database application service with a corresponding True
Cache database application service.

To distinguish the True Cache database application services, it's a good
practice to use the primary service name followed by `_TC`.

For example, if the primary service is `SALES`, the True Cache service would
be `SALES_TC`.

Also, only start the True Cache services on True Caches, and make sure they're
read-only.

### 2.1.5 Best Practices for Maximum Availability Architecture (MAA) with True
Cache

Follow these best practices for maximum availability with True Cache.

  * Configure True Cache for high availability with two identical True Caches per dataset using the uniform configuration architecture. To learn more, see [Uniform Configuration](overview-true-cache-configuration.md#GUID-CC75EDE3-11B2-447F-ADD0-9ED5E83D22D3 "In a uniform configuration, you can deploy multiple, identical True Caches that use the same True Cache database application service."). 
  * Use the JDBC 23ai UCP connection configuration for the application to ensure minimal impact on the application if one True Cache becomes unavailable.
  * Before enabling the database application service for True Cache, populate the cache by running critical workload queries. To learn more about how data is cached, see [How True Cache Works](overview-oracle-true-cache.md#GUID-B2A69908-5E17-4D2A-97FB-F768DDE43B32 "At a high level, here's how Oracle True Cache works."). 
  * Before shutting down True Cache for planned maintenance, stop the database application service on True Cache.
  * Partition or design True Caches to minimize fetches from the primary database.

### 2.1.6 Pluggable Database (PDB) Behavior and Restrictions with True Cache

To achieve maximum availability, PDBs on True Cache and the primary database
have the following behavior and restrictions.

  * Because non-CDB database are deprecated in Oracle 23ai, True Cache does not support non-CDB primary databases.
  * You can't perform administration tasks (for example, opening, closing, or shutting down) on PDBs on True Cache. However, applications can connect to specific PDBs on True Cache the same way they do for primary databases and Oracle Active Data Guard.
  * True Cache's PDB availability depends on the PDB state on the primary database. 
    * When True Cache connects to the primary database, all open PDBs on the primary database (in any Oracle RAC instances) are open on True Cache. 
    * If a PDB isn't open on the primary database on all Oracle RAC instances, then True Cache effectively can't read anything from that PDB and sees the PDB in a mounted state. When the primary database opens the PDB, then True Cache automatically opens it as well.
    * When a PDB is closed on the primary database, it's marked as disconnected on True Cache and never closes, unless some other action on the primary database requires the PDB to close, like dropping it, renaming it, or using flashback. Otherwise, the PDB remains open forever. Even if the primary CDB shuts down, when the primary CDB restarts, True Cache checks whether each PDB can resume or has to be closed and restarted. 
    * When the primary database shuts down, `ROOT` is marked as disconnected on True Cache, and it may remain disconnected for a maximum of 24 hours (which is configurable). 

The following example illustrates how a PDB can be open on one True Cache and
closed on another True Cache that's connected to the same primary database.

  1. PDB 1 is open on the primary database.
  2. True Cache 1 connects to the primary database and sees that PDB 1 is open, so it opens PDB 1.
  3. The primary database closes PDB 1, but PDB 1 stays open on True Cache 1.
  4. The primary database is bounced but doesn't reopen PDB 1.
  5. True Cache 2 connects to the primary database and sees PDB 1 is in a mounted state, so PDB 1 stays closed and mounted on True Cache 2.


[← Previous](configuring-true-cache.md)

[Next →](overview-true-cache-configuration.md)