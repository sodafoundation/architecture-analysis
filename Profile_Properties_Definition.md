# Profile Properties Definition

**Status**: Completed

**Version**: Alpha

**Author(s)**: Leon Wang (@leonwanghui)

## Summary

This proposal is designed for standardizing the properties definition of OpenSDS
profile. This design is also part of referenced implementation of LineOfService
defined in [Swordfish](https://www.snia.org/sites/default/files/SMI/swordfish/v106/Swordfish_v1.0.6_Specification.pdf)
specification (v1.0.6), which promises to provide a unified approach for the
management of storage and servers in hyperscale and cloud infrastructure
environments.

## Motivation

The profile defined in OpenSDS represents a service level, such as gold, silver
and bronze. The controller chooses an appropriate dock service for the profile
to handle the request when user creates a volume or group. This design proposal
aims to define the standard capability rules for the profile to ensure that all
the storage system can abstract out virtual pools in accordance with the
standard rules.

## Proposal

### Goals

* Redesign the definition of `ExtraSpec` contained in `ProfileSpec`.

* Update the selector filter to map profile properties with pool capabilities.

* Update some operations about profile extras in OpenSDS Northbound API.

### Data model impact

This proposal will update the definition of `ProfileSpec` with these changes below:

* ProvisioningPropertiesSpec
```go
type ProvisioningPropertiesSpec struct {
	// DataStorage represents some suggested data storage capabilities.
	DataStorage DataStorageLoS `json:"dataStorage,omitempty"`
	// IOConnectivity represents some suggested IO connectivity capabilities.
	IOConnectivity IOConnectivityLoS `json:"ioConnectivity,omitempty"`
}
```

* ReplicationPropertiesSpec
```go
type ReplicationPropertiesSpec struct {
	// DataProtection represents some suggested data protection capabilities.
	DataProtection DataProtectionLoS `json:"dataProrection,omitempty"`
	// ReplicaInfos represents some suggested data replicaiton information.
	ReplicaInfos struct {
		// The enumeration literal specifies whether the target elements will be
		// updated synchronously or asynchronously. The possible values of this
		// property could be:
		// * Active: Active-Active (i.e. bidirectional) synchronous updates.
		// * Adaptive: Allows implementation to switch between synchronous
		//   and asynchronous modes.
		// * Asynchronous: Asynchronous updates.
		// * Synchronous: Synchronous updates.
		ReplicaUpdateMode string `json:"replicaUpdateMode,omitempty"`
		// ConsistencyEnabled indicates that the source and target shall be
		// consistent. The default value is false.
		ConsistencyEnabled bool `json:"consistencyEnabled,omitempty"`
		// ReplicationPeriod shall be an ISO 8601 duration that defines the
		// duration of performing replication operation. For example,
		// "P3Y6M4DT12H30M5S" represents a duration of "3 years, 6 months,
		// 4 days, 12 hours, 30 minutes and 5 seconds".
		ReplicationPeriod string `json:"replicationPeriod,omitempty"`
		// ReplicationBandwidth specifies the maximum bandwidth for performing
		// replication operation.
		// +units:Mb/s
		ReplicationBandwidth int64 `json:"replicationBandwidth,omitempty"`
	} `json:"replicaInfos,omitempty"`
}
```

* SnapshotPropertiesSpec
```go
type SnapshotPropertiesSpec struct {
	// The property defines how to take snapshots.
	Schedule struct {
		// This vaule is represented as a string in ISO 8601 datetime format.
		// ISO 8601 sets out an internationally agreed way to represent dates:
		// yyyy-mm-ddThh:mm:ss.ffffff. For example, "3:53pm, September 15, 2008"
		// is represented as "2008-09-15T15:53:00".
		Datetime string `json:"datetime,omitempty"`
		// The value specifies the duration of executing a operation, which
		// contains three options including Daily, Weekly and Monthly.
		Occurence string `json:"occurence,omitempty"`
	} `json:"schedule,omitempty"`
	Retention struct {
		// The value specifies the number of recently created snapshots for
		// retention.
		// +optional
		Number int64 `json:"number,omitempty"`
		// The value specifies the duration of snapshots for retention.
		// +optional
		// +units:day
		Duration int64 `json:"duration,omitempty"`
	} `json:"retention,omitempty"`
}
```

* DataProtectionPropertiesSpec
```go
type DataProtectionPropertiesSpec struct {
	// DataProtection represents some suggested data protection capabilities.
	DataProtection DataProtectionLoS `json:"dataProtection,omitempty"`
	// ConsistencyEnabled indicates that the source and target shall be
	// consistent. The default value is false.
	ConsistencyEnabled bool `json:"consistencyEnabled,omitempty"`
}
```

* CustomPropertiesSpec
```go
// CustomPropertiesSpec is a dictionary object that contains unique keys and
// JSON objects.
type CustomPropertiesSpec map[string]interface{}
```

* ProfileSpec
```go
// An OpenSDS profile is identified by a unique name and ID. With adding
// profile properties, each profilecontains a set of tags of storage
// capabilities which are desirable features for a class of applications.
type ProfileSpec struct {
	*BaseModel
	// The uuid of project
	// + readOnly
	TenantId string `json:"tenantId"`

	// The name of the profile.
	Name string `json:"name,omitempty"`

	// The description of the profile.
	// +optional
	Description string `json:"description,omitempty"`

	// The storage type of the profile.
	// One of: block, file or object.
	StorageType string `json:"storageType,omitempty"`

	// ProvisioningProperties represents some suggested properties for performing
	// provisioning policies.
	// +optional
	ProvisioningProperties ProvisioningPropertiesSpec `json:"provisioningProperties,omitempty"`

	// ReplicationProperties represents some suggested properties for performing
	// replicaiton policies.
	// +optional
	ReplicationProperties ReplicationPropertiesSpec `json:"replicationProperties,omitempty"`

	// SnapshotProperties represents some suggested properties for performing
	// snapshot policies.
	// +optional
	SnapshotProperties SnapshotPropertiesSpec `json:"snapshotProperties,omitempty"`

	// DataProtectionProperties represents some suggested properties for
	// performing data protection policies.
	// +optional
	DataProtectionProperties DataProtectionPropertiesSpec `json:"dataProtectionProperties,omitempty"`

	// CustomProperties is a map of keys and JSON object that represents the
	// customized properties of profile, such as requested capabilities
	// including diskType, latency, deduplicaiton, compression and so forth.
	// +optional
	CustomProperties CustomPropertiesSpec `json:"customProperties,omitempty"`
}
```

### REST API impact

This proposal would have imapact on operations about profile extras below:

| Method | Uri | Description |
| --- | --- | --- |
| POST | /:tenantId/profiles/:profileId/extras | Add an extra property to profile |
| GET | /:tenantId/profiles/:profileId/extras | List all properties in profile |
| DELETE | /:tenantId/profiles/:profileId/extras/:extraKey | Delete specified property from profile |

If the proposal get approved, those methods would be replaced with below:

| Method | Uri | Description |
| --- | --- | --- |
| POST | /:tenantId/profiles/:profileId/customProperties | Add key-value customized properties to profile |
| GET | /:tenantId/profiles/:profileId/customProperties | List all customized properties in profile |
| PUT | /:tenantId/profiles/:profileId/customProperties/:customKey | Update specified customized property in profile |
| DELETE | /:tenantId/profiles/:profileId/customProperties/:customKey | Remove specified customized property from profile |

### Security impact

None

### Other end user impact

There would be some changes in client package that all operations related to
profile extras should be updated with REST API. See detailed changes in
[REST API](### REST API impact) section.

### Performance impact

None

### Other deployer impact

There is no impact on the deployment, but to reduce the complexity of configuring
profile, we would provide some sample profiles in different scenarios (such as
Data Provisioning, Data Replication and so forth).

### Developer impact

None

## Use Cases

* Data provisioning

* Data replication

* Data protection for persistent volumes using snapshot



## Implementation

* Update `ProfileSpec` schema (see [Data model](### Data model impact) section)
in model package.
* Improve the selector filter to map new ProfileSpec to StoragePoolSpec.
* Update REST API methods related to profile extras
(see [REST API](### REST API impact) section) in api package.

## Alternatives considered

None

## Open issues

More scenarios should be presented in the long term, including data migration,
data lifecycle, etc.
