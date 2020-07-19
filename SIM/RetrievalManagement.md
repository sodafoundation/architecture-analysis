# Retrieval Management Requirements and design



**Author(s)**: [Pravin Ranjan](https://github.com/pravinranjan10)

Major Version Updates

Date 13/07/2020 : Version : Description  : Author



## Goals
* Retrieve or collect the resources(storage, volumes and pools)
  information from registered backends.
* Perform the syncing process of resources whenever required and/or
  periodically.


## Motivation and background
Delfin is an infrastructure management platform for heterogeneous storage environments in SODA foundation. To support this project, resources discovery and data collection required which are implemented in this module.   

### Non-Goals

* Currently periodic Synchronization is not considered

### Assumptions and Constraints

NA

### Requirement Analysis

### Input Requirements

Overall Input requirements can be referred from [here](https://github.com/sodafoundation/design-specs/blob/master/specs/SIM/SODA_InfrastructureManagerDesign.md)


### Feature Requirements

#### Requirement Analysis

There are few major requirements for this module:
1. Develop framework for task manager
2. Collect Pool  information
3. Collect LUN(Volume) information
4. Collect device information


#### List of Requirements
##### Functional Requirements
The detailed requirements are given below.
##### Develop framework for task scheduler
* Create/Schedule a task, when device is registered
* Create/Schedule a task, periodically
* Create/Schedule a task, at given time
* Create/Schedule a task, at priority basis
* Create/Schedule a task, now (may be based on call from admin)
* Support for the configuration file/Policy setup
* Publish the task on Bus
* Retrieval Manager should be able to read the Task from Bus
* Clear task from scheduling(suppose, task is scheduled and   
  somebody unregisters the device. Then how to handle)

##### Collect Pool  information
* Collect Pool information of a device
* Store the pool information to DB
* Forward the pool information to exporter

##### Collect Volume  information
* Collect volume information of a device
* Store the volume information to DB
* Forward the volume information to exporter

##### Collect device information
* Collect device information
* Store the device information to DB
* Forward the device information to exporter



##### Non Functional Requirements

* Performance Requirements

* Security Requirements

* Other Non Functional Requirements (Scalability, HA etc…)


## Architecture Analysis
### High level Architecture
You may refer the high level architecture design [here](https://github.com/sodafoundation/design-specs/blob/master/specs/SIM/SODA_InfrastructureManagerDesign.md)


### Module Architecture
This is a Retrieval management module and responsible for retrieving(discovering) the resources(storage, volumes and pools) information from the registered backends. The already discovered resources can also be Synchronized whenever required and also on a periodic basis through API calls.

The api to Synchronize the resources: /v1/storages/sync

The above API will Synchronize all the resources of all registered backends in the system.

The Retrieval management module contains three major components, Redis, TaskManager and RabbitMq as shown in the diagram below. The Resource Manager and Driver Manager are part of the whole project delfin which are shortly described below (For more details high level architecture can be referred from the above link).

![Resource Synchronization](RetreivalManager2.png)

##### Redis:
* Redis is a in-memory data structure store, used as a database, cache
  and message broker.
* In this project, redis is used to store the lock and status
  variables so that other nodes of a cluster can access these variables in a distributed environment.

##### RabbitMq:
* RabbitMQ is used as a message broker in this project. The tasks are
  pushed to the queue of RabbitMQ, and later it published those tasks to connected consumer nodes.


##### Resource Manager:

* Act as the API server for all REST requests . Resource Manager takes
  care of syncing registered storages with SIM DB. In other words, this takes input from /v1/storages/sync API and calls other methods to Synchronize the resources information of registered backends.


##### Task Manager:

* Task manager is responsible for collecting the information of
  resources and updating the DB. It contains three submodules.
* RPC: This module is a Client side of the task manager. Resource
  Manager make a procedural call to RPC methods(i.e sync_storage_resource()). Later, it cast the task to RabbitMq message bus.
* Manager: It is tasks consumer part of the task manager. It receive the
  tasks from RabbitMq and make a procedural call to appropriate methods based on received input tasks.
* Tasks: It contains the implementation code for retrieving the
  resources information and updating the DB. To get the latest information from storage backends, it calls driver manager methods, and later updates the DB with collected information.


##### Delfin Driver manager:
* This layer contains driver’s code. Drivers are lightweight processes
  that configure and collect data from various sources including storage, switch , host etc.
* Each driver’s connected to a single storage backend and performs
  specific tasks(storage, volumes, pools collection) on that backend. Driver manager’s responsibility is to take input from the task manager and select specific drivers based on input parameters. Once the task is performed, return the response with collected information.
* Task manager to driver manager is a procedural call. And from driver
  manager to delfin south bound drivers are REST, CLI, SNMP, SSH, SMI etc.


#### Interface Model

##### External Driver Interfaces

The interface for storage/volume/pool collections:

1. driver_api.get_storage(context, storage_id)

2. driver_api.list_pools(context, storage_id)

3. driver_api.list_volumes(context, storage_id)


##### Databases Interfaces:
###### DB Storage Interface
1. db.storage_get(context, storage_id)

2. db.storage_update(context, storage_id, storage)

3. db.storage_delete(context, storage_id)

###### DB Pool Interface
4. db.storage_pool_get_all(context, filters={"storage_id": storage_id})

5. db.storage_pools_delete(context, delete_id_list)

6. db.storage_pools_update(context, update_list)

7. db.storage_pools_create(context, add_list)

###### DB Volume Interface
8. db.storage_volume_get_all(context, filters={"storage_id": storage_id})

9. db.storage_volumes_delete(context, delete_id_list)

10. db.storage_volumes_update(context, update_list)

11. db.storage_volumes_create(context, add_list)

###### DB AccessInfo Interface
12. db.access_info_delete(context, storage_id)

###### DB Alertsource Interfaces
13. db.alert_source_delete(context, storage_id)


### Data View

#### Data Model
Data Model can be referred from [here](https://github.com/sodafoundation/design-specs/blob/master/specs/SIM/SODA_InfrastructureManagerDesign.md#data-model)


### Development and Deployment Context

#### Code
The module code can be found [here](https://github.com/sodafoundation/delfin/tree/master/delfin/task_manager)

### Execution View

NA


## Sequence Diagrams

Below are sequence diagrams of the resource management module.

1. POST (Add a device ): It is a POST call from API Server to Registration manager

![Resource Synchronization](ResourceSynchronozation.jpg)

## Design Alternatives and other notes

NA


## Open Issues

NA


## Design Requirements / Tasks

NA

## Scratchpad

NA
