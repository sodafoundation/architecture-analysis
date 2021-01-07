# **Data Replication and Disaster Recovery in Kubernetes**

## Introduction
Kubernetes does the Data Replication/Recovery via Volume Snaphot and Cloning of Volumes. Kubernetes defines a lifecycle stages and interfaces for Volume snapshotting and cloning, now it’s left for the vendor CSI drivers to implement these stages to provide the Data replication and Recovery. Currently NetApp Trident, Portworx , AliCloudDisk, AWS Elastic Block Storage, Ceph RBD, Cinder, Dell EMC PowerScale/Unity/VxFlexOs, Digital Ocean Block Storage, Ember CSI, Hitachi Vantara, HPE, Huawei Storage CSI,  Linstor, Nutanix, Tencent, Vsphere and many more provides snapshot and recovery Options

## Project Analysis Template
## Project Name
### Introduction
| Name | Kubernetes |
|--|--|
| **Website/Project Page** | https://kubernetes.io/  |
| **Organization/Foundation** | CNCF Foundation |
| **License** | Apache 2 |
| **Open/Proprietary** | Platform is OpenSource but the Vendors CSI Implementation might carry different license |
| **Source Path(if open source)** | https://kubernetes-csi.github.io/docs/ |
| **Brief Description** | Kubernetes is a widely adopted platform in Container world, Soda also gives a CSI solution where Containers can use the volumes provisioned by SODA. Data Replication/Recovery is one of the very important fearure provided by Kubernetes, this documents covers the DR solution provided by Kubernetes  |

### Technical Analysis


#### Snapshot and Restore Feature

Many storage systems provide the ability to create a "snapshot" of a persistent volume. A snapshot represents a point-in-time copy of a volume. A snapshot can be used either to provision a new volume (pre-populated with the snapshot data) or to restore the existing volume to a previous state (represented by the snapshot).

Kubernetes CSI currently enables CSI Drivers to expose the following functionality via the Kubernetes API:

