[Previous](configuring-global-data-services-true-cache.md) [Next](true-
cache-error-messages.md) JavaScript must be enabled to correctly display
this content

  1. [Oracle True Cache User's Guide](index.md)
  2. [Deploying Oracle True Cache with Oracle Global Data Services](deploying-oracle-true-cache-oracle-global-data-services.md)
  3. True Cache Restrictions with Oracle Global Data Services

## 9.4 True Cache Restrictions with Oracle Global Data Services

Deploying True Cache with Oracle GDS has the following restrictions.

  * When adding True Cache services in `GDSCTL`, the `-failover_primary` option requires the patch for bug 36740927.
  * If the application uses the JDBC programming model, both the primary database service and True Cache service names must be fully qualified with the domain name (for example, `sales.example.com` and `sales_tc.example.com`). This is because GDS has a default domain name and is different from the database's `domain_name` parameter. This also limits the fully qualified service name to a maximum of 64 characters.


[← Previous](configuring-global-data-services-true-cache.md)

[Next →](true-cache-error-messages.md)
