# Sushi Fileshare Design

**Author(s)**: [jackhaibo](https://github.com/jackhaibo)

## Summary

This is the design spec for the CSI fileshare feature.

## Motivation

Currently kubernetes supports volume features, and fileshare features can also be added. 

### Goals

* Fileshare plugin is independent of volume plugin.
* NFS driver and oceanstor 18000 v5 driver fileshare can be created, deleted, attached, detached in kubernetes cluster.
 
### Non-Goals

Fileshare replication can be implemented in kubernetes cluster in the next phrase. 
 
## Design Details

* CreateFileShare(req *csi.CreateVolumeRequest) (*csi.CreateVolumeResponse, error)
* DeleteFileShare(shareID string) (*csi.DeleteVolumeResponse, error)
* ControllerPublishFileShare(req *csi.ControllerPublishVolumeRequest) (*csi.ControllerPublishVolumeResponse, error)
* ControllerUnpublishFileShare(req *csi.ControllerUnpublishVolumeRequest) (*csi.ControllerUnpublishVolumeResponse, error)
* ListFileShares(req *csi.ListVolumesRequest) (*csi.ListVolumesResponse, error)
* NodeStageFileShare(req *csi.NodeStageVolumeRequest) (*csi.NodeStageVolumeResponse, error)
* NodeUnstageFileShare(req *csi.NodeUnstageVolumeRequest) (*csi.NodeUnstageVolumeResponse, error)
* NodePublishFileShare(req *csi.NodePublishVolumeRequest) (*csi.NodePublishVolumeResponse, error)
* NodeUnpublishFileShare(req *csi.NodeUnpublishVolumeRequest) (*csi.NodeUnpublishVolumeResponse, error)

### Fileshare configuration yaml 
The fileshare yaml configuration file is the same as the volume, but note that the profile's storageTye is file.

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: csi-sc-opensdsplugin
provisioner: csi-opensdsplugin
parameters:
  attachMode: read
  profile: abc
allowedTopologies:
- matchLabelExpressions:
  - key: topology.csi-opensdsplugin/zone
    values:
    - default

---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: csi-pvc-opensdsplugin
spec:
  accessModes:
  - ReadWriteMany
  resources:
    requests:
      storage: 1Gi
  storageClassName: csi-sc-opensdsplugin
---
apiVersion: v1
kind: Pod
metadata:
  name: nginx 
spec:
  containers:
  - image: nginx
    imagePullPolicy: IfNotPresent
    name: nginx
    ports:
    - containerPort: 80
      protocol: TCP
    volumeMounts:
      - mountPath: /var/lib/www/html
        name: csi-data-opensdsplugin 
  volumes:
  - name: csi-data-opensdsplugin
    persistentVolumeClaim:
      claimName: csi-pvc-opensdsplugin
      readOnly: false
```

## Use Cases

User wants to use fileshare in the pods in kubernetes cluster.
 
## Implementation

* implement CreateFileShare and DeleteFileShare
* implement CreateFileShareAcl and DeleteFileShareAcl in ControllerPublishFileShare and ControllerUnpublishFileShare
* implement other functions: ListFileShares NodeStageFileShare NodeUnstageFileShare NodePublishFileShare NodeUnpublishFileShare
