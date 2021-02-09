
# Performance monitoring design document   

## Goal
Bring out the design considerations and enhancements to delfin framework for metrics collection from heterogeneous back ends
## Motivation and background
Performance metrics are the key indicators of storage devices, which every administrators would like to monitor, analyse and take decisions.
## Non-Goals
This doc covers only performance collection related design aspects and impacts to the framework, other use cases of delfin is not covered.
## Assumptions and Constraints
Performance collection is possible only if the storage is registered for resource collection in delfin. 

## Requirement analysis

### Input Requirements
1. Heterogeneous storage performance monitoring

#### Feature Requirements
1. Enable storage device for performance collection
2. Collect and push performance metrics periodically

#### Requirement Analysis
Requirements are derived from existing SRM use cases and user inputs. 
Competitor analysis report : **Link to be updated**

#### List of Requirements
1. Registration of storage for performance collection
2. Schedule collection job for different resource level metrics.
3. Provide metrics to delfin client

##### Functional Requirements
1. Register a device for performance collection
2. Specify resources and interval to poll
3. Once registered, performance information should be collected at every interval
4. GET supported metrics list for each device and resources
5. Push collected data to exporters.
6. update/remove performance collection configurations.
7. Remove Performance collection tasks when storage is un-registered.


##### Non Functional Requirements
1. Fault of any task node should not impact collection tasks
2. After process restart, the tasks should be automatically scheduled.
3. Collection failures must be logged


## Architecture Analysis

