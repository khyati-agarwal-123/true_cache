[Previous](enabling-dml-redirection.md) [Next](shutting-and-starting-true-
cache.md) JavaScript must be enabled to correctly display this content

  1. [Oracle True Cache User's Guide](index.md)
  2. Deploying True Cache in Containers

## 3 Deploying True Cache in Containers

Containers can simplify the deployment of True Cache configurations.

You can use the scripts that are provided in the True Cache container image to
automatically create a True Cache configuration. The scripts complete the
following configuration tasks:

  * Create the primary database and True Cache containers.
  * Create and start True Cache.
  * Create and start the database application services for the primary database and True Cache.

See the complete instructions for downloading the images and creating the
containers in the [Oracle Database Container Images
README.md](https://github.com/oracle/docker-images/blob/main/OracleDatabase/SingleInstance/README.md).

Create a separate container for each True Cache. Assign different container
names, Oracle SIDs, and database application service names, as needed.


[← Previous](enabling-dml-redirection.md)

[Next →](shutting-and-starting-true-cache.md)
