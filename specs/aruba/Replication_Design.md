# OpenSDS Replication Design

**Authors:** [Xing Yang](https://github.com/xing-yang), [Lin Xu](https://github.com/wisererik)

## Overview
This design proposal aims to provide support for volume level replications. For group level replication and snapshot, the design will adopt the generic volume groups concept from OpenStack Cinder and will be documented in different specs. APIs will be provided for volume level level replications. This proposal was drafted [here](https://docs.google.com/document/d/1ymjJdBjFntaVcnR-m--VdSILkzOOj3CM4mZA1Sg5Mk0/edit#).

## Proposed Change
* Add new Replication APIs.
* Modify the DR Controller to handle replication operations.
* Modify Selector and Policy Controller to handle replication requests. 
* Change database to handle additional replication related resources.
* Implement interfaces in the southbound volume drivers.
* For host-based replication, a replication plug-in will be added that only implements replication related functions, i.e. volume creation will be handled by the volume drivers.
* Add new Replication CLIs.

### Architecture Diagram
In the following architecture diagram, components in Green are proposed for replication. Note that there will also be changes in db schema and volume drivers that are not shown in Green.
![OpenSDS Architecture Diagram](opensds_arch.png?raw=true "OpenSDS Architecture Diagram")

Array-based replication work flow is shown as follows:
![Array-based Replication Diagram](array_based_replication.png?raw=true "Array-based Replication Diagram")

Setup:

* Two Cinder stand-alones on 2 different Availability Zones under 2 different OpenSDS hubs. Note that it can be two native storage arrays without Cinder as well.
* OpenSDS Controller can be deployed in AZ1 or AZ2 or another AZ.
* Etcd is distributed and communicates with OpenSDS Controller and the Docks.

Array-based replication workflow:

1. Assume volumes are already created in Cinder in AZ1 and AZ2.
2. DR API receives request to create a replication pair.
3. API server calls OpenSDS controller.
4. OpenSDS controller uses Selector to choose the backends (primary and secondary) based on input replication devices info and the profile.
    1. If no backends can meet the requirement, bail out.
    2. The controller knows the primary volume_id and replication_driver_data for both primary and secondary site. It finds the AZ for the primary site, i.e., AZ1. It passes the command to the Dock in AZ1 to execute the action.
5. OpenSDS controller uses DR Controller to start the create replication process.
6. DR Controller goes through Volume Controller to communicate with Docks hosting volume drivers in AZ1 and AZ2 to get volume info.
7. DR Controller creates a replication pair using Volume Driver through array replication mechanism. Two corresponding replication entries will be created in the db (i.e., {“replication_id”: <AB>, “local_volume_id”: <a1>, “remote_volume_id”: <b1>, “replication_master”: true} and {“replication_id”: <BA>, “local_volume_id”: <b1>, “remote_volume_id”: <a1>, “replication_master”: false})
8. OpenSDS controller uses Policy Engine to register async policies to run later.

Note:

* This diagram contains two AZs in the same region. For cross region replication, we will have one OpenSDS Controller manage one region, and an orchestrator coordinate the two regions.  There will be one etcd db per region that communicates with the OpenSDS Controller and the Docks in that region. One replication entry will be created in the db in each region after the create replication operation.  (i.e., {“replication_id”: <AB>, “local_volume_id”: <a1>, “remote_volume_id”: <b1>, “replication_master”: true} in region1 and {“replication_id”: <BA>, “local_volume_id”: <b1>, “remote_volume_id”: <a1>, “replication_master”: false} in region2)  
* Multiple DC, cross-region replication is important but we will focus on solving single site problem first.

![Create Replication Diagram](create_replication_array_based.png?raw=true "Create Replication Diagram")

In this multi-region deployment, database is distributed across regions.  Selector and policy engine are not shown in this diagram for simplicity. Controller refers to the volume controller plus DR controller.

1. Controller 1 creates source volume
    * Creates entry in db
    * Creates volume on array1
2. Controller 1 sends request to controller 2 to create target volume
3. Controller 2 asks backend to create target volume
    * Creates entry in db
    * Creates volume on array2
4. Creates source replication
    * Creates entry in db
    * Creates replication relationship on array1 and array2
5. Controller 1 sends request to controller 2 to create target replication
6. Controller 2 creates entry in db

### Host-based Replication Workflow
Host-based replication serves 2 purposes:
1. Active volume replication requires the host-based replicator (HBR) to be on the same host.
2. Inactive volume replication allows the HBR to be on another host.

![Host-based Replication Diagram](host_based_replication.png?raw=true "Hosted-based Replication Diagram")

Setup:

* Two Cinder stand-alones on 2 different Availability Zones under 2 different OpenSDS hubs.  Note that it can be two storage arrays without Cinder as well.
* There is a corresponding DR hub in each AZ.
* OpenSDS Controller, gRPC server, and etcd can be deployed in AZ1 or AZ2 or another AZ.
* Etcd is distributed and communicates with OpenSDS Controller and the Docks.

Host-based Replication workflow:
1. Assume volumes are already created in Cinder in AZ1 and AZ2 (TODO: Add create volume steps in orchestration workflow later).
2. DR API receives request to create a replication pair.
3. API server calls OpenSDS controller.
4. OpenSDS controller uses Selector to choose the volume backends (primary and secondary) and replicator backend based on input replication devices info and the profile.
    * If no backends can meet the requirement, bail out.
5. OpenSDS controller uses DR Controller to start the create replication process.
6. DR Controller goes through Volume Controller to communicate with Docks hosting volume drivers in AZ1 and AZ2 to get volume info, and also communicates with DR Docks hosting the Replicators.
7. DR Controller calls Volume Controller to attach source volume and target volume to DR Dock host.
8. DR Controller creates a replication pair using the Replication Driver through the Replicator and starts the replication.
9. OpenSDS controller uses Policy Engine to register async policies to run later.


## Volume Level Replication APIs

### Create Replication
Method: Post
URL: v1/{tenant_id}/block/replications

### Index Replication
Method: Get
URL: v1/{tenant_id}/block/replications

### Detail Replication
Method: Get
URL: v1/{tenant_id}/block/replications/detail

### Show Replication
Method: Get
URL: v1/{tenant_id}/block/replications/{id}

### Update Replication
Method: Put
URL: v1/{tenant_id}/block/replications/{id}

### Enable Replication
Method: Post
URL: v1/{tenant_id}/block/replications/{id}/action

### Disable Replication
Method: Post
URL: v1/{tenant_id}/block/replications/{id}/action

### Delete Replication
Method: Delete
URL: v1/{tenant_id}/block/replications/{id}

### Failover Replication
Method: Post
URL: v1/{tenant_id}/block/replications/{id}/action
