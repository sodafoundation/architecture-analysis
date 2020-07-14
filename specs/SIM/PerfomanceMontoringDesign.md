
# Performance Monitoring in delfin- design document   

## Requirement analysis

1. User wants to get device (storage) performance information
2. User wants to get other resource (storage-pool,volume,controller,port,disk, etc )performance information.
3. User wants to get performance information for a range , or historic performance:  example :  (real-time, user-defined time segment, and latest 4Hours/12Hours/1Day/1Week/1Month/1Year performance information).
4. User wants to configure collection intervals for each resource 
5. User wants to trigger performance collection  of particular storage.
6. Usee wants to export real time collection data to third party systems.

## Possible metrics for resources
### Storage
| Metric Name     | Description               | Unit | Data Type | VMAX | Oceanstor |
|-----------------|---------------------------|------|-----------|------|-----------|
| cpuUsage        | indicates the CPU usage.  | %    | float     | No   | Yes       |
| memoryUsage     | indicates the CPU usage.  | %    | float     | No   | Yes       |
| responseTime    | Average I/O response time | ms   | float     | Yes  | Yes       |
| throughput      | Throughput                | IO/s | float     | Yes  | Yes       |
| readThroughput  | Read Throughput           | IO/s | float     | Yes  | Yes       |
| writeThroughput | Write Throughput          | IO/s | float     | Yes  | Yes       |
| bandwidth       | Bandwidth                 | MB/s | float     | Yes  | Yes       |
| readBandwidth   | Read Bandwidth            | MB/s | float     | Yes  | Yes       |
| writeBandwidth  | Write Bandwidth           | MB/s | float     | Yes  | Yes       |

### Controller
| Metric Name                        | Description                     | Unit | Data Type | VMAX | Oceanstor |
|------------------------------------|---------------------------------|------|-----------|------|-----------|
| throughput                         | Throughput                      | IO/s | float     |      |           |
| readBandwidth                      | Read Bandwidth                  | MB/s | float     |      |           |
| writeBandwidth                     | Write Bandwidth                 | MB/s | float     |      |           |
| responseTime                       | Average I/O response time       | ms   | float     |      |           |
| cpuUsage: indicates the CPU usage. | CPU usage                       | %    | float     |      |           |
| memoryUsage: memory usage          | Memory usage                    | %    | float     |      |           |
| queueLength: queue length          | Queue length                    |      | float     |      |           |
| readThroughput                     | Read Throughput                 | IO/s | float     |      |           |
| writeThroughput                    | Write Throughput                | IO/s | float     |      |           |
| bandwidth                          | Bandwidth                       | MB/s | float     |      |           |
| writeCacheUsage                    | Write cache usage               | %    | float     |      |           |
| readHitRatio                       | Read cache hit ratio            | %    | float     |      |           |
| writeHitRatio                      | Write cache hit ratio           | %    | float     |      |           |
| readResponseTime                   | Average read I/O response time  | ms   | float     |      |           |
| writeResponseTime                  | Average write I/O response time | ms   | float     |      |           |
|                                    |                                 |      |           |      |           |
|                                    |                                 |      |           |      |           |

### Port
| Metric Name               | Description                     | Unit | Data Type | VMAX | Oceanstor |
|---------------------------|---------------------------------|------|-----------|------|-----------|
| bandwidth                 | Bandwidth                       | MB/s | float     |      |           |
| throughput                | Throughput                      | IO/s | float     |      |           |
| responseTime              | Average I/O response time       | ms   | float     |      |           |
| readBandwidth             | Read Bandwidth                  | MB/s | float     |      |           |
| writeBandwidth            | Write Bandwidth                 | MB/s | float     |      |           |
| readThroughput            | Read Throughput                 | IO/s | float     |      |           |
| writeThroughput           | Write Throughput                | IO/s | float     |      |           |
| utility                   | Utilization                     | %    | float     |      |           |
| readResponseTime          | Average read I/O response time  | ms   | float     |      |           |
| writeResponseTime         | Average write I/O response time | ms   | float     |      |           |
| queueLength: queue length | Queue length                    |      | float     |      |           |
| readSize:                 | Average read I/O size           | KB   | float     |      |           |
| writeSize                 | Average Write I/O Size          | KB   | float     |      |           |
| serviceTime: service time | Average I/O service time        | ms   | float     |      |           |
| maxResponseTime           | Maximum I/O response time       | ms   | float     |      |           |
| readRatio: read ratio     | Read I/O percentage             | %    | float     |      |           |
| writeRatio                | Write I/O percentage            | %    | float     |      |           |


### Storage Pool
| Metric Name     | Description               | Unit | Data Type | VMAX | Oceanstor |
|-----------------|---------------------------|------|-----------|------|-----------|
| bandwidth       | Bandwidth                 | MB/s | float     | No   | Yes       |
| responseTime    | Average I/O response time | ms   | float     | Yes  | Yes       |
| throughput      | Throughput                | IO/s | float     | Yes  | Yes       |
| readBandwidth   | Read Bandwidth            | MB/s | float     | Yes  | Yes       |
| writeBandwidth  | Write Bandwidth           | MB/s | float     | Yes  | Yes       |
| readThroughput  | Read Throughput           | IO/s | float     | Yes  | Yes       |
| writeThroughput | Write Throughput          | IO/s | float     | Yes  | Yes       |
|                 |                           |      |           |      |           |
|                 |                           |      |           |      |           |


### Disk
<TO do>

### Volume
<to do>

## delfin Metric model
delfin can internally use [Prometheus](https://prometheus.io/) to persist metric data. delfin should also give option for third parties to export collected metrics to other platforms.
delfin metric data model is in align with [Prometheus data model](https://prometheus.io/docs/concepts/data_model/) to support time series data persistence .
### data model
| Propperty | DataType | Description           | Example               |
|-----------|----------|-----------------------|-----------------------|
| name      | string   | Name of the indicator | average_read_requests |
|labels|map|Any parameters required to distuinquish this indicator uniquely |storage_id = delfin id of the storage, resource_type = volume, id = delfin id of this resource, native_volume_id = id of this volume in back_end, native_storage_pool_id = associated storage_pool id in backend,  type= RAW, unit = IO/s, value_type = COUNTER|
| value     | float | value of this indicator      | 1094.28     |
| timestamp | int   | epoch time of this indicator | 1594635195  |


### Labels model
| | |
|-|-|
|Resource|Required labels|
|Array| storage_id = <storage_id> resource_type = array id = <delfin id of this resource> |
|Storage-Pool| storage_id = <storage_id> resource_type = storage_pool id = <delfin id of this resource> native_storage_pool_id = <associated storage_pool id in backend > type=enum unit = enum value_type = enum|
|Volume|storage_id = <storage_id> resource_type = volume id = <delfin id of this resource> native_volume_id = <id of this pool  in back_end> native_storage_pool_id = <associated storage_pool id in backend > type=enum unit = enum value_type = enum|

#### enums
| | |
|-|-|
|**Propperty**|**enum**|
|type|RAW,DERIVED,AGGREGATED|
|value_type|COUNTER, RATE, GUAGE,|
|unit|IOPS,MB/s,%,ms,KB,

## Performance Collection Flow


![](./Resources/PerformanceCollectionFlow.png)

## Performance Metric Query Flow
![](./Resources/PerformanceQueryFlow.png)