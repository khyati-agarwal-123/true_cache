[Previous](configuring-true-cache-manually.md) [Next](creating-true-cache-
manually.md) JavaScript must be enabled to correctly display this content

  1. [Oracle True Cache User's Guide](index.md)
  2. [Configuring True Cache](configuring-true-cache.md)
  3. [Configuring True Cache Manually](configuring-true-cache-manually.md)
  4. Prerequisites

## 2.3.1 Prerequisites

Before configuring True Cache, complete the following steps.

  * Install the Oracle Database software on the True Cache nodes.
  * Ensure that a primary database is running on the primary node in archive logging (`ARCHIVELOG)` mode.

The primary database must be in `ARCHIVELOG` mode to ship redo log files to
the True Cache node. To see if the primary database is in `ARCHIVELOG` mode,
enter the following SQL query:

    
        SELECT log_mode FROM v$database;
    
    LOG_MODE
    ------------
    ARCHIVELOG

If the primary database is not in `ARCHIVELOG` mode, restart it in mount mode,
run the `ALTER DATABASE ARCHIVELOG` command, and open the primary database
again.

  * Don't set `LOG_ARCHIVE_CONFIG` and `LOG_ARCHIVE_DEST_n` on the primary database. True Cache automatically configures these for the primary database.


[← Previous](configuring-true-cache-manually.md)

[Next →](creating-true-cache-manually.md)
