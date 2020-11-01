# NetAPP ONTAP Storage Driver Support

**Authors:** [Himanshu](https://github.com/himanshuvar),
[Sanil](https://github.com/skdwriting)

## Summary
This is the design spec for the NetApp Storage Driver Support feature.

This proposal is drafted for adding NetAPP ONTAP storage driver as one of the supported backend storages for OpenSDS dock. It defines the interfaces for ONTAP storage driver.

## Motivation
As a storage provisioner, OpenSDS provides various storage vendors support through wide range of backend drivers. OpenSDS Hotpot which is the software defined storage controller provides pluggable framework for different storage systems. To extend this capability of OpenSDS Hotpot  provides the ability to support one of popular Storage Vendor NetAPP, create storage volumes etc. for a wide range of North Bound platforms like Kubernetes, VMware, OpenStack etc.

ONTAP is NetApp’s multiprotocol, unified storage operating system. ONTAP systems have all-flash, hybrid, or all-HDD configurations and offer many different deployment models includes FAS, AFF, ONTAP Select, and Cloud Volumes ONTAP. Through this proposal, plan is to support above NetApp ONTAP storage deployments.

## Proposal

### Goal

* Add support for OpenSDS Southbound NetApp ONTAP driver.
* Installation using Ansible needs to be modified so that new driver is configured.


## Details

### NetApp ONTAP drivers

* constant definition, like other storage drivers, in contrib/drivers/netapp/ontap/ontap.go
```go
const (
	opensdsPrefix   = "opensds-"
	sizeShiftBit    = 30
	defaultConfPath = "/etc/opensds/driver/ontap.yaml"
	defaultAZ       = "default"
)
```
* NetApp ONTAP driver needs to implement the following VolumeDriver Interface supported by OpenSDS Hotpot.
```go
type VolumeDriver interface {
	Setup() error
	Unset() error
	CreateVolume(opt *pb.CreateVolumeOpts) (*model.VolumeSpec, error)
	PullVolume(volIdentifier string) (*model.VolumeSpec, error)
	DeleteVolume(opt *pb.DeleteVolumeOpts) error
	ExtendVolume(opt *pb.ExtendVolumeOpts) (*model.VolumeSpec, error)
	InitializeConnection(opt *pb.CreateVolumeAttachmentOpts) (*model.ConnectionInfo, error)
	TerminateConnection(opt *pb.DeleteVolumeAttachmentOpts) error
	CreateSnapshot(opt *pb.CreateVolumeSnapshotOpts) (*model.VolumeSnapshotSpec, error)
	PullSnapshot(snapIdentifier string) (*model.VolumeSnapshotSpec, error)
	DeleteSnapshot(opt *pb.DeleteVolumeSnapshotOpts) error
	InitializeSnapshotConnection(opt *pb.CreateSnapshotAttachmentOpts) (*model.ConnectionInfo, error)
	TerminateSnapshotConnection(opt *pb.DeleteSnapshotAttachmentOpts) error
	CreateVolumeGroup(opt *pb.CreateVolumeGroupOpts) (*model.VolumeGroupSpec, error)
	UpdateVolumeGroup(opt *pb.UpdateVolumeGroupOpts) (*model.VolumeGroupSpec, error)
	DeleteVolumeGroup(opt *pb.DeleteVolumeGroupOpts) error
	ListPools() ([]*model.StoragePoolSpec, error)
}
```
