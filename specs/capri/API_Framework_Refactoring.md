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
* Provide a unifed model definition for api-server, controller and dock modules by refactoring current defined protobuf model.
* Nomalize the status machine design and only allow controller module to handle it.

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

##### status machine machanism
Currently the status machine logic is at a chaos, there is no restriction for allowing specific role accessing the database
and update resource status. After decoupling api-server from controlelr module, only controller has the access to update
the status of resource, and for security concerns, dock module should not even try to access the database.

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
}

type OsdsApiServer struct {
	ApiEndpoint        string        `conf:"api_endpoint,localhost:50040"`
	AuthStrategy       string        `conf:"auth_strategy,noauth"`
	Daemon             bool          `conf:"daemon,false"`
	PolicyPath         string        `conf:"policy_path,/etc/opensds/policy.json"`
	LogFlushFrequency  time.Duration `conf:"log_flush_frequency,5s"` // Default value is 5s
	HTTPSEnabled       bool          `conf:"https_enabled,false"`
	BeegoHTTPSCertFile string        `conf:"beego_https_cert_file,/opt/opensds-security/opensds/opensds-cert.pem"`
	BeegoHTTPSKeyFile  string        `conf:"beego_https_key_file,/opt/opensds-security/opensds/opensds-key.pem"`
}

