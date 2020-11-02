# NetApp NAS driver

## Summary
### Overview of Data ONTAP
ONTAP is the Data Management software which run on NetApp FAS or AFF appliances, ONTAP select commodity hardware or in Private, Public or Hybrid clouds (NetApp Private Storage or Cloud Volumes ONTAP).
It  offers unified storage for applications that read and write data over block or file access protocols, in storage configurations that range from high-speed flash, to lower-priced spinning media, to cloud-based object storage.
Data ONTAP enables you to combine multiple physical storage controllers into a single logical cluster that can non-disruptively service multiple storage workload need
More on Data ONTAP at [https://docs.netapp.com/ontap-9/index.jsp](https://docs.netapp.com/ontap-9/index.jsp)
[https://hol.netapp.com/labguides/SL10280.pdf](https://hol.netapp.com/labguides/SL10280.pdf)

### Overview of SODA Dock
SODA Dock provides the platform for different storage drivers to connect to it and provide an unified and semaless access to the underlying storages. Any compute on the North can seamlessly access the underlying storage, using SODA API, through a single south-bound interface called SODA Dock. These drivers can be Block storage drivers or File storage drivers from different Vendors like EMC, NetApp, IBM, Huawei, HPE, Chubaofs, Manila, Native LVM etc

SODA Dock already supports NetApp ONTAP SAN Driver for Block devices. This design spec is to support NetApp NAS Fileshare driver in SODA. The idea is to support Fileshare management for NetApp NAS devices.

## Use case
* As a Server admin, I want to use the NFS client of ESXi to mount a designated NFS volume from a NetApp NAS Server, create a NFS Datastores and use to store and boot virtual machines in the same way that one uses for VMFS Datastores. The NFS volumes on the NAS server are created by the Storage administrators and exported from the NFS server of the NetApp filer.

* As a storage admin, I want to get a unified storage management platform from where I can manage different Storage arrays in the datacenter. These array can be pure SAN device, NAS device or a Unified storage device. 

* As a storage admin, I should be able to create a NetApp NAS volume for File storage, mount it, apply appropriate export policies and share the export location. This export location can be used by the server admins to mount it on the host through the Client and use it as File storage

*  As a service provider (SSP), I want to manage the storage provisioning from a NetApp Filer for different clients. Each client physical storage is segregated by the Virtual Storage Container called SVM. These SVMs have separate access credentials and endpoints defined. As a SSP, I should be able to manage different clients from a single pane of SODA Dock. Also these SVMs can have their own QoS defined.

## Requirement
* SODA Dock should support NetApp Fileshare driver to support
    * Creation of Fileshare on a given Aggregate
    * Deletion of Filesahre on a given Aggregate
    * Taking snapshot of the Fileshare
    * Delete Snapshot of the Fileshare
* The Fileshare created should have a defined mountpoint for the Hosts to mount and use it 
* Support creation of Fileshare on FlexVolume of an Aggregate. As the Fileshares are created on a FlexVol of the NetApp, it gives flexibility to increase or decrease the size of Fileshare, transparent to the end user

## Goals
* Add support for NetApp ONTAP NAS Driver with required fileshare features
* Add installation of NetApp NAS driver through Ansible
* NetApp ONTAP supports multiple data access protocols like CIFS and NFS for creating export/shares which can be mounted/mapped to be used as File storage

## Non Goals
* No Qtree support for G Release
* Currently SODA will support only NFS protocol
* This design spec is only for the SODA dock to support NetApp Fileshare. Integrating these Fileshares with Northbound compute platforms like expanding SODA NGC plugin to support NFS datastore too, is not the scope of this proposal
* multi-tenant deployment and registration of SVM per tenant

## Interface Model
* Provide the Fileshare operations and Management through any Client like SODA Dashboard or SODA CLI 'osdsctl'
* SODA API interface for Fileshare opertions and Management
  SODA Dock support for NetApp Fileshare will support all File Share APIs. For more Info, check [https://raw.githubusercontent.com/sodafoundation/api/master/openapi-spec/swagger.yaml](https://raw.githubusercontent.com/sodafoundation/api/master/openapi-spec/swagger.yaml)
## Feature Details
SODA Dock works off the "Storage Pool" of the storage provider. These pools are used to create the logical storages for provisioning. For NetApp, *Aggregates* are the storage providers. 

### Storage Backend
Before going into the details, let's understand few NetApp ONTAP details which  forms the building block of SODA NetApp NAS Fileshare driver support.
#### Storage Virtual Machine (SVM)
NetApp clusters are the consolidation of various storage workloads. Each workload has an assigned SVM, which is a Virtual Storage Controller. It is useful for storage tiering, data segregation and data access control. Every SVM can have a defined Management LIF (Logical Inteface) and/or  Data LIFs

#### Aggregates
Aggregates are container for the disks managed by the Node. Every aggregate has defined RAID

#### FlexVol Volumes
ONTAP serves data to clients and hosts from logical containers called FlexVol volumes. Because these volumes are only loosely coupled with their containing aggregate, they offer greater flexibility in managing data.
Volumes contain file systems in a NAS environment and LUNs in a SAN environment

#### Qtrees
qtrees are used to partition a FlexVol volume into more manageable units, and quotas to limit volume resource usage

For using NetApp NAS Volumes as fileshare in SODA, few configurations are required to be done from the Storage Backend
1) Create atleast one *Aggregate* and assign it to the **SVM** which is to be used in SODA as the NetApp storage endpoint
2) Enable NFS/CIFS protocol on the SVM
3) The default export policy of the SVM root volume must include a rule to allow all clients open access through NFS
4) Create NFS/CIFS server on the SVM
5) Create a LIF
For more info, check the NFS and CIFS/SMB mangement section in [https://docs.netapp.com/ontap-9/index.jsp](https://docs.netapp.com/ontap-9/index.jsp)

These configurations are required to be provided in the "NetApp NAS Fileshare driver" config file in SODA.
Ex: (netapp_ontap_nas.yaml)
```
backendOptions:
  version: 1
  username: "admin"
  password: "password"
  storageDriverName: "ontap-nas" # This is name of the SODA Netapp NAS driver
  managementLIF: "192.168.1.2"   # Filer Node Management LIF
  dataLIF: "192.168.1.3"         # Filer SVM Data LIF
  svm: "svm0"                    # SVM of the Filer
pool:
  aggr0:                         # Aggregate in the filer which associated with above SVM
    storageType: file
    availabilityZone: default
    multiAttach: true
    extras:
      dataStorage:
        provisioningPolicy: Thin
        compression: false
        deduplication: false
        storageAccessCapability:
          - Read
          - Write
          - Execute
      ioConnectivity:
        accessProtocol: nfs
        maxIOPS: 7000000
        maxBWS: 600
        minIOPS: 1000000
        minBWS: 100
        latency: 100
      advanced:
        diskType: SSD
        latency: 5ms
```
* When SODA dock process starts it reads this config file and registers the Storage Driver (**ontap_nas**)
* The driver then connects to the LIF on the NetApp Filer SVM and discovers the pool details, i.e. the Aggregate (provided in the config file)
* This discovered aggregate/pool form the base for storage provisioning through SODA from the NetApp filer

![NetApp FileShare Driver Flow](resources/NetAppNAS_SODA_Flow.png)

### Implementation details
* SODA uses NetApp provided Trident Storage Driver to do backend operations
* SODA NetApp NAS Fileshare driver will implement following interfaces
 ```
 type FileShareDriver interface {
    //Any initialization the fileshare driver does while starting.
    Setup() error
    //Any operation the fileshare driver does while stopping.
    Unset() error

    CreateFileShare(opt *pb.CreateFileShareOpts) (*model.FileShareSpec, error)

    DeleteFileShare(opts *pb.DeleteFileShareOpts) error

    CreateFileShareSnapshot(opts *pb.CreateFileShareSnapshotOpts) (*model.FileShareSnapshotSpec, error)

    DeleteFileShareSnapshot(opts *pb.DeleteFileShareSnapshotOpts) error

    CreateFileShareAcl(opt *pb.CreateFileShareAclOpts) (*model.FileShareAclSpec, error)

    DeleteFileShareAcl(opt *pb.DeleteFileShareAclOpts) error

    ListPools() ([]*model.StoragePoolSpec, error)
 }
```

* Register new NetApp NAS driver type
```
    case config.NetappOntapNasDriverType:
        f = &netapp.NASDriver{}
```
 
* Define the following model for NetApp NAS driver
```
 package netapp
 import (
    "github.com/netapp/trident/storage_drivers/ontap"
    . "github.com/sodafoundation/dock/contrib/drivers/utils/config"
 )

 type BackendOptions struct {
    Version           int    `yaml:"version"`
    StorageDriverName string `yaml:"storageDriverName"`
    ManagementLIF     string `yaml:"managementLIF"`
    DataLIF           string `yaml:"dataLIF"`
    Svm               string `yaml:"svm"`
    Username          string `yaml:"username"`
    Password          string `yaml:"password"`
 }

 type ONTAPConfig struct {
    BackendOptions `yaml:"backendOptions"`
    Pool           map[string]PoolProperties `yaml:"pool,flow"`
 }

 type NASDriver struct {
    nasStorageDriver *ontap.NASStorageDriver
    conf             *ONTAPConfig
 }
```

* SODA NetApp NAS Fileshare driver creates the volume in NAS environment and creates the mount path or Juntion path on the backend storage
* It also applies the SVM export policies to the Volume created
* Once the Volume is created and mounted on the SVM, these File storage can be used by clients. Clients can simply mount or create shares to use it as Fileshare on the respective servers/host
* SODA NetApp Fileshare driver facilititates to:
    * Create Fileshares (which eventually operates to create a volume and mount it on the NetApp storage backend)
    * Applies export policies to the volume created on the backend
    * These Fileshares and the detail metadata are stored in SODA etcd
    * Create Fileshare in SODA  expects the required policies and AZ are already created
    * The APIs create an Unique ID for every Fileshare created with name and size
    * It also, facilitates to delete a Fileshare. This will delete the Fileshare from SODA etcd DB as well as unmount and delete it from the NetApp storage backend
    * Similar operations for Fileshare snapshot creation

* Sample code to create fileshare
```
func (d *NASDriver) CreateFileShare(opt *pb.CreateFileShareOpts) (vol *model.FileShareSpec, err error) {
    var name = opt.GetName()
    volConfig := d.GetVolumeConfig(name, opt.GetSize())
    storagePool := &storage.Pool{
        Name:               opt.GetPoolName(),
        StorageClasses:     make([]string, 0),
        Attributes:         make(map[string]sa.Offer),
        InternalAttributes: make(map[string]string),
    }

    // Here the storage pool is the aggregate on the Netapp Filer
    // This Create call will create a volume and mount to be used as
    // the export/share
    err = d.nasStorageDriver.Create(volConfig, storagePool, make(map[string]sa.Request))
    if err != nil {
        log.Errorf("create nas fileshare (%s) failed: %v", opt.GetId(), err)
        return nil, err
    }

    return &model.FileShareSpec{
        BaseModel: &model.BaseModel{
            Id: opt.GetId(),
        },
        Name:             opt.GetName(),
        Size:             opt.GetSize(),
        Description:      opt.GetDescription(),
        Protocols:        []string{NFSProtocol},
        AvailabilityZone: opt.GetAvailabilityZone(),
        PoolId:           opt.GetPoolId(),
        Metadata: map[string]string{
            "nfsFileshareName": name,
        },
    }, nil
}

``` 

### Deployment
SODA Installer ansible script should provide provision to enable netapp nas backend
```
enabled_backends: lvm,nfs,netapp_ontap_nas #For Multi-backend add backends here, for eg. enabled_backends: lvm,ceph,cinder,nfs
```
The NetApp NAS config Yaml file should be created which will be used for the new driver registration