1. Creation and deletion of volume snapshots via [Kubernetes native API](https://kubernetes.io/docs/concepts/storage/volume-snapshots/). 
2. Creation of new volumes pre-populated with the data from a snapshot via Kubernetes [dynamic volume provisioning](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/).

Note: Documentation under https://kubernetes.io/docs is for the latest Kubernetes release. Documentation for earlier releases are stored in different location. For example, this is the documentation location for [v1.16](https://v1-16.docs.kubernetes.io/docs/concepts/storage/volume-snapshots/).

## Implementing Snapshot & Restore Functionality in Your CSI Driver

To implement the snapshot feature, a CSI driver MUST:

* Implement the `CREATE_DELETE_SNAPSHOT` and, optionally, the `LIST_SNAPSHOTS` controller capabilities
* Implement `CreateSnapshot`, `DeleteSnapshot`, and, optionally, the `ListSnapshots`, controller RPCs.

For details,  see the [CSI spec](https://github.com/container-storage-interface/spec/blob/master/spec.md).


#### Lifecycle of a volume snapshot and volume snapshot content  

VolumeSnapshotContents are resources in the cluster. VolumeSnapshots are requests for those resources. The interaction between VolumeSnapshotContents and VolumeSnapshots follow this lifecycle:

*Provisioning Volume Snapshot*

There are two ways snapshots may be provisioned: pre-provisioned or dynamically provisioned.  
 `Pre-provisioned`  
A cluster administrator creates a number of VolumeSnapshotContents. They carry the details of the real volume snapshot on the storage system which is available for use by cluster users. They exist in the Kubernetes API and are available for consumption.  
`Dynamic`  
Instead of using a pre-existing snapshot, you can request that a snapshot to be dynamically taken from a PersistentVolumeClaim. The VolumeSnapshotClass specifies storage provider-specific parameters to use when taking a snapshot.  
  
`Binding`  
The snapshot controller handles the binding of a VolumeSnapshot object with an appropriate VolumeSnapshotContent object, in both pre-provisioned and dynamically provisioned scenarios. The binding is a one-to-one mapping.  
In the case of pre-provisioned binding, the VolumeSnapshot will remain unbound until the requested VolumeSnapshotContent object is created.  

Persistent Volume Claim as Snapshot Source Protection  

The purpose of this protection is to ensure that in-use PersistentVolumeClaim API objects are not removed from the system while a snapshot is being taken from it (as this may result in data loss).  

While a snapshot is being taken of a PersistentVolumeClaim, that PersistentVolumeClaim is in-use. If you delete a PersistentVolumeClaim API object in active use as a snapshot source, the PersistentVolumeClaim object is not removed immediately. Instead, removal of the PersistentVolumeClaim object is postponed until the snapshot is readyToUse or aborted.  

*Delete*  
Deletion is triggered by deleting the VolumeSnapshot object, and the DeletionPolicy will be followed. If the DeletionPolicy is Delete, then the underlying storage snapshot will be deleted along with the VolumeSnapshotContent object. If the DeletionPolicy is Retain, then both the underlying snapshot and VolumeSnapshotContent remain.  


### Snapshot APIs

With the promotion of Volume Snapshot to beta, the feature is now enabled by default on standard Kubernetes deployments instead of being opt-in. This involves a revamp of volume snapshot APIs.

The schema definition for the custom resources (CRs) can be found [here](https://github.com/kubernetes-csi/external-snapshotter/blob/release-3.0/client/apis/volumesnapshot/v1beta1/types.go). The CRDs are no longer automatically deployed by the sidecar. They should be installed by the Kubernetes distributions.


Each VolumeSnapshot contains a spec and a status.
```
apiVersion: snapshot.storage.k8s.io/v1beta1
kind: VolumeSnapshot
metadata:
  name: new-snapshot-test
spec:
  volumeSnapshotClassName: csi-hostpath-snapclass
  source:
    persistentVolumeClaimName: pvc-test
 ```
persistentVolumeClaimName is the name of the PersistentVolumeClaim data source for the snapshot. This field is required for dynamically provisioning a snapshot.
A volume snapshot can request a particular class by specifying the name of a VolumeSnapshotClass using the attribute volumeSnapshotClassName. If nothing is set, then the default class is used if available.
For pre-provisioned snapshots, you need to specify a volumeSnapshotContentName as the source for the snapshot as shown in the following example. The volumeSnapshotContentName source field is required for pre-provisioned snapshots.
```
apiVersion: snapshot.storage.k8s.io/v1beta1
kind: VolumeSnapshot
metadata:
  name: test-snapshot
spec:
  source:
    volumeSnapshotContentName: test-content
 
Volume Snapshot Contents
Each VolumeSnapshotContent contains a spec and status. In dynamic provisioning, the snapshot common controller creates VolumeSnapshotContent objects. Here is an example:
apiVersion: snapshot.storage.k8s.io/v1beta1
kind: VolumeSnapshotContent
metadata:
  name: snapcontent-72d9a349-aacd-42d2-a240-d775650d2455
spec:
  deletionPolicy: Delete
  driver: hostpath.csi.k8s.io
  source:
    volumeHandle: ee0cfb94-f8d4-11e9-b2d8-0242ac110002
  volumeSnapshotClassName: csi-hostpath-snapclass
  volumeSnapshotRef:
    name: new-snapshot-test
    namespace: default
    uid: 72d9a349-aacd-42d2-a240-d775650d2455
 ```
volumeHandle is the unique identifier of the volume created on the storage backend and returned by the CSI driver during the volume creation. This field is required for dynamically provisioning a snapshot. It specifies the volume source of the snapshot.
For pre-provisioned snapshots, you (as cluster administrator) are responsible for creating the VolumeSnapshotContent object as follows.
```
apiVersion: snapshot.storage.k8s.io/v1beta1
kind: VolumeSnapshotContent
metadata:
  name: new-snapshot-content-test
spec:
  deletionPolicy: Delete
  driver: hostpath.csi.k8s.io
  source:
    snapshotHandle: 7bdd0de3-aaeb-11e8-9aae-0242ac110002
  volumeSnapshotRef:
    name: new-snapshot-test
    namespace: default
``` 
snapshotHandle is the unique identifier of the volume snapshot created on the storage backend. This field is required for the pre-provisioned snapshots. It specifies the CSI snapshot id on the storage system that this VolumeSnapshotContent represents.


### Test Snapshot Feature

To test snapshot Beta version, use the following [example yaml files](https://github.com/kubernetes-csi/external-snapshotter/tree/release-3.0/examples/kubernetes).

Create a _StorageClass_:
```
kubectl create -f storageclass.yaml
```

Create a _PVC_:
```
kubectl create -f pvc.yaml
```

Create a _VolumeSnapshotClass_:
```
kubectl create -f snapshotclass.yaml
```

Create a _VolumeSnapshot_:
```
kubectl create -f snapshot.yaml
```

Create a _PVC_ from a _VolumeSnapshot_:
```
kuberctl create -f restore.yaml
```


### Volume Cloning  

The CSI Volume Cloning feature adds support for specifying existing PVCs in the dataSource field to indicate a user would like to clone a Volume.  

A Clone is defined as a duplicate of an existing Kubernetes Volume that can be consumed as any standard Volume would be. The only difference is that upon provisioning, rather than creating a "new" empty Volume, the back end device creates an exact duplicate of the specified Volume.  

The implementation of cloning, from the perspective of the Kubernetes API, simply adds the ability to specify an existing PVC as a dataSource during new PVC creation. The source PVC must be bound and available (not in use).  

#### Implementing Volume cloning functionality

To implement volume cloning the CSI driver MUST:

1. Implement checks for `csi.CreateVolumeRequest.VolumeContentSource` in the plugin's `CreateVolume` function implementation.
2. Implement `CLONE_VOLUME` controller capability.

It is the responsibility of the storage plugin to either implement an expansion after clone if a provision request size is greater than the source, or allow the external-resizer to handle it.  In the case that the plugin does not support resize capability and it does not have the capability to create a clone that is greater in size than the specified source volume, then the provision request should result in a failure.


#### Enabling Cloning for CSI volumes in Kubernetes
Volume cloning was promoted to Beta in version 1.16 and GA in 1.18, and as such is enabled by defult for kubernetes versions >= 1.16

In Kubernetes 1.15 this feature was alpha status and required enabling the appropriate feature gate:

```
--feature-gates=VolumePVCDataSource=true
```

#### Volume Cloning API

```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: cloned-pvc
spec:
  storageClassName: my-csi-plugin
  dataSource:
    name: existing-src-pvc-name
    kind: PersistentVolumeClaim
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi
      
```

### Recommendation for SODA
Soda CSI Plugins should use these api's to enable the Data Replication for the volumes which are created in K8s using the Soda Plugins.  
Currently none of these provides the feature for continous backup/cloning, soda can provide this feature for DR which will be effectively used in continous backup of databases.  

