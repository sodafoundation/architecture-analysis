# Delfin Host Mapping Model Analysis and Design

**Authors:** [Najmudheen CT](https://github.com/NajmudheenCT), [Xulin](https://github.com/wisererik)

This is host mapping model analysis  and design for SODA Infrastructure management project Delfin.

## Goal

This documents purpose is to define data models of Delfin host mapping object models, relations and interfaces

## Non-Goals

NA

## Assumptions and Constraints

NA

## Common SAN terms related host mapping
### Volume 
Volumes are the only data layer visible to hosts, a volume could be any logical unit in the storage device such as  (LUN/Volume/Logical device/Virtual volume)
### LUN masking
[LUN masking](https://en.wikipedia.org/wiki/Logical_Unit_Number_masking#:~:text=LUN%20Masking%20is%20a%20level,specific%20host%20with%20specific%20HBA.)
In short, Masking  is the way you define which WWN (HBA's ) will be able to access the LUNs mentioned

### LUN mapping
LUN mapping determines which LUNs are available in a front end port

### Initiator
 An initiator is the consumer of storage, typically a server with an adapter card in it called a Host Bus Adapter (HBA) or an iscsi adapter.  The initiator “initiates” a connection over the fabric to one or more ports on your storage system which are called target ports.
 
### Target ports
Target ports are the ports on your storage system which deliver storage volumes (called target devices or LUNs) to the initiators


## Requirement Analysis

#### 1. Delfin project needs to support a Common  Model to represent host mapping of volumes ( LUN paths) of hetrogenious storage devices


#### 2. Delfin framework should define interfaces for the drivers to support host mapping



## Host Mapping Models of different Storage devices

Every storage models has their own way of representing host to volume mapping. Below we discuss some storage model approaches





## Delfin host mapping: Common terms to be known 

### Masking view
The masking view identifies the host group (set of hosts), the port group (set of array ports), and the storage group (set of volumes) in a combination which are associated. Masking view represent the LUN masking and mapping information. A masking view will have only one host group, storage group and port group
### Host group
logical grouping of hosts. This could be representing the real back end host group or a dummy object created by delfin. Delfin logic of creating dummy object is explained below. Host group and Storage group together represent the LUN masking.
### Port group
Logical grouping of array ports(target ports) where the volumes are mapped.This could be representing the real back end port group or a dummy object created by delfin. Delfin logic of creating dummy object is explained below. Port group and Storage group together represent LUN mapping.
### Storage group
Logical grouping of volumes . This could be representing the real back end storage group or a dummy object created by delfin. Delfin logic of creating dummy object is explained below.
### Host
Represents a host , consists of multiple initiators
### Initiator
An HBA wwn or iscsi target.
### Port
Array port through which the volume is mapped to the initiator
### Volume
volume or LUN or logical device or virtual volume which is masked to initiators.


## Delfin common model 

### Entity Relationship
Abstract model that organizes data elements and their relationships. It is not related to  implementation. 
![](./resources/ER_Model.png)

### Database model
A graphical representation of data model/schema in the relational database.

![](./resources/Delfin_host_mapping.png)

## Masking view building and querying work flow


![](./resources/work_flow_slide1.png)
![](./resources/work_flow_slide2.png)
![](./resources/work_flow_slide3.png)
![](./resources/work_flow_slide4.png)



## Driver interfaces 
Host mapping is a representation of relationship between different physical and logical resources. A single driver interface is not possible to collect and build relationships. There should be different interfaces to collect  resources and one interface to collect relation between the resources.

## Structures to be filled by driver interfaces

**masking\_views**

              {

                             id
                             is_native
                             native_masking_view_id
                             storage_id
                             relations
                                           {
                                           model_name
                                           native_id
                                           <other relation fields if any>
                                           }

              }

**host\_group**
```
{

id
name
is_native
native_host_group_id
storage_id
}
```
**Host**
```
{
id
type
is_native
ip_address
status
name
native_host_id
native_host_group_id
storage_id
}
```
**Initiator**
```
{
id
wwn
status
type
native_initiator_id
native_host_id
storage_id
}
```

**Storage_group**
```
{
id
name
is_native
native_storage_group_id
storage_id
}
```
**Port_group**
```
{
id
name
is_native
native_port_group_id
storage_id
}
```
**Port**

[port](https://github.com/sodafoundation/architecture-analysis/blob/28f8f477a1d8ae3ec1793fc8ea677a0518898339/Analysis/DataStorageProjects/Delfin/ResourceModel/ResourceModel.md#port)

**Volume**

[volume](https://github.com/sodafoundation/architecture-analysis/blob/28f8f477a1d8ae3ec1793fc8ea677a0518898339/Analysis/DataStorageProjects/Delfin/ResourceModel/ResourceModel.md#volumes)

## New driver interfaces
```
@abc.abstractmethod
def list_masking_views(self, context):
"""list host mapping relations from storage backend

 :param context: The context of delfin.
 :type context: delfin.context.RequestContext
 :return type: masking_views, list of masking_view objects_
"""


@abc.abstractmethod
def list_host_groups(self, context):
"""list host groups from storage backend.

 :param context: The context of delfin.
 :type context: delfin.context.RequestContext
 :return type: host_groups, list of host_group objects"""



@abc.abstractmethod
def list_hosts(self, context):
"""list hosts from storage backend.

 :param context: The context of delfin.
 :type context: delfin.context.RequestContext
 :return type: hosts, list of host objects"""



@abc.abstractmethod
def list_initiators(self, context):
"""list initiators from storage backend.

 :param context: The context of delfin.
 :type context: delfin.context.RequestContext
 :return type: initiators, list of initiator objects """


@abc.abstractmethod
def list_storage_groups(self, context):
"""list host storage groups from storage backend.

 :param context: The context of delfin.
 :type context: delfin.context.RequestContext
 :return type: storage_groups, list of storage_group objects_"""



@abc.abstractmethod
def list_port_groups(self, context):
"""list port groups from storage backend.

 :param context: The context of delfin.
 :type context: delfin.context.RequestContext
 :return type: port\_groups, list of port objects_"""
```
## Changes to existing driver interfaces

1. list\_volumes has to add native\_storage\_group\_id if available from backend
2. list\_port has to add native\_port\_group\_id if available from backend

## Changes to Task manager

1. New task for invoking new driver interfaces
2. New function to build masking_view relations

    ```
    build_masking_view(masking_view)
    {
        // check minimum required objects are there in the relation
        // build initiator->host->host_group->masking_view relation (create dummy objects for missing objects in the order of hierarchy )
        // build port->port_group->masking_view relation (create dummy objects for missing objects in the order of hierarchy )
        // build volume->storage_group->masking_view relation (create dummy objects for missing objects in the order of hierarchy )
        
    }
    
    ```


