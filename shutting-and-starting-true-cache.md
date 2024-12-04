[Previous](deploying-true-cache-containers.md) [Next](monitoring-true-
cache.md) JavaScript must be enabled to correctly display this content

  1. [Oracle True Cache User's Guide](index.md)
  2. Shutting Down and Starting True Cache

## 4 Shutting Down and Starting True Cache

You can shut down and start an existing True Cache.

Note:

Perform these tasks on the True Cache container database (CDB), not on the
pluggable database (PDB).

### 4.1 Shutting Down True Cache

To shut down True Cache, use the `SHUTDOWN` command.

Note:

Before shutting down True Cache for planned maintenance, stop the database
application service on True Cache.

    
    
    connect / as SYSDBA;
    
    
    SHUTDOWN;

When you shut down True Cache, the cached content is lost. When you restart
True Cache, it fetches the content again from the primary database.

### 4.2 Starting True Cache

To start an existing True Cache, use the `STARTUP` command.

    
    
    connect / as SYSDBA;
    
    
    STARTUP;

Work is automatically routed to True Cache when it declares that it's ready to
support the True Cache service.


[← Previous](deploying-true-cache-containers.md)

[Next →](shutting-and-starting-true-cache.md)
