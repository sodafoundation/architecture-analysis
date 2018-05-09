# Design to Define Storage Pool Properties

**Status**: Completed

**Version**: Alpha

**Author(s)**: Leon Wang (@leonwanghui)

## Summary

This proposal is designed for standardizing the properties definition of storage
pool. This design is also part of referenced implementation of LineOfService
defined in [Swordfish](https://www.snia.org/sites/default/files/SMI/swordfish/v106/Swordfish_v1.0.6_Specification.pdf)
specification (v1.0.6), which promises to provide a unified approach for the
management of storage and servers in hyperscale and cloud infrastructure
environments.

## Motivation

As we all know, we did a lot of work to design OpenSDS Profile (see 
[here](https://docs.google.com/document/d/1irNnz019j0XuW6SZNigs6QuYFOCC3uL44EkRYJiHyq8/edit?usp=sharing)).
In addition, the extensible [capabilties filter](https://github.com/opensds/opensds/pull/297)
mechanism was also finished several days ago. This design spec proposes how to
implement the StoragePool resources based on the Swordfish spec.

Now that we have come to a conclusion about `Profile` design, it would be much
easier to design `StoragePool` because both of them are composed of
LineOfService defined in Swordfish.

## Proposal

### Goals

* Redesign the definition of `ExtraSpec` contained in `StoragePoolSpec`.

* Update all storage drivers to stay up with the changes.

* Update the fake pool data declared in test package.

### Data model impact

This proposal will update the definition of `StoragePoolSpec` with these changes below:

* DataStorageLoS
```go
// DataStorageLoS can be used to describe a service option covering storage
// provisioning and availability.
type DataStorageLoS struct {
	// The enumeration literal specifies the time after a disaster that the
	// client shall regain conformant service level access to the primary
	// store. The possible values of this property could be:
	// * OnlineActive: Active access to synchronous replicas.
	// * OnlinePassive: Passive access to replicas via the same front-end
	//   interconnect.
	// * Nearline: Access to replica via a different front-end interconnect. A
	//   restore step is required before recovery can commence.
	// * Offline: No direct connection to the replica. (i.e. To a bunker
	//   containing backup media.)
	//
	// The expectation is that the services required to implement this
	// capability are part of the advertising system.
	RecoveryTimeObjective string `json:"recoveryTimeObjective,omitempty" yaml:"recoveryTimeObjective,omitempty"`

	// ProvisioningPolicy shall define the provisioning policy for storage.
	// The possible values of this property could be:
	// * Thick: This enumeration literal specifies storage shall be fully
	//   allocated.
	// * Thin: This enumeration literal specifies storage may be over allocated.
	ProvisioningPolicy string `json:"provisioningPolicy,omitempty" yaml:"provisioningPolicy,omitempty"`

	// IsSpaceEfficient indicates that the storage is compressed or deduplicated.
	// The default value for this prperty is false.
	IsSpaceEfficient bool `json:"isSpaceEfficient,omitempty" yaml:"isSpaceEfficient,omitempty"`
}
```

* IOConnectivityLoS
```go
// IOConnectivityLoS can be used to specify the characteristics of storage
// connectivity.
type IOConnectivityLoS struct {
	// The enumeration literal shall specify the Access protocol for this
	// service option. The possible values of this property could be:
	// * iSCSI: Internet SCSI.
	// * RBD: Ceph RBD protocol.
	// * FC: Fibre Channel.
	// * NVMeOverFabrics: NVMe over Fabrics.
	AccessProtocol string `json:"accessProtocol,omitempty" yaml:"accessProtocol,omitempty"`

	// MaxIOPS shall be the maximum IOs per second that the connection shall
	// allow for the selected access protocol.
	// +units:[IO]/s
	MaxIOPS int64 `json:"maxIOPS,omitempty" yaml:"maxIOPS,omitempty"`

	// MaxBWS shall be the the maximum bandwidth in Mbps that a connection can
	// support.
	// +units:Mb/s
	MaxBWS int64 `json:"maxBWS,omitempty" yaml:"maxBWS,omitempty"`
}
```

* DataProtectionLoS
```go
// DataProtectionLoS describes a replica that protects data from loss. The
// requirements must be met collectively by the communication path and the
// replica.
// The expectation is that the services required to implement this
// capability are part of the advertising system.
type DataProtectionLoS struct {
	// IsIsolated shall indicate if the replica is in a separate fault domain.
	IsIsolated bool `json:"isIsolated,omitempty" yaml:"isIsolated,omitempty"`

	// MinLifetime shall be an ISO 8601 duration that specifies the minimum
	// required lifetime of the replica. For example, "P3Y6M4DT12H30M5S"
	// represents a duration of "3 years, 6 months, 4 days, 12 hours, 30 minutes
	// and 5 seconds".
	MinLifetime string `json:"minLifetime,omitempty" yaml:"minLifetime,omitempty"`

	// The enumeration literal specifies the geograhic scope of the failure
	// domain, and currently contains these options:
	// * Datacenter: A facility that provides communication, power, or cooling
	//   infrastructure to a co-located set of servers, networking and storage.
	// * Rack: A container within a datacenter that provides communication,
	//   power, or cooling to a set of components.
	// * RackGroup: A set of racks that may share common communication, power,
	//   or cooling.
	// * Region: A set of resources that are required to be either geographically
	//   or politically isolated from resources not in the resources.
	// * Row: A set of adjacent racks or rackgroups that may share common
	//   communication, power, or cooling.
	// * Server: Components of a CPU/memory complex that share the same
	//   infrastructure.
	RecoveryGeographicObject string `json:"recoveryGeographicObjective,omitempty" yaml:"recoveryGeographicObjective,omitempty"`

	// This value shall be an ISO 8601 duration that specifies the maximum time
	// over which source data may be lost on failure. For example,
	// "P3Y6M4DT12H30M5S" represents a duration of "3 years, 6 months, 4 days,
	// 12 hours, 30 minutes and 5 seconds".
	// In the case that IsIsolated = false, failure of the domain is not a
	// consideration.
	RecoveryPointObjectiveTime string `json:"recoveryPointObjectiveTime,omitempty" yaml:"recoveryPointObjectiveTime,omitempty"`

	// The enumeration literal specifies the time after a disaster that the
	// client shall regain conformant service level access to the primary
	// store. The possible values of this property could be:
	// * OnlineActive: Active access to synchronous replicas.
	// * OnlinePassive: Passive access to replicas via the same front-end
	//   interconnect.
	// * Nearline: Access to replica via a different front-end interconnect. A
	//   restore step is required before recovery can commence.
	// * Offline: No direct connection to the replica. (i.e. To a bunker
	//   containing backup media.)
	RecoveryTimeObjective string `json:"recoveryTimeObjective,omitempty" yaml:"recoveryTimeObjective,omitempty"`

	// The enumeration literals may be used to specify the intended outcome of
	// the replication. The possible values of this property could be:
	// * Clone: This enumeration literal shall indicate that replication shall
	//   create a point in time, full copy the source.
	// * Mirror: This enumeration literal shall indicate that replication shall
	//   create and maintain a copy of the source.
	// * Snapshot: This enumeration literal shall indicate that replication
	//   shall create a point in time, virtual copy of the source.
	// * TokenizedClone: This enumeration literal shall indicate that replication
	//   shall create a token based clone.
	ReplicaType string `json:"replicaType,omitempty" yaml:"replicaType,omitempty"`
}
```

* StoragePoolExtraSpec
```go
type StoragePoolExtraSpec struct {
	// DataStorage represents some suggested data storage capabilities.
	DataStorage DataStorageLoS `json:"dataStorage,omitempty" yaml:"dataStorage,omitempty"`
	// IOConnectivity represents some suggested IO connectivity capabilities.
	IOConnectivity IOConnectivityLoS `json:"ioConnectivity,omitempty" yaml:"ioConnectivity,omitempty"`
	// DataProtection represents some suggested data protection capabilities.
	DataProtection DataProtectionLos `json:"dataProtection,omitempty" yaml:"dataProtection,omitempty"`

	// Besides those basic suggested pool properties, vendors can configure
	// some advanced features (such as diskType, latency, compression, etc)
	// themselves, all these properties can be exposed to controller scheduler
	// and filtered by selector in a extensible way.
	Advanced map[string]interface{} `json:"advanced,omitempty" yaml:"advanced,omitempty"`
}
```

### REST API impact

None

### Security impact

None

### Other end user impact

None

### Performance impact

Compared with before, there would be some performance loss when encoding and
decoding the StoragePoolSpec. But we can improve the performance like this:
```go
type StoragePoolExtraSpec struct {
	// DataStorage represents some suggested data storage capabilities.
	DataStorage *DataStorageLoS `json:"dataStorage,omitempty" yaml:"dataStorage,omitempty"`
	// IOConnectivity represents some suggested IO connectivity capabilities.
	IOConnectivity *IOConnectivityLoS `json:"ioConnectivity,omitempty" yaml:"ioConnectivity,omitempty"`
	// DataProtection represents some suggested data protection capabilities.
	DataProtection *DataProtectionLos `json:"dataProtection,omitempty" yaml:"dataProtection,omitempty"`

	// Besides those basic suggested pool properties, vendors can configure
	// some advanced features (such as diskType, latency, compression, etc)
	// themselves, all these properties can be exposed to controller scheduler
	// and filtered by selector in a extensible way.
	Advanced map[string]interface{} `json:"advanced,omitempty" yaml:"advanced,omitempty"`
}
```

### Other deployer impact

If this prososal gets accepted, the backend configuration file (take `lvm` for
example) should be updated like this:
```yaml
tgtBindIp: 192.168.0.105
tgtConfDir: /etc/tgt/conf.d
pool:
  opensds-default:
    storageType: block
    availabilityZone: default
    extras:
      dataStorage:
        provisioningPolicy: Thin
        isSpaceEfficient: true
      ioConnectivity:
        accessProtocol: iSCSI
        maxIOPS: 8000000
        maxBWS: 700
      dataProtection:
        isIsolated: true
        minLifetime: P0Y0M7DT0H0M0S
        recoveryGeographicObjective: Region
        RecoveryPointObjectiveTime: P0Y0M0DT12H30M5S
        RecoveryTimeObjective: Nearline
        ReplicaType: Snapshot
      advanced:
        diskType: SSD
        latency: 3ms
```

### Developer impact

Because this proposal has modified the `StoragePoolSpec` definition, all related
packages (including db, api and so on) could be affected.

## Use Cases

* Multi-tenants resource provisioning

After the system initiated, the capabilities report thread will be triggered
automatically and then capabilities properties of storage pool resource will be
stored in database. After that, admin A creates a `ProvisionProfile` which can
be viewed by all users, so user B can check all available profiles and choose
one to create a volume.

Currently the `ExtraSpec` defined in `ProfileSpec` is k-v pair, so an example
of ProvisionProfile is as follows:
```go
SampleProfile = model.ProfileSpec{
	BaseModel: &model.BaseModel{
		Id: "2f9c0a04-66ef-11e7-ade2-43158893e017",
	},
	Name:        "silver",
	Description: "silver policy",
	Extras: model.ExtraSpec{
		"dataStorage": map[string]interface{}{
			"provisioningPolicy": "Thin",
			"isSpaceEfficient":   true,
		},
		"ioConnectivity": map[string]interface{}{
			"accessProtocol": "iSCSI",
			"maxIOPS":        float64(8000000),
		},
		"advanced": map[string]interface{}{
			"diskType": "SSD",
			"latency": "3ms",
		},
	},
}
```

## Implementation

* Update `StoragePool` schema (see `Data model` section) in model package.
* Modify `ListPools` method in all storage drivers. (Only several lines changed)
* Update some fake data defined in `test/collection/data.go`.
* Rename `ExtraSpec` (contained in StoragePoolSpec) to `StoragePoolExtraSpec`.

## Alternatives considered

None

## Open issues

* This proposal is focusing on storage provision scenario, so all the storage
pool properties are based on the current definition of `ProvisionProfile`. If
other profiles (such as `ReplicationProfile`, `MigrationProfile`) are designed
later, there would be some changes in pool properties.
* Currently the design of `ReplicationProfile` also contains the properties
`RecoveryTimeObjectives`, from my perspective there would no impact to the
current design, because they are different use cases for users: one for data
provisioning service and the other one is for data replication service. And the
reason for adding `RTO` in DataStorageLoS is that user may also need HA when
they ask for storage provsioning service, but this feature is definitely
optional because not every vendor are not willing to implement this feature.
* For `FreeCapacity` definition in `StoragePoolSpec`, it's a long term to
enable `thin-provisioning` feature for all storage drivers, and currently we
suppose all storage pools are full-allocated to avoid over-provision situation.
