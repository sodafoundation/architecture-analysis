# Title

**Author(s)**: [Jack Li](https://github.com/jackhaibo)

## Summary

This design is proposed to enable multiattach of volumes.

## Motivation

Currently we refuse to attach a volume that is already attached (in-use). There are situations where a cloud user may have deployed a clustered file-system on a Cinder volume and they would like the ability to attach a volume to multiple hosts/servers at the same time.

### Goals

This spec proposes a set of changes to enable and control the use of multiattach volumes from the OpenSDS side.


## Design Details

1.The backend devices that support multi-attach ability need to report this in their capabilities in yaml configuration file.
2.Attach a volume as one would normally do (Read Write access) to a host and then attach it to another host in Read Only mode or Read Write Mode. In order to implement this, add an item AttachMode to the volume attachment which has two values: RO (Read Only) and RW (Read Write), default is RW. 
3.Add multiAttach bool to the volume model to enables/disables the ability of multi-attach. 
4.Add 'multiattach': '<is> True' to the profile and do some corresponding changes to the controller to choose backend
if user require a multi-attach volume.

### Data model impact

1.The dock loads configuration file that has the key value pair multiAttach:true/false and add 'multiattach': '<is> True' to Metadata.

Metadata: map[string]string{"multiattach": "<is> True"}

type DockSpec struct {
	*BaseModel
	Type string `json:"type,omitempty"`
	Name string `json:"name,omitempty"`
	Description string `json:"description,omitempty"`
	Status string `json:"status,omitempty"`
	StorageType string `json:"storageType,omitempty"`
	Endpoint string `json:"endpoint,omitempty"`
	NodeId string `json:"nodeId,omitempty"`
	DriverName string `json:"driverName,omitempty"`
	Metadata map[string]string `json:"metadata,omitempty"`	
}

2.The volume attachment struct adds AttachMode, read-only (‘ro’) or read-and-write (‘rw’), default is ‘rw’.

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

3.The volume struct adds multiAttach.

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
	MultiAttach bool `json:"multiAttach,omitempty"`
}

### REST API impact

N/A

### Security impact

N/A

### Other end user impact

The CLI need some changes that enable to create a multi-attach volume.

### Performance impact

N/A

### Other deployer impact

How does it affect the deployment? Are there config option changes?

### Developer impact

Drivers will need to add a capabilities field "multiattach: True/False", and do any special handling on their end for connecting/disconnecting volumes in this category.

## Use Cases

1.There are products that can be used like Oracle RAC that would make things like H/A databases via a shared Block device possible which is something of interest.
2.Passive stand-by servers/volumes.

## Implementation

Describe how the feature will be implemented. This can be a list of work items.
1.Update the dock to report capability of multi-attach.
2.Update the controller to select the multi-attach enabled backend at volume create time.
3.Update the CLI to enable create multi-attach volume.
4.Update attach/detach to be multiattach aware.
5.Update nbp to enable multiattach.

## Open issues

1.Should the capabilities field "multiattach: True/False" be added to dock spec or pool spec when dock reports multiAttach?

## References

https://github.com/openstack/cinder-specs/blob/master/specs/queens/enable-multiattach.rst#id2