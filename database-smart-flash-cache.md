##  Database Smart Flash Cache {#GUID-ED1DA158-6649-47CD-9A39-7E0930203441} 

If the data that you want to cache doesn't fit in memory, you can expand the capacity of True Cache by adding flash devices. You do this by configuring Database Smart Flash Cache. 

To enable Database Smart Flash Cache, see [ Configuring Database Smart Flash Cache ](https://docs.oracle.com/pls/topic/lookup?ctx=db23&id=ADMIN-GUID-31E0885D-53DB-442D-BC5F-DDE0588C915A) . 

> **note:** When configuring Database Smart Flash Cache on True Cache, spilling TDE encrypted data to local flash devices is not supported. If a data block is TDE encrypted on the primary database, it stays in the DRAM buffer cache on True Cache. 
