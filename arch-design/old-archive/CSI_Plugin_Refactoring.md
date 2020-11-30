# CSI Plugin Refactoring

**Authors:** [Xing Yang](https://github.com/xing-yang)

This document describes how to refactor the existing OpenSDS CSI Plugin.

## Motivation

When the CSI plugin was first introduced in OpenSDS, it only supported iSCSI for Native OpenSDS Southbound LVM driver.  As a result, the iSCSI logic is everywhere in the code.  When other protocol such as Ceph RBD was added, it was added on top of the existing code without a design change. Even if Ceph RBD itself does not require iSCSI, iSCSI initiator is still required to be present on the node for the OpenSDS CSI plugin to work. 

The existing OpenSDS CSI plugin should be refactored so that iSCSI is not required for non-iSCSI protocols.

### Goal

* Refactor CSI plugin so that iSCSI is not required for non-iSCSI protocol.
* Add support for FC.
* Make sure Ceph RBD still work properly after the refactoring.
* Make sure Cinder driver still work properly after the refactoring.
* Merge connector lib in nbp and hotpot repo.
* Installation using ansible-playbook needs to be modified so that iSCSI is not required for non-iSCSI protocols.

## CSI Plugin Interfaces

### NodeGetInfo and NodeGetId

NodeGetInfo and NodeGetId need to return different values as NodeId for different protocols.  One problem is that when NodeGetInfo is called, the CSI plugin does not really know what protocol it is.  The protocol info gets passed in when a volume is attached to the node. So NodeGetInfo needs to find out the iSCSI initiator name, WWPN and WWNN, and other info and return all of them if needed.  Normally a node won’t be used for both iSCSI and FC, but it is possible that the node was used for iSCSI earlier and now it is configured to be used for FC.

#### iSCSI

NodeId for iSCSI is as follows:

```
hostName,iqn:<initiator name>
```

Note: hostName is the Kubernetes node name, and initiator name is the iSCSI initiator node name.


#### FC

NodeId for FC is as follows:

```
hostName,wwpn:<port_name>,wwnn:<node_name>
```

Note: hostName is the Kubernetes node name, and port_name is the WWPN and node_name is the WWNN.


#### Ceph RBD

NodeId for RBD is as follows:

```
hostName
```

Note: hostName is the Kubernetes node name.


#### NVMeoF

NodeId for NVMeoF is as follows:

```
hostName
```

Note: hostName is the Kubernetes node name.


#### Default

Default NodeId is as follows:

```
hostName
```

Note: hostName is the Kubernetes node name.


### NodeStageVolume and NodeUnstageVolume

NodeStageVolume and NodeUnstageVolume should only call iSCSI related commands when the protocol is iSCSI.


### NodePublishVolume and NodeUnpublishVolume

NodePublishVolume and NodeUnpublishVolume should only call iSCSI related commands when the protocol is iSCSI.


### ControllerPublishVolume

ControllerPublishVolume will pass in NodeId to the Initiator field.  The content of NodeId could be different based on different protocols.

The following code snippet is from ControllerPublishVolume in controller.go.

```
        localIqn := req.NodeId
 
        attachReq := &model.VolumeAttachmentSpec{
                VolumeId: req.VolumeId,
                HostInfo: model.HostInfo{
                        Host:      req.NodeId,
                        Platform:  runtime.GOARCH,
                        OsType:    runtime.GOOS,
                        Initiator: localIqn,
                },
                Metadata: req.VolumeAttributes,
        }
```


## The Connector

Attach() and Detach() are the two methods that an OpenSDS driver with a different protocol needs to implement.  They are called by the Node RPCs in node.go.  Attach() is called in NodeStageVolume().  Detach() is called by delTargetPathInAttachment() which is called in NodeUnstageVolume().

Connectors for different protocols such as iSCSI or RBD are under the hotpot repo https://github.com/opensds/opensds/tree/master/contrib/connector.
