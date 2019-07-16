# Multi_Attach_Design

**Author(s)**: [Jack Li](https://github.com/jackhaibo)

## Summary

This design is proposed to enable MultiAttach of volumes.

## Motivation

Currently a request to attach a volume that is already attached (in-use) will be rejected. There are situations where a cloud user may have deployed a clustered file-system and they would like the ability to attach a volume to multiple hosts/servers at the same time.

### Goals

This spec proposes a set of changes to enable and control the use of MultiAttach volumes from the OpenSDS side.

## Design Details

1. The drivers that support multi-attach ability need to report this in their capabilities through pool configuration file. The multi-attach volume creation request will select multi-attach enabled pool from pools which come from different drivers and many of them may not support this ability. 
2. Attach a volume as one would normally do (Read Write access) to a host and then attach it to another host in Read Only mode or Read Write Mode. In order to implement this, add an item AttachMode to the volume attachment which has two values: RO (Read Only) and RW (Read Write), default is RW. 
3. Add multiAttach bool to the volume model to enable/disable the ability of multi-attach. 
4. Add 'capabilities:multiAttach': '<is> True' to the profile and do some corresponding changes to the controller to choose driver if user requires a multi-attach volume.

### Data model impact

1. Add MultiAttach field in pool spec

```go
type StoragePoolSpec struct {
	*BaseModel
	Name string `json:"name,omitempty"`
	Description string `json:"description,omitempty"`
	Status string `json:"status,omitempty"`
	DockId string `json:"dockId,omitempty"`
	AvailabilityZone string `json:"availabilityZone,omitempty"`
	TotalCapacity int64 `json:"totalCapacity,omitempty"`
	FreeCapacity int64 `json:"freeCapacity,omitempty"`
	StorageType string `json:"storageType,omitempty"`
	Extras StoragePoolExtraSpec `json:"extras,omitempty"`
	ReplicationType string `json:"replicationType,omitempty"`
	ReplicationDriverName string `json:"replicationDriverName,omitempty"`

	// MultiAttach
	// If true, this volume can attach to more than one instance. Default will be multiAttach:False
	MultiAttach bool `json:"multiAttach,omitempty"`
}
```


2. The volume attachment struct adds AttachMode, read-only (‘ro’) or read-and-write (‘rw’), default is ‘rw’.

```go
type VolumeAttachmentSpec struct {
	*BaseModel
	TenantId string `json:"tenantId,omitempty"`
	UserId string `json:"userId,omitempty"`
	VolumeId string `json:"volumeId,omitempty"`
	Mountpoint string `json:"mountpoint,omitempty"`
	Status string `json:"status,omitempty"`
	Metadata map[string]string `json:"metadata,omitempty"`
	HostInfo `json:"hostInfo,omitempty"`
	ConnectionInfo `json:"connectionInfo,omitempty"`
	AccessProtocol string `json:"accessProtocol,omitempty"`
	AttachMode string `json:"attachMode,omitempty"`
}
```

3. The volume struct adds MultiAttach.

```go
type VolumeSpec struct {
	*BaseModel
	TenantId string `json:"tenantId,omitempty"`
	UserId string `json:"userId,omitempty"`
	Name string `json:"name,omitempty"`
	Description string `json:"description,omitempty"`
	GroupId string `json:"groupId,omitempty"`
	Size int64 `json:"size,omitempty"`
	AvailabilityZone string `json:"availabilityZone,omitempty"`
	Status string `json:"status,omitempty"`
	PoolId string `json:"poolId,omitempty"`
	ProfileId string `json:"profileId,omitempty"`
	Metadata map[string]string `json:"metadata,omitempty"`
	SnapshotId string `json:"snapshotId,omitempty"`
	SnapshotFromCloud bool `json:"snapshotFromCloud,omitempty"`
	ReplicationId string `json:"replicationId,omitempty"`
	ReplicationDriverData map[string]string `json:"replicationDriverData,omitempty"`
	AttachStatus string
	MultiAttach bool `json:"multiAttach"`
}
```

### REST API impact

N/A

### Security impact

N/A

### Other end user impact

User should specify whether the pool supports MultiAttach in the configuration file use the following command to create a multi-attach related profile and specify it in volume creation.

```
osdsctl profile create '{"name": "multi-attach", "description": "multi attach profile","customProperties":{"capabilities:multiAttach":"<is> true"}}'
```

### Other deployer impact

N/A

### Developer impact

Drivers will need to add a capabilities field "multiAttach: True/False" to their pool configuration files, and do any special handling on their end for connecting/disconnecting volumes in this category.

## Use Cases

1. There are products such as Oracle RAC that can leverage this feature. Oracle RAC uses H/A database via a shared block device.
2. Passive stand-by servers/volumes.
3. Multi-attach may be required active/active or active/passive scenario

## Implementation

1. Update the configuration files of pools belong to a driver that support multi-attach to report this capability.
2. Update the state machine to accommodate multi-attach.
2. Update the controller to select a multi-attach enabled pool based on profile at volume create time.
3. Update attach/detach to be multiAttach aware.
4. Update nbp to enable multiAttach.

## References

https://github.com/openstack/cinder-specs/blob/master/specs/queens/enable-multiattach.rst#id2

## Driver configuration yaml file example for Multi-Attach

```yaml
authOptions:
  username: "admin"
  password: "IaaS@PORTAL-CLOUD9!"
  # Whether to encrypt the password. If enabled, the value of the password must be ciphertext.
  EnableEncrypted: false
  # Encryption and decryption tool. Default value is aes. The decryption tool can only decrypt the corresponding ciphertext.
  PwdEncrypter: "aes"
  url: "https://0.0.0.0:8088"

  fmIp: 0.0.0.0
  fsaIp:
    - 0.0.0.1
    - 0.0.0.2
    - 0.0.0.3

pool:
  0:
    storageType: block
    availabilityZone: default
    multiAttach: true
    extras:
      dataStorage:
        provisioningPolicy: Thin
        isSpaceEfficient: false
      ioConnectivity:
        accessProtocol: iscsi
        maxIOPS: 7000000
        maxBWS: 600
      advanced:
        diskType: SSD
        latency: 3ms
  1:
    storageType: block
    availabilityZone: default
    multiAttach: true
    extras:
      dataStorage:
        provisioningPolicy: Thin
        isSpaceEfficient: false
      ioConnectivity:
        accessProtocol: iscsi
        maxIOPS: 3000000
        maxBWS: 300
      advanced:
        diskType: SSD
        latency: 500ms
```

## Nbp changes
In nbp, whether the volume can be attached to more than once will be determined: if the volume capability has MULTI_NODE mode, but the volume does enable MultiAttach, multiAttach will fail.

