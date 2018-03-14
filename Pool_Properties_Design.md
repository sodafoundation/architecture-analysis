# Design to Define Storage Pool Properties

**Status**: WIP

**Version**: Alpha

**Author(s)**: Leon Wang (@leonwanghui)

## Summary

This proposal is designed for standardizing the properties definition of storage pool. This design is also part of referenced implementation of LineOfService defined in [Swordfish](https://www.snia.org/sites/default/files/SMI/swordfish/V105/Swordfish_v1.0.5_Specification.pdf) specification (v1.0.5), which promises to providing a unified approach for the management of storage and servers in hyperscale and cloud infrastructure environments.

## Motivation

During these two months, we did a lot of work to design OpenSDS Profile (see [here](https://docs.google.com/document/d/1irNnz019j0XuW6SZNigs6QuYFOCC3uL44EkRYJiHyq8/edit?usp=sharing)). Besides the extensible [capabilties filter](https://github.com/opensds/opensds/pull/297) mechanism was also finished several days ago. But as for the design of implementation of StoragePool resource, it's still an issue unresolved.

Now that we have come to a conclusion about Profile design, it would be much easier to design StoragePool because both of them are composed of LineOfService defined in Swordfish.

### Goals

* Redesign the definition of `ExtraSpec` contained in `StoragePoolSpec`.

* Update all storage drivers to stay up with the changes.

* Update the fake pool data declared in test package.

### Non-Goals

None

## Proposal

Description of the proposed solution.

### Data model impact

This proposal will update the definition of `StoragePoolSpec` with these changes below:

* Implement `DataStorageLoS`, `IOConnectivityLoS` and `DataProtectionLos` struct which defined in Swordfish:
```go
// DataStorageLoS can be used to describe a service option covering storage
// provisioning and availability.
type DataStorageLoS struct {
	// The enumeration literal specifies the time after a disaster that the
	// client shall regain conformant service level access to the primary
	// store, currently it only supports "OnlineActive","OnlinePassive",
	// "Nearline" and "Offline".
	// The expectation is that the services required to implement this
	// capability are part of the advertising system.
	RecoveryTimeObjective int64 `json:"recoveryTimeObjective,omitempty" yaml:"recoveryTimeObjective,omitempty"`

	// ProvisioningPolicy only supports "Fixed" and "Thin".
	ProvisioningPolicy string `json:"provisioningPolicy,omitempty" yaml:"provisioningPolicy,omitempty"`

	// IsSpaceEfficient indicates that the storage is compressed or deduplicated.
	// The default value for this prperty is false.
	IsSpaceEfficient bool `json:"isSpaceEfficient,omitempty" yaml:"isSpaceEfficient,omitempty"`
}

// IOConnectivityLoS can be used to specify the characteristics of storage
// connectivity.
type IOConnectivityLoS struct {
	// The Enumeration Literal shall specify the Access protocol for this
	// service option.
	AccessProtocol string `json:"accessProtocol,omitempty" yaml:"accessProtocol,omitempty"`

	// MaxIOPS shall be the maximum IOs per second that the connection shall
	// allow for the selected access protocol.
	// +units:[IO]/s
	MaxIOPS int64 `json:"maxIOPS,omitempty" yaml:"maxIOPS,omitempty"`
}

// DataProtectionLoS describes a replica that protects data from loss. The
// requirements must be met collectively by the communication path and the
// replica. Currently it's empty.
type DataProtectionLos struct{}
```

* Add `StoragePoolExtraSpec` struct definition:
```go
type StoragePoolExtraSpec struct {
	// DataStorage represents suggested some data storage capabilities.
	DataStorage DataStorageLoS `json:"dataStorage,omitempty" yaml:"dataStorage,omitempty"`
	// IOConnectivity represents some suggested IO connectivity capabilities.
	IOConnectivity IOConnectivityLoS `json:"ioConnectivity,omitempty" yaml:"ioConnectivity,omitempty"`
	// DataProtection represents some suggested data protection capabilities.
	DataProtection DataProtectionLos `json:"dataProtection,omitempty" yaml:"dataProtection,omitempty"`

	// Besides those basic suggested pool properties above, vendors can configure
	// some advanced features (diskType, IOPS, throughout, latency, etc)
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

Compared with before, there would be some performance loss when encoding and decoding the StoragePoolSpec. But we can improve the performance like this:
```go
type StoragePoolExtraSpec struct {
	// DataStorage represents suggested some data storage capabilities.
	DataStorage *DataStorageLoS `json:"dataStorage,omitempty" yaml:"dataStorage,omitempty"`
	// IOConnectivity represents some suggested IO connectivity capabilities.
	IOConnectivity *IOConnectivityLoS `json:"ioConnectivity,omitempty" yaml:"ioConnectivity,omitempty"`
	// DataProtection represents some suggested data protection capabilities.
	DataProtection *DataProtectionLos `json:"dataProtection,omitempty" yaml:"dataProtection,omitempty"`

	// Besides those basic suggested pool properties above, vendors can configure
	// some advanced features (diskType, IOPS, throughout, latency, etc)
	// themselves, all these properties can be exposed to controller scheduler
	// and filtered by selector in a extensible way.
	Advanced *map[string]interface{} `json:"advanced,omitempty" yaml:"advanced,omitempty"`
}
```

### Other deployer impact

If this prososal gets permitted, the backend configuration file (take ceph for example) should be updated like this:
```yaml
configFile: /etc/ceph/ceph.conf
pool:
  rbd:
    storageType: block
    availabilityZone: default
    extras:
      dataStorage:
        provisioningPolicy: Thin
        isSpaceEfficient: true
      ioConnectivity:
        accessProtocol: rbd
        maxIOPS: 1000
      advanced:
        diskType: SSD
        throughput: 1000
```

### Developer impact

Because this proposal has modified the `StoragePool` definition, all packages (db, api and so on) could be affected.

## Use Cases

* Multi-tenants resource provisioning
After the system initiated, the capabilities report thread will be triggered automatically and then capabilities properties of storage pool resource will be stored in database. After that, admin A creates a `ProvisionProfile` which can be viewed by all users, so user B can check all available profiles and create a volume.

## Implementation

* Update `StoragePool` schema (see `Data model` section) in model package.
* Modify `ListPools` method in all storage drivers. (Only several lines changed)
* Update some fake data defined in `test/collection/data.go`.
* Rename `ExtraSpec` (contained in StoragePoolSpec) to `StoragePoolExtraSpec`.

## Alternatives considered

None

## Open issues

None
