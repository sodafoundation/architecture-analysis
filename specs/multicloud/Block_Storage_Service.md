# Block Device Management in Multi-Cloud

## Goals

Block device management for multi-cloud. Cloud Block Device support for:
1.  AWS EBS
2.  Google Cloud Storage

-   This proposal addresses only the creation/deletion/update of Cloud block devices
-   This proposal addresses the Snapshot creation and Lifecycle management only within the Cloud
   
## Motivation and background

SODA Foundation Gelato project provides policy based data mobility across public, private and Hybrid clouds. This mobility is currently limited to Object Storage only.

The proposal here is to broaden the scope to Block Storage also. Different Cloud vendors provide block storage services. Block storage is important for the applications of instances. Some of the Block storage being available to the instances over network i.e. Network attached, makes the storage accessible across the instances.

These block devices provide services which fits into the services multicloud provides.
1.  Availability: The block device/volume snapshots provide the backup
2.  Security: The Block device/volume can be encrypted for security. Similar to the encryption provided for Objects/Blob storage
3.  Data Lifecycle Management: Block device Snapshots management for creation and deletion on custom schedule
4.  Data Migration and availability: Block volume snapshots are available across the AZs of a region. Point-in-time snapshots can be migrated across instances in the AZs.

The basic idea behind adding block support is to create a Distributed Data Store(DDS) going ahead. Distributed across on-premise and cloud.
This block device support in multi-cloud is a step forward to achieve the DDS.

## Non-Goals
1.  Host Management for the Volumes are not part of SODA Faroe release
2.  With Faroe release, only Create and List for AWS EBS will be available as experimental feature

### Requirement Analysis
1.  Block Storage support for multi cloud
2.  Snapshot management for multi cloud

### Input Requirements
High level requirement is to add Block and File storage for Cloud Data Management. Already Object storage is supported for in multi-cloud for Data Migration and Lifecycle policies

## Feature Requirements
Add Block Storage Management in SODA multi-cloud/Gelato

### Requirement Analysis
Block Device Management: Though Object storage solves the provisioning management issues presented by the storage expansion and data growth needs as well as the need for the resiliency, Block storage still holds its own place because of multiple reasons. Like:
1.  Ability to incrementally edit one part of the file/block
2.  Direct OS access to the mounted storage
3.  Flexibility and usability provided by the FS on top of the block storage
4.  Object storage is good for unstructured data but for structured data storage and access one need the block storage
 
Gelato/SODA multi-cloud project should provide the flexibility and unified ease of use for managing the Block devices. This include:
1.  Block Device creation/deletion/update
2.  Block Device attach/detach to the compute
3.  Snapshots of the block device for Lifecycle and data migration

### Functional Requirements
1.  SODA multi-cloud project should be able to Manage Cloud Volumes like AWS EBS, Google Cloud Persistent Storage
2.  These Block devices can be managed by the Multi-Cloud APIs
3.  Management of block device includes, Creation, Deletion, Updation, Attach and Detach
4.  Services are provided for different Cloud Providers like AWS,GCP, IBM etc
5.  Use the Cloud Credentials in a multi-tenant environment for providing services
 

### Non Functional Requirements
1.  Include the Vendor libs for Block Device management
2.  SODA multi-cloud installation handles any specific library installation/uninstallation requirements for Block Device support

## Architecture Analysis
Here is the High level Architecture for multi cloud Block storage support
![Arch BlockService Multicloud](resources/MulticloudBlockArch.png) 

### Module Architecture
![Module Arch FileShare](resources/MulticloudBlockService.png)

### High Level Module architecture
1.  Client calls multi-cloud API for Block Device
2.  Call is handled by API Handler
3.  Authenticates the API call
4.  Send the request to the new service called “Block Service”
5.  Block service updates the metadata and call the Backend adapter
6.  Backend adapter based upon the request connects to the required/specific Cloud backend and serves the request back
7.  Any required data received from Backend is updated in DB
 

### Development and Deployment Context

