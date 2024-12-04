[Previous](true-cache-error-messages.md) [Next](true-cache-wait-events.md)
JavaScript must be enabled to correctly display this content

  1. [Oracle True Cache User's Guide](index.md)
  2. True Cache Database Statistics Descriptions

## B True Cache Database Statistics Descriptions

The following table describes the True Cache-related statistics stored in the
`V$SESSTAT` and `V$SYSSTAT` views.

For details on classes and `TIMED_STATISTICS`, see [Statistics
Descriptions](https://docs.oracle.com/pls/topic/lookup?ctx=en/database/oracle/oracle-database/23&id=REFRN-GUID-2FBC1B7E-9123-41DD-8178-96176260A639).

Table B-1 Database Statistics Descriptions

Name | Class | Description | TIMED_STATISTICS  
---|---|---|---  
TrueCache: block requests to preferred primary | 264 | Number of block fetch requests sent to the preferred primary Oracle RAC instance with object or undo affinity. |   
TrueCache: block requests to primary | 264 | Total number of block fetch requests sent to the primary database. |   
True Cache: message roundtrip time data send | 256 | Cumulative, elapsed, round-trip messaging time in microseconds of this primary instance sending data blocks to True Cache. |   
True Cache: message roundtrip time request send | 256 | Cumulative, elapsed, round-trip messaging time in microseconds of this True Cache sending data block fetching requests to the primary database. |   
True Cache: message count data send | 256 | Total number of messages this primary instance sends to True Cache for returning data blocks. |   
True Cache: message count request send | 256 | Total number of messages this True Cache sends to the primary database for requesting data blocks. |   
True Cache potentially current buffer made current | 264 | Count of data blocks arriving at True Cache as potentially current buffers due to timing conditions, and later confirmed to be real current buffers, so redo will continue to apply. |   
True Cache potentially current buffer made CR | 264 | Count of data blocks arriving at True Cache as potentially current buffers due to timing conditions, and later deemed to be good only as consistent read buffers, so they will be aged out. | 


[← Previous](true-cache-error-messages.md)

[Next →](true-cache-wait-events.md)