type OsdsLet struct {
	ApiEndpoint       string        `conf:"api_endpoint,localhost:50049"`
	Daemon            bool          `conf:"daemon,false"`
	LogFlushFrequency time.Duration `conf:"log_flush_frequency,5s"` // Default value is 5s
}
```

##### Unified protobuf model
If we consider to make grpc communication easier to maintain, then we should design a unified protobuf model for the
whole opensds system, which means we only need to define one protobuf file and all modules of opensds could import it
if required.

All the protobuf model and generated files would be moved to `pkg/model/proto` module.

### Data model impact

Because grpc model requires generated code from protobuf, so the new protobuf model would be as follows:
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
    rpc DeleteVolumeGroup (DeleteVolumeGroupOpts) returns (GenericResponse){}
}

service ProvisionDock {
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
    rpc DeleteVolumeGroup (DeleteVolumeGroupOpts) returns (GenericResponse){}
}

// CreateVolumeOpts is a structure which indicates all required properties
// for creating a volume.
message CreateVolumeOpts {
    // The uuid of the volume, optional when creating.
    string id = 1;
    // The name of the volume, required.
    string name = 2;
    // The requested capacity of the volume, required.
    int64 size = 3;
    // The description of the volume, optional.
    string description = 4;
    // When create volume from snapshot, this field is required.
    string snapshotId = 5;
    // The locality that volume belongs to, required.
    string availabilityZone = 6;
    // The service level that volume belongs to, required.
    string profileId = 7;
    // The uuid of the pool on which volume will be created, required.
    string poolId = 8;
    // The name of the pool on which volume will be created, required.
    string poolName = 9;    
    // The metadata of the volume, optional.
    map<string, string> metadata = 10;
    // The storage driver type.
    string driverName = 11;
    // The Context
    string context = 12;
    // The replication driver data
    map<string, string> replicationDriverData = 13;
    // The uuid of replication
    string replicationId = 14;
    // The size of snapshot
    int64 snapshotSize = 15;
    // Down load snapshot from cloud
    bool snapshotFromCloud = 16;
}

// DeleteVolumeOpts is a structure which indicates all required properties
// for deleting a volume.
message DeleteVolumeOpts {
    // The uuid of the volume, required.
    string id = 1;
    // The service level that volume belongs to, required.
    string profileId = 2;
    // The uuid of the pool on which volume will be created, required.
    string poolId = 3;
    // The metadata of the volume, optional.
    map<string, string> metadata = 4;
    // The storage driver type.
    string driverName = 5;
    // The Context
    string context = 6;
}

// ExtendVolumeOpts is a structure which indicates all required properties
// for Extending a volume.
message ExtendVolumeOpts {
    // The uuid of the volume, optional when creating.
    string id = 1;
    // The name of the volume, required.
    string name = 2;
    // The requested capacity of the volume, required.
    int64 size = 3;
    // The description of the volume, optional.
    string description = 4;
    // When create volume from snapshot, this field is required.
    string snapshotId = 5;
    // The locality that volume belongs to, required.
    string availabilityZone = 6;
    // The service level that volume belongs to, required.
    string profileId = 7;
    // The uuid of the pool on which volume will be created, required.
    string poolId = 8;
    // The name of the pool on which volume will be created, required.
    string poolName = 9;    
    // The metadata of the volume, optional.
    map<string, string> metadata = 10;
    // The storage driver type.
    string driverName = 11;
    // The Context
    string context = 12;
}

// CreateVolumeSnapshotOpts is a structure which indicates all required
// properties for creating a volume snapshot.
message CreateVolumeSnapshotOpts {
    // The uuid of the volume snapshot, optional.
    string id = 1;
    // The name of the volume snapshot, required.
    string name = 2;
    // The size of the volume that snapshot belongs to, required.
    int64 size = 3;
    // The description of the volume snapshot, optional.
    string description = 4;
    // The uuid of the volume that snapshot belongs to, required.
    string volumeId = 5;
    // The service level that volume belongs to, required.
    string profileId = 6;
    // The metadata of the volume snapshot, optional.
    map<string, string> metadata = 7;
    // The storage driver type.
    string driverName = 8;
    // The Context
    string context = 9;
}

// DeleteVolumeSnapshotOpts is a structure which indicates all required
// properties for deleting a volume snapshot.
message DeleteVolumeSnapshotOpts {
    // The uuid of the volume snapshot, required.
    string id = 1;
    // The uuid of the volume that snapshot belongs to, required.
    string volumeId = 2;
    // The metadata of the volume snapshot, optional.
    map<string, string> metadata = 3;
    // The storage driver type.
    string driverName = 4;
    // The Context
    string context = 5;
}

// CreateVolumeAttachmentOpts is a structure which indicates all required
// properties for creating a volume attachment.
message CreateVolumeAttachmentOpts {
    // The uuid of the volume attachment, optional.
    string id = 1;
    // The uuid of the volume, required.
    string volumeId = 2;
    // This field indicates if the volume is attached locally, optional.
    bool doLocalAttach = 3;
    // This field indicates if the volume is attached multiple times, optional.
    bool multiPath = 4;
    // The infomation of the host node on which the volume will be attached.
    HostInfo hostInfo = 5;
    // The metadata of the volume attachment, optional.
    map<string, string> metadata = 6;
    // The storage driver type.
    string driverName = 7;
    // The Context
    string context = 8;
    // The protocol
    string AccessProtocol = 9;
}

// DeleteVolumeAttachmentOpts is a structure which indicates all required
// properties for deleting a volume attachment.
message DeleteVolumeAttachmentOpts {
    // The uuid of the volume attachment, optional.
    string id = 1;
    // The uuid of the volume, required.
    string volumeId = 2;
    // The infomation of the host node on which the volume will be attached.
    HostInfo hostInfo = 3;
    // The metadata of the volume attachment, optional.
    map<string, string> metadata = 4;
    // The storage driver type.
    string driverName = 5;
    // The Context
    string context = 6;
    // The protocol
    string AccessProtocol = 7;
}

// CreateSnapshotAttachmentOpts is a structure which indicates all required
// properties for creating a snapshot attachment.
message CreateSnapshotAttachmentOpts {
    // The uuid of the snapshot attachment, optional.
    string id = 1;
    // The uuid of the snapshot, required.
    string snapshotId = 2;
    // This field indicates if the snapshot is attached locally, optional.
    bool doLocalAttach = 3;
    // This field indicates if the snapshot is attached multiple times, optional.
    bool multiPath = 4;
    // The infomation of the host node on which the snapshot will be attached.
    HostInfo hostInfo = 5;
    // The metadata of the snapshot attachment, optional.
    map<string, string> metadata = 6;
    // The storage driver type.
    string driverName = 7;
    // The Context
    string context = 8;
    // The protocol
    string AccessProtocol = 9;
}

// DeleteSnapshotAttachmentOpts is a structure which indicates all required
// properties for deleting a snapshot attachment.
message DeleteSnapshotAttachmentOpts {
    // The uuid of the snapshot attachment, optional.
    string id = 1;
    // The uuid of the snapshot, required.
    string snapshotId = 2;
    // The infomation of the host node on which the volume will be attached.
    HostInfo hostInfo = 3;
    // The metadata of the snapshot attachment, optional.
    map<string, string> metadata = 4;
    // The storage driver type.
    string driverName = 5;
    // The Context
    string context = 6;
    // The protocol
    string AccessProtocol = 7;
}

message HostInfo {
    // The platform of the host, such as "x86_64"
    string platform = 1;
    // The type of OS, such as "linux","windows", etc.
    string osType = 2;
    // The name of the host
     string host = 3;
    // The ip address of the host
    string ip = 4;
    // The initiator infomation, such as: "iqn.2017.com.redhat:e08039b48d5c"
    string initiator = 5;
}

message VolumeData {
    map<string, string> data = 1;
}

// CreateReplicationOpts is a structure which indicates all required properties
// for creating a replication.
// NOTE: Need to figure out how to handle more than 2 sites.
message CreateReplicationOpts {
    // The uuid of the replication, optional when creating.
    string id = 1;
    // The name of the replication, optional.
    string name = 2;
    // The description of the replication, optional.
    string description = 3;
    // The uuid of the primary volume. This field is required.
    string primaryVolumeId = 4;
    // The uuid of the secondary volume. This field is required.
    string secondaryVolumeId = 5;
    // The dock infomation on which the request will be executed
    string availabilityZone = 6;
    // The service level that volume belongs to, required.
    string profileId = 7;
    // The uuid of the pool on which volume will be created, required.
    string poolId = 8;
    // The name of the pool on which volume will be created, required.
    string poolName = 9;
    // The metadata of the primary replication, optional.
    map<string, string> primaryReplicationDriverData = 11;
    // The metadata of the seondary replication, optional.
    map<string, string> secondaryReplicationDriverData = 12;
    // The dock id.
    string dockId = 13;
    // The replication driver type.
    string driverName = 14;
    // The Context
    string context = 15;
    // Whether is primary replication
    bool  isPrimary = 16;
    // The replication metadata
    map<string, string> metadata = 17;
    // The volume data list
    repeated  VolumeData volumeDataList = 18;
    //the replication mode sync/async
    string replicationMode = 19;
    // 0 means sync replication.
    int64 ReplicationPeriod = 20;
    // replication bandwidth
    int64 ReplicationBandwidth = 21;
}

// Delete ReplicationOpts is a structure which indicates all required properties
// for deleting a replication.
// NOTE: Need to figure out how to handle more than 2 sites.
message DeleteReplicationOpts {
    // The uuid of the replication, optional when creating.
    string id = 1;
    // The name of the replication, optional.
    string name = 2;
    // The description of the replication, optional.
    string description = 3;
    // The uuid of the primary volume. This field is required.
    string primaryVolumeId = 4;
    // The uuid of the secondary volume. This field is required.
    string secondaryVolumeId = 5;
    // The dock infomation on which the request will be executed
    string availabilityZone = 6;
    // The service level that volume belongs to, required.
    string profileId = 7;
    // The uuid of the pool on which volume will be created, required.
    string poolId = 8;
    // The name of the pool on which volume will be created, required.
    string poolName = 9;
    // The metadata of the primary replication, optional.
    map<string, string> primaryReplicationDriverData = 11;
    // The metadata of the seondary replication, optional.
    map<string, string> secondaryReplicationDriverData = 12;
    // The dock id.
    string dockId = 13;
    // The replication driver type.
    string driverName = 14;
    // The Context
    string context = 15;
    // The replication metadata
    map<string, string> metadata = 16;
    // Whether is primary replication
    bool  isPrimary = 17;
}


// Delete ReplicationOpts is a structure which indicates all required properties
message EnableReplicationOpts {
    // The uuid of the replication, optional when creating.
    string id = 1;
    // The name of the replication, optional.
    string name = 2;
    // The description of the replication, optional.
    string description = 3;
    // The uuid of the primary volume. This field is required.
    string primaryVolumeId = 4;
    // The uuid of the secondary volume. This field is required.
    string secondaryVolumeId = 5;
    // The dock infomation on which the request will be executed
    string availabilityZone = 6;
    // The service level that volume belongs to, required.
    string profileId = 7;
    // The uuid of the pool on which volume will be created, required.
    string poolId = 8;
    // The name of the pool on which volume will be created, required.
    string poolName = 9;
    // The metadata of the primary replication, optional.
    map<string, string> primaryReplicationDriverData = 11;
    // The metadata of the seondary replication, optional.
    map<string, string> secondaryReplicationDriverData = 12;
    // The dock id.
    string dockId = 13;
    // The replication driver type.
    string driverName = 14;
    // The Context
    string context = 15;
    // The replication metadata
    map<string, string> metadata = 16;
    // Whether is primary replication
    bool  isPrimary = 17;
}

// Delete ReplicationOpts is a structure which indicates all required properties
message DisableReplicationOpts {
    // The uuid of the replication, optional when creating.
    string id = 1;
    // The name of the replication, optional.
    string name = 2;
    // The description of the replication, optional.
    string description = 3;
    // The uuid of the primary volume. This field is required.
    string primaryVolumeId = 4;
    // The uuid of the secondary volume. This field is required.
    string secondaryVolumeId = 5;
    // The dock infomation on which the request will be executed
    string availabilityZone = 6;
    // The service level that volume belongs to, required.
    string profileId = 7;
    // The uuid of the pool on which volume will be created, required.
    string poolId = 8;
    // The name of the pool on which volume will be created, required.
    string poolName = 9;
    // The metadata of the primary replication, optional.
    map<string, string> primaryReplicationDriverData = 11;
    // The metadata of the seondary replication, optional.
    map<string, string> secondaryReplicationDriverData = 12;
    // The dock id.
    string dockId = 13;
    // The replication driver type.
    string driverName = 14;
    // The Context
    string context = 15;
    // The replication metadata
    map<string, string> metadata = 16;
    // Whether is primary replication
    bool  isPrimary = 17;
}

// Delete ReplicationOpts is a structure which indicates all required properties
message FailoverReplicationOpts {
    // The uuid of the replication, optional when creating.
    string id = 1;
    // The name of the replication, optional.
    string name = 2;
    // The description of the replication, optional.
    string description = 3;
    // The uuid of the primary volume. This field is required.
    string primaryVolumeId = 4;
    // The uuid of the secondary volume. This field is required.
    string secondaryVolumeId = 5;
    // The dock infomation on which the request will be executed
    string availabilityZone = 6;
    // The service level that volume belongs to, required.
    string profileId = 7;
    // The uuid of the pool on which volume will be created, required.
    string poolId = 8;
    // The name of the pool on which volume will be created, required.
    string poolName = 9;
    // The metadata of the primary replication, optional.
    map<string, string> primaryReplicationDriverData = 11;
    // The metadata of the seondary replication, optional.
    map<string, string> secondaryReplicationDriverData = 12;
    // The dock id.
    string dockId = 13;
    // The replication driver type.
    string driverName = 14;
    // The Context
    string context = 15;
    // Allow attached volume
    bool allowAttachedVolume = 16;
    // The secondary backend id.
    string secondaryBackendId = 17;
    // The replication metadata
    map<string, string> metadata = 18;
    // Whether is primary replication
    bool  isPrimary = 19;
    message FailoverRequest {
        bool allowAttachedVolume = 1;
        string secondaryBackendId = 2;
    }
}

// CreateVolumeGroupOpts is a structure which indicates all required
// properties for creating a volume group.
message CreateVolumeGroupOpts {
    // The uuid of the volume group, optional when creating.
    string id = 1;
    // The name of the volume, required.
    string name = 2;
    // The driver of the volume group.
    string driverName = 3;
    // The description of the volume, optional.
    string description = 4;
    // When create volume from snapshot, this field is required.
    string availabilityZone = 5;
    // The addVolumes contain UUIDs of volumes to be added to the group.
    repeated string addVolumes = 6;
    // The removeVolumes contains the volumes to be removed from the group.
    repeated string removeVolumes = 7;
    // The pool belongs to the group.
    string poolId =8;
    // The Context
    string context = 9;
}

message UpdateVolumeGroupOpts{
    // The uuid of the volume group, optional when updating.
    string id = 1;
    // The driver of the volume group.
    string driverName = 2;
    // The addVolumes contain UUIDs of volumes to be added to the group.
    repeated string addVolumes = 3;
    // The removeVolumes contains the volumes to be removed from the group.
    repeated string removeVolumes = 4;
    // The pool belongs to the group.
    string poolId =5;
    // The Context
    string context = 6;
}

message DeleteVolumeGroupOpts{
    // The uuid of the volume group, optional when deleting.
    string id = 1;
    // The pool belongs to the group.
    string poolId =2;
    // The driver of the volume group.
    string driverName = 3;
    // The Context
    string context = 4;
}
service AttachDock {
    // Attach a volume
    rpc AttachVolume (AttachVolumeOpts) returns (GenericResponse){}
    
    // Detach a volume
    rpc DetachVolume (DetachVolumeOpts) returns (GenericResponse){}
}

// AttachVolumeOpts is a structure which indicates all required
// properties for attaching a volume.
message AttachVolumeOpts {
	// The access protocol for attaching a volume.
    string accessProtocol = 1;
	// The connectionData for attaching a volume.
	string connectionData = 2;
    // The metadata for attaching a volume, optional.
    map<string, string> metadata = 3;
    // The Context
    string context = 4;
}

// DetachVolumeOpts is a structure which indicates all required
// properties for detaching a volume.
message DetachVolumeOpts {
	// The access protocol for detaching a volume.
    string accessProtocol = 1;
	// The connectionData for detaching a volume.
	string connectionData = 2;
    // The metadata for detaching a volume, optional.
    map<string, string> metadata = 3;
    // The Context
    string context = 4;
}

// Generic response, it return:
// 1. Return result with message when create/update resource successfully.
// 2. Return result without message when delete resource successfully.
// 3. Return Error with error code and message when operate unsuccessfully.
message GenericResponse {
    message Result {
        string message = 1;
    }
    
    message Error {
        string code = 1;
        string description = 2;
    }
    
    oneof reply {
        Result result = 1;
        Error error = 2;
    }
}
```

### REST API impact

None

### Security impact

None

### Other end user impact

None

### Performance impact

##### Pros
Decoupling `api-server` from `controller` would make `api-server` much easier to scale out, and therefore improving performance when handling heavy workloads.

##### Cons
There is no doubt that the time for handling every single request would increase a bit because of gRPC communication, but users would not
perceive the delay after async-conmmunition between api-server and controller module completed.

### Other deployer impact

We would have a standalone `osds-apiserver` process installed, so there would be some changes in `opensds.conf` file, and this is an updated example below:
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

There would be some other communication mechanisms could be considered, but currently `grpc` is recommended as the default option. 

## Open issues

* There are some remaining issues about async handling in `osds-apiserver` process, we would try to resolve them after this proposal gets merged.

* Some operations (update and delete) related volume group resource is so complicated that we have to grant dock module the access to update the status of volume resources.
