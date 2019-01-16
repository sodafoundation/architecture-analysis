# API Framework Refactoring

**Author(s)**: [Leon Wang](https://github.com/leonwanghui)

## Summary

This design is proposed to decouple the logic of api-server from `opensds-controller` service daemon, which enables the
standard three-layer architecture: `api-server`, `controller` and `dock`.

## Motivation

Currently api-server and controller modules are tightly coupled into one process. Although they are already divided into
different modules, there are still some problems when coming across these situations:
* controller module is NOT REQUIRED in thin-opensds scenario.
* api-server should support scaling-out when meeting heavy workloads.
* Coupling api-server and controller is not suitable for async communication.

Considering those problems above, decoupling api-server from `osdslet` daemon is on high demand.

### Goals

* Decouple opensds api-server from opensds-controller and run them as separate processes.
* Choose `grpc` as the communication mechanism between api-server and controller.
* For protobuf model defined for grpc communicating between api-server and controller, it should keep the same model
with what defined in dock module.

### Non-Goals

* Enable `real` async communication between api-server and controller, so that the system can respond to long
time-consuming operations to users immediately.
* Update the global configuration and deployment scripts (in opensds-installer). 

## Design Details

To achieve the goal above, there are mainly several items should be focused:

##### api-server construction
This design requires constructing a new entry into `api-server` service, which means some logic code inside the current
`osdslet` service should be moved to another module called `api-server`. As for the code style and required dependencies,
we can refer to the current framework.

##### grpc mechanism
Since we choose `grpc` as the communication mechanism, then we should design data model and `ControllerGrpcServer` and
`ControllerGrpcClient` to enable e2e workflow. Thankfully we can borrow some idea from dock module design and then there
would be a lot of boilerplate code to be reduced.

##### global configuration
With this design proposal, some global configuration structure should be updated as follows:
```go
type Config struct {
	Default           `conf:"default"`
	OsdsApiServer     `conf:"osdsapiserver"`
	OsdsLet           `conf:"osdslet"`
	OsdsDock          `conf:"osdsdock"`
	Database          `conf:"database"`
	KeystoneAuthToken `conf:"keystone_authtoken"`
	Flag              FlagSet
}

type OsdsApiServer struct {
	ApiEndpoint       string        `conf:"api_endpoint,localhost:50040"`
	AuthStrategy      string        `conf:"auth_strategy,noauth"`
	PolicyPath        string        `conf:"policy_path,/etc/opensds/policy.json"`
	Daemon            bool          `conf:"daemon,false"`
	LogFlushFrequency time.Duration `conf:"log_flush_frequency,5s"` // Default value is 5s
}

type OsdsLet struct {
	ApiEndpoint       string        `conf:"api_endpoint,localhost:50049"`
	Daemon            bool          `conf:"daemon,false"`
	LogFlushFrequency time.Duration `conf:"log_flush_frequency,5s"` // Default value is 5s
}
```

##### unified protobuf model
If we consider to make grpc communication easier to maintain, then we should design a unified protobuf model for the
whole opensds system, which means we only need to define one protobuf file and all modules of opensds could import it
if required.

But to achieve this goal, we would change a lot of code in controller module, so it's better to split this change into
separate PR after the items above are resolved.

### Data model impact

Because grpc model requires generated from protobuf, so some protobuf files should be added:
```shell
service Controller {
    // Create a volume
    rpc CreateVolume (CreateVolumeOpts) returns (GenericResponse){}
    
    // Delete a volume
    rpc DeleteVolume (DeleteVolumeOpts) returns (GenericResponse){}
    
    // Extend a volume
    rpc ExtendVolume (ExtendVolumeOpts) returns (GenericResponse){}
    
    // Create a volume snapshot
    rpc CreateVolumeSnapshot (CreateVolumeSnapshotOpts) 
      returns (GenericResponse){}
    
    // Delete a volume snapshot
    rpc DeleteVolumeSnapshot (DeleteVolumeSnapshotOpts) 
      returns (GenericResponse){}
    
    // Create a volume attachment
    rpc CreateVolumeAttachment (CreateVolumeAttachmentOpts)
      returns (GenericResponse){}
    
    // Delete a volume attachment
    rpc DeleteVolumeAttachment (DeleteVolumeAttachmentOpts)
      returns (GenericResponse){}

    // Create a replication
    rpc CreateReplication (CreateReplicationOpts) returns (GenericResponse){}

    // Delete a replication
    rpc DeleteReplication (DeleteReplicationOpts) returns (GenericResponse){}

    // Enable a replication
    rpc EnableReplication (EnableReplicationOpts) returns (GenericResponse){}

    // Disable a replication
    rpc DisableReplication (DisableReplicationOpts) returns (GenericResponse){}

    // Failover a replication
    rpc FailoverReplication (FailoverReplicationOpts) returns (GenericResponse){}

    // Create a volume group
    rpc CreateVolumeGroup (CreateVolumeGroupOpts) returns (GenericResponse){}
	
    // Update volume group
    rpc UpdateVolumeGroup (UpdateVolumeGroupOpts) returns (GenericResponse){}
	
    // Delete volume group
    rpc DeleteVolumeGroup (DeleteVolumeGroupOpts) returns (GenericResponse){}}

// CreateVolumeOpts is a structure which indicates all required properties
// for creating a volume.
message CreateVolumeOpts {
    // The message of request, required.
    string message = 1;
    // The Context
    string context = 2;
}

// DeleteVolumeOpts is a structure which indicates all required properties
// for deleting a volume.
message DeleteVolumeOpts {
    // The message of request, required.
    string message = 1;
    // The Context
    string context = 2;
}

// ExtendVolumeOpts is a structure which indicates all required properties
// for Extending a volume.
message ExtendVolumeOpts {
    // The id of volume, required.
    string id = 1;
    // The message of request, required.
    string message = 2;
    // The Context
    string context = 3;
}

// CreateVolumeAttachmentOpts is a structure which indicates all required properties
// for creating a volume attachment.
message CreateVolumeAttachmentOpts {
    // The message of request, required.
    string message = 1;
    // The Context
    string context = 2;
}

// CreateVolumeAttachmentOpts is a structure which indicates all required properties
// for deleting a volume attachment.
message DeleteVolumeAttachmentOpts {
    // The message of request, required.
    string message = 1;
    // The Context
    string context = 2;
}

// CreateVolumeSnapshotOpts is a structure which indicates all required properties
// for creating a volume snapshot.
message CreateVolumeSnapshotOpts {
    // The message of request, required.
    string message = 1;
    // The Context
    string context = 2;
}

// DeleteVolumeSnapshotOpts is a structure which indicates all required properties
// for deleting a volume snapshot.
message DeleteVolumeSnapshotOpts {
    // The message of request, required.
    string message = 1;
    // The Context
    string context = 2;
}

// CreateReplicationOpts is a structure which indicates all required properties
// for creating a replication.
// NOTE: Need to figure out how to handle more than 2 sites.
message CreateReplicationOpts {
    // The message of request, required.
    string message = 1;
    // The Context
    string context = 2;
}

// Delete ReplicationOpts is a structure which indicates all required properties
// for deleting a replication.
// NOTE: Need to figure out how to handle more than 2 sites.
message DeleteReplicationOpts {
    // The message of request, required.
    string message = 1;
    // The Context
    string context = 2;
}


// Delete ReplicationOpts is a structure which indicates all required properties
message EnableReplicationOpts {
    // The message of request, required.
    string message = 1;
    // The Context
    string context = 2;
}

// Delete ReplicationOpts is a structure which indicates all required properties
message DisableReplicationOpts {
    // The message of request, required.
    string message = 1;
    // The Context
    string context = 2;
}

// Delete ReplicationOpts is a structure which indicates all required properties
message FailoverReplicationOpts {
    // The message of request, required.
    string message = 1;
    string failoverMessage = 2;
    // The Context
    string context = 3;
}

// CreateVolumeGroupOpts is a structure which indicates all required
// properties for creating a volume group.
message CreateVolumeGroupOpts {
    // The message of request, required.
    string message = 1;
    // The Context
    string context = 2;
}

message UpdateVolumeGroupOpts{
    // The message of request, required.
    string message = 1;
    repeated string addVolMessage = 2;
    repeated string rmVolMessage = 3;
    // The Context
    string context = 4;
}

message DeleteVolumeGroupOpts{
    // The message of request, required.
    string message = 1;
    // The Context
    string context = 2;
}

message GenericResponse {}
```

### REST API impact

None

### Security impact

None

### Other end user impact

None

### Performance impact

##### Pros
Decoupling `api-server` from `controller` would make `api-server` much easier to scale out, and therefore improving
performance when handling heavy workloads.

##### Cons
There is no doubt that the time for handling every single request would increase a bit because of grpc communication,
but actually it could be ignored for async operations and it has no impact on users' experience.

### Other deployer impact

We would have a standalone `osds-apiserver` process installed, so there would be some changes in `opensds.conf` file,
and this is an updated example below:
```conf
[osdsapiserver]
api_endpoint = 0.0.0.0:50040
log_file = /var/log/opensds/osdsapiserver.log

[osdslet]
api_endpoint = 0.0.0.0:50049
log_file = /var/log/opensds/osdslet.log

[osdsdock]
api_endpoint = 0.0.0.0:50050
log_file = /var/log/opensds/osdsdock.log
# Choose the type of dock resource, only support 'provisioner' and 'attacher'.
dock_type = provisioner
# Specify which backends should be enabled, sample,ceph,cinder,lvm and so on.
enabled_backends = sample

[sample]
name = sample
description = Sample Test
driver_name = sample

[ceph]
name = ceph
description = Ceph Test
driver_name = ceph
config_path = /etc/opensds/driver/ceph.yaml

[cinder]
name = cinder
description = Cinder Test
driver_name = cinder
config_path = /etc/opensds/driver/cinder.yaml

[lvm]
name = lvm
description = LVM Test
driver_name = lvm
config_path = /etc/opensds/driver/lvm.yaml
host_based_replication_driver = DRBD

[huawei_dorado]
name = dorado
description = dorado Test
driver_name = dorado
config_path = /etc/opensds/driver/dorado.yaml
replication_type = array_based

[huawei_fusionstorage]
name = fusionstorage backend
description = This is a fusionstorage backend service
driver_name = huawei_fusionstorage
config_path = /etc/opensds/driver/fusionstorage.yaml

[database]
credential = opensds:password@127.0.0.1:3306/dbname
endpoint = localhost:2379,localhost:2380
driver = etcd
```

### Developer impact

None

## Use Cases

See [Motivation](#motivation) section.

## Implementation

See [Design Details](#design-details) section.

## Alternatives considered

There would be some other communication mechanisms could be considered, but currently `grpc` is recommended as the
default option. 

## Open issues

* There are some remaining issues about async handling in `osds-apiserver` process, we would try to resolve them after
this proposal get merged.
* In the Phase 2 we would design a unified protobuf model utilized by all modules, which would be a separate PR after
the items above are resolved.