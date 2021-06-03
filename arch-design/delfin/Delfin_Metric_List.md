# Delfin Metric List   

**Authors:** [Najmudheen CT](https://github.com/NajmudheenCT)

## Motivation and background
Delfin framework  collects metrics form heterogeneous devices. Every device follow their own naming and descriptions for metrics. Delfin should have a unified list of metrics to be supported to enable users to view the metrics in same context irrespective of devices.

## Goal
List all delfin unified metric names, unit and description

## Non-Goals
This doc do not cover the common metric data model or custom metric names.
## Metrics Lists
### Device
| Metric Name     | Unit | Description                             |
| --------------- | ---- | --------------------------------------- |
| iops            | iops | read and write  operations per second   |
| readIops        | iops | Read  operations per second             |
| writeIops       | iops | Write  operations per second            |
| throughput      | MB/S | Total data transferred per second       |
| readThroughput  | MB/S | Total read data transferred per second  |
| writeThroughput | MB/S | Total write data transferred per second |
| responseTime    | ms   | Average time taken for an IO operation  |
### Storage Pool
| Metric Name     | Unit | Description                             |
| --------------- | ---- | --------------------------------------- |
| iops            | iops | read and write  operations per second   |
| readIops        | iops | Read  operations per second             |
| writeIops       | iops | Write  operations per second            |
| throughput      | MB/S | Total data transferred per second       |
| readThroughput  | MB/S | Total read data transferred per second  |
| writeThroughput | MB/S | Total write data transferred per second |
| responseTime    | ms   | Average time taken for an IO operation  |

### Volume
| Metric Name        | Unit | Description                                 |
| ------------------ | ---- | ------------------------------------------- |
| iops               | iops | read and write  operations per second       |
| readIops           | iops | Read  operations per second                 |
| writeIops          | iops | Write  operations per second                |
| throughput         | MB/S | Total data transferred per second           |
| readThroughput     | MB/S | Total read data transferred per second      |
| writeThroughput    | MB/S | Total write data transferred per second     |
| responseTime       | ms   | Average time taken for an IO operation      |
| cacheHitRatio      | %    | Percentage of io that are cache hits        |
| readCacheHitRatio  | %    | Percentage of read ops that are cache hits  |
| writeCacheHitRatio | %    | Percentage of write ops that are cache hits |
| ioSize             | KB   | The average size of IO requests in KB.      |
| ioSizeRead         | KB   | The average size of read IO requests in KB. |
| ioSizeWrite        | KB   | The average size of read IO requests in KB. |

### Controller

| Metric Name     | Unit | Description                             |
| --------------- | ---- | --------------------------------------- |
| iops            | iops | read and write  operations per second   |
| readIops        | iops | Read  operations per second             |
| writeIops       | iops | Write  operations per second            |
| throughput      | MB/S | Total data transferred per second       |
| readThroughput  | MB/S | Total read data transferred per second  |
| writeThroughput | MB/S | Total write data transferred per second |
| responseTime    | ms   | Average time taken for an IO operation  |
### Port

| Metric Name     | Unit | Description                             |
| --------------- | ---- | --------------------------------------- |
| iops            | iops | read and write  operations per second   |
| readIops        | iops | Read  operations per second             |
| writeIops       | iops | Write  operations per second            |
| throughput      | MB/S | Total data transferred per second       |
| readThroughput  | MB/S | Total read data transferred per second  |
| writeThroughput | MB/S | Total write data transferred per second |
| responseTime    | ms   | Average time taken for an IO operation  |
### Disk

| Metric Name     | Unit | Description                             |
| --------------- | ---- | --------------------------------------- |
| iops            | iops | read and write  operations per second   |
| readIops        | iops | Read  operations per second             |
| writeIops       | iops | Write  operations per second            |
| throughput      | MB/S | Total data transferred per second       |
| readThroughput  | MB/S | Total read data transferred per second  |
| writeThroughput | MB/S | Total write data transferred per second |
| responseTime    | ms   | Average time taken for an IO operation  |