Sample code for AWS EBS services
[https://github.com/kumarashit/cloud_test/blob/master/aws/aws_connect.go](https://github.com/kumarashit/cloud_test/blob/master/aws/aws_connect.go)
  

## Use case View
1.  Replicate Cloud Volume Using DRBD and Three site replication
2.  User wants on-demand block device availability for the Cloud instances it is using
3.  User wants the backup of the Block device it uses. This can be achieved by taking on-demand or scheduled snapshots/backup.
4.  K8s pods can leverage the  awsElasticBlockStore  to mount a AWS EBS volume into the pods. These volume contents are preserved when the pods are removed and it’s merely the action of unmounting the volume from pods. This behavior is unlike to the emptyDir where the contents are removed when the pod is removed
5.  Lifecycle policy for Block Device snapshot transition or deletion
6.  Cloud Volume as iSCSI to the other site using VPN
7.  On-premise storage like HPE Cloud Volume or ONTAP Cloud Volume for cloning and sharing across Clouds
8.  Block Volume snapshot migration across the Availability Zones in a region make it available for instances across AZs
9.  Cost efficiency: Use on-premise native storage hardware for backup. Move Cloud Volume snapshots to on-premise backup


## Interface Model

Block Device Service is available through any client which can use the Cloud Block Device SODA API
1.  SODA Dashboard
2.  SODA REST APIs

### APIs
Get volumes for a backend:
``` 
[GET] http://127.0.0.1:8089/v1/94b280022d0c4401bcf3b0ea85870519/volumes?backendId=5ef26d79c5d1b20001308f66
{
 "volumes": [
  {
   "size": 8589934592,
   "type": "gp2",
   "state": "in-use",
   "volumeId": "vol-0b1191d991f2bac39",
   "availabilityZone": "ap-south-1b",
   "createdAt": "2020-06-07 18:55:24.995 +0000 UTC"
  },
  {
   "size": 1073741824,
   "type": "gp2",
   "state": "available",
   "volumeId": "vol-08645c4985913749b",
   "availabilityZone": "ap-south-1a",
   "createdAt": "2020-06-23 15:17:54.645 +0000 UTC"
  },
  {
   "name": "aksvol1",
   "size": 1073741824,
   "type": "gp2",
   "state": "available",
   "volumeId": "vol-0526b378cf6dce574",
   "availabilityZone": "ap-south-1a",
   "createdAt": "2020-06-23 17:15:11.876 +0000 UTC"
  },
  {
   "name": "aksvol2",
   "size": 1073741824,
   "type": "gp2",
   "state": "available",
   "volumeId": "vol-05d10356c6e008090",
   "availabilityZone": "ap-south-1a",
   "createdAt": "2020-06-23 18:43:08.308 +0000 UTC"
  },
  {
   "name": "aksvol3",
   "size": 1073741824,
   "type": "gp2",
   "state": "available",
   "volumeId": "vol-0d6e695a04a988711",
   "availabilityZone": "ap-south-1a",
   "createdAt": "2020-06-23 18:53:07.982 +0000 UTC"
  },
  {
   "name": "aksvol4",
   "size": 1073741824,
   "type": "gp2",
   "state": "available",
   "volumeId": "vol-053075656150acc49",
   "availabilityZone": "ap-south-1a",
   "createdAt": "2020-06-23 18:54:04.868 +0000 UTC"
  },
  {
   "name": "aksvol5",
   "size": 536870912000,
   "type": "sc1",
   "state": "available",
   "volumeId": "vol-0ca0ec01c9df43d6a",
   "availabilityZone": "ap-south-1a",
   "createdAt": "2020-06-23 19:16:36.395 +0000 UTC"
  },
  {
   "name": "aksvol6",
   "size": 536870912000,
   "type": "sc1",
   "state": "available",
   "volumeId": "vol-0c23d3de79169dfdd",
   "availabilityZone": "ap-south-1a",
   "createdAt": "2020-06-23 20:33:53.237 +0000 UTC"
  },
  {
   "name": "aksvol6",
   "size": 536870912000,
   "type": "sc1",
   "state": "available",
   "volumeId": "vol-0138920bf8f577701",
   "availabilityZone": "ap-south-1a",
   "createdAt": "2020-06-23 20:33:52.848 +0000 UTC"
  },
  {
   "name": "aksvol7",
   "size": 536870912000,
   "type": "sc1",
   "state": "available",
   "volumeId": "vol-0f032816fad07911d",
   "availabilityZone": "ap-south-1a",
   "createdAt": "2020-06-23 20:55:25.905 +0000 UTC"
  },
  {
   "name": "aksvol8",
   "size": 5368709120,
   "type": "io1",
   "state": "available",
   "volumeId": "vol-0f2a279b5f863f07e",
   "availabilityZone": "ap-south-1a",
   "createdAt": "2020-06-23 21:00:53.662 +0000 UTC"
  },
  {
   "name": "aksvol9",
   "size": 5368709120,
   "type": "gp2",
   "state": "available",
   "volumeId": "vol-0baf8208853c00bc8",
   "availabilityZone": "ap-south-1a",
   "createdAt": "2020-06-23 21:01:18.965 +0000 UTC"
  },
  {
   "name": "aksvol9",
   "size": 536870912000,
   "type": "sc1",
   "state": "available",
   "volumeId": "vol-0c60636e633b1a965",
   "availabilityZone": "ap-south-1a",
   "createdAt": "2020-06-23 21:01:32.531 +0000 UTC"
  }
 ]
}
```
Create a volume in AWS
```
[POST] http://127.0.0.1:8089/v1/94b280022d0c4401bcf3b0ea85870519/volumes
``` 
Request
{
    "name": "aksvol9", 
    "availabilityZone": "ap-south-1a",
    "size": 500,
    "type": "sc1",
    "backendId": "5ef26d79c5d1b20001308f66",
    "iops": 200
}

```
```
Response:
{
 "name": "aksvol9",
 "size": 536870912000,
 "type": "sc1",
 "state": "creating",
 "volumeId": "vol-0373a261b5193db39",
 "availabilityZone": "ap-south-1a",
 "createdAt": "2020-06-23 21:22:57 +0000 UTC"
}
```
```
Create a Block device
[POST] /v1/<tenantId>/volumes
Request:
Response:
```

``` 
List all volumes
[GET] /v1/<tenantId>/volumes
```

```
Get details of a volume
[GET] /v1/<tenantId>/volumes/<volume-id>
```

```
Get volumes of a specific Cloud backend
[GET] /v1/<tenantId>/volumes?backendId=<backendId>
```


## References

[https://docs.aws.amazon.com/sdk-for-go/api/service/ec2/](https://docs.aws.amazon.com/sdk-for-go/api/service/ec2/)
[https://cloud.google.com/compute/docs/reference/rest/v1/disks](https://cloud.google.com/compute/docs/reference/rest/v1/disks)
 

## Data Model
Data Structures, key points considered, open and alternate points etc…All the data structure to be added here
![Data Model](resources/BlockDataModel.png)

### Comparison for cloud
![Cloud Block Storage Comparision](resources/CloudComparisonBlock.png)
## Sequence Diagrams
### Backend registration:
Block service backend registration is tied to a region.

![Backend Registration Sequenc](resources/BackendRegistration.png)

  
### Block service API call
![Block Service API Call](resources/BlockServiceflow.png)

## Open Issues

