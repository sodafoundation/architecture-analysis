# Multi-OpenStack Design

**Authors:** [Xing Yang](https://github.com/xing-yang)

This document is a design proposal on how OpenSDS can manage Cinder in multiple OpenStack deployments.

This design was drafted [here](https://docs.google.com/document/d/1fDMlePgsYi8blZt8MfIX7xa2CrdwUFgXMaBQ0xZfQsI/edit?usp=sharing).

## Background

Some customers have multiple OpenStack deployments.  It is hard to manage them separately.  The multi-OpenStack feature is intended to provide a single pane of management so customers can manage Cinder in multiple OpenStack deployments using one master OpenSDS.

## Objectives

### Goals

* OpenSDS shall be able to manage Cinder in multiple OpenStack deployments.
* Federated keystone or shared keystone will be used to provide identity service for the master OpenSDS and multiple OpenStack deployments it manages.


## Design Details

### Use Case 1: OpenSDS Provision Volumes through Cinder Driver

This diagram shows how OpenSDS can manage Cinder in multiple OpenStack deployments using cinder volume driver.  Note that this picture does not include Cinder Compatible API Adapter for simplification.

![Multi-OpenStack Use Case 1](resources/multi_openstack_usecase_1.png?raw=true "Multi-OpenStack Use Case 1")

* Admin: OpenSDS will provide APIs to register storage backends.  There will be a storage backend type associated with each storage backend.  Cinder backend from an OpenStack deployment can be registered as a storage backend of type Cinder.

* Storage backend info will be saved in OpenSDS database as key-values pairs.  Cinder backend info can be saved as a storage backend info in the database.

* When a volume is created in OpenSDS and a corresponding volume database entry is created, there will be a field to record the Cinder backend that this specific volume belongs to. (may not need this because we already have profile info in volume and profile is mapped to volume type.  In profile, we may need Cinder backend info)

* OpenSDS will map OpenSDS profile to Cinder pool and volume type.  When a Cinder backend is added to OpenSDS, OpenSDS will discover and find pools and volume types in Cinder and create corresponding profiles in OpenSDS.  Custom properties in OpenSDS profiles will be used to map Cinder volume types.

* There should be a manual sync functionality that the admin can run after a Cinder backend is registered with OpenSDS to retrieve any new pools and volume types. 
(Note: There is an OpenSDS API to return Volume Types for StoragePool.)
(Automatic sync can be done in the next phase.)

* Authentication and authorization will be done using Federated Keystone across the Master OpenSDS and the OpenStack deployments it manages.


### Use Case 2: OpenStack Consumes OpenSDS Native Volumes

This diagram show how OpenStack can use volumes created using native OpenSDS drivers.

![Multi-OpenStack Use Case 2](resources/multi_openstack_usecase_2.png?raw=true "Multi-OpenStack Use Case 2")

* Native volumes created using native driver such as LVM or Ceph is not part of Cinder.  This volume can be attached to a Nova VM using Cinder Compatible API adapter.

```
        cinder attachment-create <volume uuid> <instance uuid>
```

`volume uuid` is the id of the volume in OpenSDS.  `instance uuid` is the id of the Nova VM.

* Using Cinder Compatible API Adapter, create volume will take Cinder API request and convert to OpenSDS request. This volume can be attached to a Nova VM using Cinder Compatible API Adapter as well.

```
            cinder attachment-create <volume uuid> <instance uuid>
```

`volume uuid` is the id of the volume in OpenSDS.  `instance uuid` is the id of the Nova VM.


## Appendix

### How Does OpenStack Nova VM Consume OpenSDS Native Volumes

This example assumes there is an OpenStack deployment and an OpenSDS deployment, and Cinder Compatible API Adapter is configured. It describes how to create a volume using OpenSDS CLI and how the volume is attached to a Nova instance in OpenStack. This is for use case 2.

#### Use OpenSDS CLI to Create a Volume

* Run the command "export OPENSDS_AUTH_STRATEGY=keystone".
* Run the command "export OPENSDS_ENDPOINT=http://localhost:50040".
* Run the command "source /opt/stack/devstack/openrc admin admin".
* Run the command "osdsctl volume create 1 --name testVolume“.

#### Attach/Detach Volume to Nova VM Using OpenStack CLI

* Login to the OpenStack node.
* Run the command "source /opt/stack/devstack/openrc admin admin".
* Run the command "export OS_VOLUME_API_VERSION=3".
* Run the command "glance image-list" to get the <cirros image ID>.
* Run the command "nova boot testServer --flavor c1 --image <cirros image ID>“ to boot a new test server.
* Run the command "nova volume-attach <test server ID> <test volume ID>“
* Run the command "nova get-vnc-console <test server ID> novnc" to get a vnc console.
* Use the vnc console URL to enter the test server. Then check if the result is correct. The account number is "cirros" and the password is "cubswin:)".
* Run command ‘sudo fdisk -l’ in the test server. If the volume was successfully attached to the test server, then in the result of "sudo fdisk -l", there is a disk whose name is the same as the value of the device in the result of the "nova volume-attach" command and its size is the same as the size of the volume.
* Run the command "nova  volume-detach <test server ID> <test volume ID>" to detach volume.

### List Attachments and Delete Attachments

* Login the openstack node.
* Run command ‘source openrc’ to get the authentification EVN variable.
* Run command ‘cinder attachment-list’ to list all attachments.
* Run ‘cinder attachment-delete <attachment ID>’ to delete an attachment