### System Architecture
Overall system architecture of delfin is available [here](https://github.com/sodafoundation/design-specs/blob/master/specs/SIM/SODA_InfrastructureManagerDesign.md).
Scope of architecture discussion in this design doc is limited to performance metrics collection interfaces, models and the modules involved for the same.

### Module Architecture
NA

### Architecture Tenets
**North-bound API** :- REST interface to delfin

**Exporter interface**:- Python class interfaces

**Driver interface**:- Python class interfaces

**Database** :- Any relation databse (sqllite3 by default)

**API server or Resource Manager** :- Process which serves the REST APIs

**Task manager**:- Process which prepares, schedule and update collection jobs.

**Driver**:- An Object which is part of other processes to connect and collect information’s from a back end.

**Exporter**:- An object which is part of task process to push data out.


### High Level Module Architecture
NA

## Detailed Design

### Use case View
To be updated
#### List of Typical Use cases
To be updated

#### Usecase context model
To be updated

#### Interface Model

##### Driver interfaces
Tobe updated

##### External Interfaces
 
###### North-bound REST interfaces
To be updated


###### Exporter interface 
Exporters need to Interface to push collected metrics through exporters.
```
    def dispatch(self, ctxt, data):
    # ctxt :- Delfin context object
    # data:- Metric data
```
 Metric model reference [here](#metric-model) 
 
Detailed exporter writing user guide [here](https://docs.sodafoundation.io/guides/developer-guides/delfin/exporter-developer-guide/) 

#### End User Context
NA

### Functional Context

NA

### Non Functional Context

NA


### Data View

#### Data and Control Data Contexts
NA

#### Data Model

##### Metric Model

| Property  | datatype  | Description                                                     |
|------------|-----------|-----------------------------------------------------------------|
| name       | string    |  Name of the indicator                                          |
| labels     | dict { }   | Any parameters required to distinguish this indicator uniquely as key value pairs|
| values      | dict {}     | time_stamp of metric  as key and value of metric  as value.                                        |

###### metric data example
```
name = read_throuhput
labels = {
			'storage_id': '0000123456789',
			'resource_type': 'port',
			'id': '12c2d52f-01bc-41f5-b73f-7abf6f38a340',
			'resource_id': 'FF1:001',
			'custom_propperty’:  'CTRL1',
			'type': 'RAW'
			'unit': 'IOPS'
			
	}
values = {
    1594635195: 1094.28,
    1594636195: 1234.54
}

```
###### Label guidelines and standards
Every metric will have storage_id, resource_type, unit,type and resource_id as mandatory labels along with other custom labels. This will help the client to uniquely identify the metric.

| | |
|-|-|
|**Resource**|**Required labels**|
|Device| **storage_id** = <storage_id> <br /> **id** = < delfin id> <br />  **resource_type** = device <br /> **resource_id** = < native device id> <br /> **unit** = enum <br /> **type** = enum |
|Storage-Pool| **storage_id** = <storage_id> <br /> **resource_type** = storage_pool <br /> **resource_id** = < native  id of this resource> <br />  **id** = < delfin id> <br />  **type**=enum <br />  **unit** = enum <br />  


###### Label field enums
| | |
|-|-|
|**Propperty**|**enum**|
|type|RAW,DERIVED,AGGREGATED|
|
|unit|IOPS,MB/s,%,ms,KB,

##### Database model
To be updated


##### Metric lists
###### Metric Lists analysis summary
* Considered different available solutions to derive support matrix of some common back ends (EMC vmax, EMC unity, ibm ds, ibm SVC, HP 3par)
* Some metrics can take common names in delfin
* Wherever the native metric name is overridden to map delfin common name, driver should provide 'native_name' and 'native_description' in the labels.
* Other metrics will follow platform metric naming
* Delfin will have API to list supported metric for each resource at each driver
* Metric name bandwidth is rarely used , most of the platforms consider data rate as throughput (MB/s) and IOPS as requests or iorate 
* Common metric names are derived based on their availability in other SRM platforms 


###### Common metric list

**Device**

| Delfin Metric  | Unit | Description                                                         |
| -------------- | ---- | ------------------------------------------------------------------ |
| throughput     | MB/s | Represents how much data is successfully transferred in MB/s       |
| responseTime   | ms   | Average time taken for an IO operation in ms                       |
| requests       | IOPS | Input/output operations per second                                 |
| readThroughput | MB/s | Represents how much data read is successfully transferred in MB/s  |
| writeThroughput  | MB/s | Represents how much data write is successfully transferred in MB/s |
| readRequests   | IOPS | Read requests per second                                           |
| writeRequests  | IOPS | Write requests per second                                          |
| memoryUsage    | %    | Internal memory usage in %                                         |
|                |      |                                                                    |

**Storage Pool**

| Metric Name     | Unit | Description                                                        |
| --------------- | ---- | ------------------------------------------------------------------ |
| Throughput      | MB/s | Represents how much data is successfully transferred in MB/s       |
| responseTime    | ms   | Average time taken for an IO operation in ms                       |
| Requests        | IOPS | Input/output operations per second                                 |
| readThroughput  | MB/s | Represents how much data read is successfully transferred in MB/s  |
| writeThroughput | MB/s | Represents how much data write is successfully transferred in MB/s |
| readRequests    | IOPS | Read requests per second                                           |
| writeRequests   | IOPS | Write requests per second                                          |

**Volume**

| Delfin Metric     | unit | Descrition                                                         |
| ----------------- | ---- | ------------------------------------------------------------------ |
| throughput        | MB/s | Represents how much data is successfully transferred in MB/s       |
| responseTime      | ms   | Average time taken for an IO operation in ms                       |
| requests          | IOPS | Input/output operations per second                                 |
| writeThroughput   | MB/s | Represents how much data read  is successfully transferred in MB/s |
| readThroughput    | MB/s | Represents how much data write is successfully transferred in MB/s |
| readRequests      | IOPS | Read requests per second                                           |
| writeRequests     | IOPS | Write requests per second                                          |
| readResponseTime  | ms   | Average time taken for a read  IO operation in ms                  |
| writeResponseTime | ms   | Average time taken for a write  IO operation in ms                 |

**Controller**

| Metric Name       | Unit | Description                                                        |
| ----------------- | ---- | ------------------------------------------------------------------ |
| requests          | IOPS | Input/output operations per second                                 |
| readThroughput    | MB/S | Represents how much data read is successfully transferred in MB/s  |
| writeThroughput   | MB/S | Represents how much data write is successfully transferred in MB/s |
| responseTime      | ms   | Average time taken for an IO operation in ms                       |
| cpuUsage          | %    | Overall CPU usage in %                                             |
| memoryUsage       | %    | Overall Memory usage in %                                          |
| readRequests      | IOPS | Read requests per second                                           |
| writeRequests     | IOPS | Write requests per second                                          |
| throughput        | MB/s | Represents how much data is successfully transferred in MB/s       |
| readResponseTime  | ms   | Average time taken for a read  IO operation in ms                  |
| writeResponseTime | ms   | Average time taken for a write  IO operation in ms                 |

**Port**

| Delfin Metric     | Unit | Remarks                                                            |
| ----------------- | ---- | ------------------------------------------------------------------ |
| Throughput        | MB/s | Represents how much data is successfully transferred in MB/s       |
| Requests          | IOPS | Input/output operations per second                                 |
| responseTime      | ms   | Average time taken for an IO operation in ms                       |
| readThroughput    | MB/s | Represents how much data read is successfully transferred in MB/s  |
| writeThroughput   | MB/s | Represents how much data write is successfully transferred in MB/s |
| readRequests      | IOPS | Read requests per second                                           |
| writeRequests     | IOPS | Write requests per second                                          |
| readResponseTime  | ms   | Average time taken for a read  IO operation in ms                  |
| writeResponseTime | ms   | Average time taken for a write  IO operation in ms                 |

**Disk**

| Delfin Metric | Unit | Description                                                  |
| ------------- | ---- | ------------------------------------------------------------ |
| responseTime  | ms   | Average time taken for an IO operation in ms                 |
| serviceTime   | ms   | Time spent completing disk I/O requests.                     |
| requests      | IOPS | Input/output operations per second                           |
| readRequests  | IOPS | Read requests per second                                     |
| writeRequests | IOPS | Write requests per second                                    |
| throughput    | MB/s | Represents how much data is successfully transferred in MB/s |

### Development and Deployment Context

#### Code

NA

#### Debug Model

NA

  
#### Build & Package

NA

#### Deployment

NA

  
### Execution View

NA


## Sequence diagrams

To be updated

## Design Alternatives and other notes

NA

## Open Issues

NA

## Design Requirements / Tasks

NA


## Scratchpad
