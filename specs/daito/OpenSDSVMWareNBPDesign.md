# OpenSDS VMWare NBP Design

**Author(s)**: [zengyingzhe](https://github.com/zengyingzhe), [Sanil Kumar](https://github.com/skdwriting), [Najmudheen](https://github.com/NajmudheenCT), [Himanshu](https://github.com/himanshuvar)

## Summary
OpenSDS provides multiple north bound platforms support (viz., Kubernetes, OpenStack etc - Please refer the OpenSDS Architecture for more information-www.opensds.io). As a part of supporting more platforms, we plan to support VMWare platform as well.

This documentation serves as the design spec for OpenSDS VMware NBP Architecture & Design.

## Motivation

VMware is one of the most important and widely used virtualization platforms, thus if we implement NBPs for VMware, it will greatly extend OpenSDS' usage scale(using in production environment) and also helps to improve the competitiveness of OpenSDS.

### Goals
* Support Specified VMware plugins and APIs using OpenSDS VMWare NBP (A tentative list is added below. However we need to further analyse on the feasibility and adaptation)
* Build a OpenSDS VMWare NBP framework which can support multi-backend and single backend storage
* Can support 3rd party backend adapters


| NBP | Purpose |
|--|--|
| VASA(vSphere APIs for Storage Awareness) Provider | Provide the capabilities recognition of storage arrays to vSphere vCenter. |
| VRO(vRealize Orchestrator) | Automate complex IT tasks for storage management. |
| SRA(Storage Replication Adapter) | Cooperate with VMware SRM to provide the VMs protection by using array-based replication. |
| vROps(vRealize Operations) | Provide integrated performance, capacity and configuration monitoring capabilities for storage arrays. |
| vRLog(vRealize Log Insight) | Provide intuitive, actionable dashboards, sophisticated analytics for storage arrays. |

Similar to the above plugins, we can support specific free standard vSphere Web Client plugins for storage management to ease the administrators for supported storage backends (already) in OpenSDS.


### Non-Goals
* No Data path plugins or features considered
* Orchestration, Telemetry and Analytics specific plugins are low priority and may need to consider the current available features in OpenSDS for their design (consider based on the basic feasibility analysis)
None.

## High Level Architecture

![](media/OpenSDS-VMWareNBP-Architecture-01.png)

vSphere Client: This is VMWare vSphere Client which gets all the client plugins get registered to. After the registration, the client gets the additional API capabilities through each of the registered plugins.

OpenSDS VMWare NBP: This serves as the north bound plugin for VMware in OpenSDS. This consists of the following main modules - a) Plugin Manager  and b) Plugins
* a) Plugin Manager : As the OpenSDS VMWare NBP supports multiple plugins, the plugin manager will handle the metadata information of all the plugins for opensds; it will do the registration of each plugins to vSphere client; config/manage (if any)
* b) Plugins: Each plugin provides the specific VMWare plugin features through the VmWare SDK APIs on the north and backend APIs on the south.


## Design
![](media/OpenSDS-VMWareNBP-Architecture-02.png)

* Plugin Manager : as described above 
* Plugin: Each plugin provides the specific VMWare plugin features through the VmWare SDK APIs on the north and backend APIs on the south.
<br> Plugin consists of :</>
    * VMWare plugin Layer: VMWare plugin layer based on the VMWare SDK APIs. This layer is independent of the backend storages (Storage Independent Logic)
    * Adapter Manager: In case the plugin supports multiple adapters of backend, this acts as an adapter manager layer. Handles the common config, metadata etc for the same
    * OpenSDS Adapter: Adapter layer for OpenSDS hotpot backend
    * 3rd Party Adapter: (OPTIONAL) - Adapter to connect directly to storage or any other backend server( like hotpot)     
 (Can be for performance testing, backend bypass testing, backend comparison, or single backend storage scenario)

The plugins can be easily integrated and customized.  It is extensible to more 3rd party storage and backends. By supporting the OpenSDS hotpot, all the supported backend storage will be available for the north bound plugin (unless specific requirements from the north bound platform)

**Notes:**
Testing Support : 3rd party storage testing for the plugins without the hotpot APIs is possible (We can use the direct adapter for such testing. However this is not a direct feature requirement from the design)

This framework should be provided as a library depending on coding language, for instances, a Jar package for Java or a library for C/C++.

The benefit of this architecture is decoupling NBP's logic that related to VMware integration and storage interaction, so that the NBP developers for VMware integration can focus on **Storage Independent Logic** development, no need to concern the details of interacting with storages.

### Data model impact

Don't know! :)
TBD

### REST API impact

* OpenSDS hotpot may need to refactor or add new APIs to support the VMWare north bound platform features.

### Security impact

Not expected; need to check more
TBD

### Other end user impact

User can use OpenSDS as storage array to provide storage capabilities to VMware, and utilize OpenSDS' advanced features.

### Performance impact

Not expected. Need to analyse and later test
TBD

### Other deployment impact

Installation of OpenSDS VMWare NBP; Each plugin registration

### Developer impact

Developers can participate in VMware NBPs implementation, or push adapters to scale NBPs up to adapt 3rd-party storage.

## Use Cases

VMware can integrate with OpenSDS.

## Implementation

All VMware NBPs should northboundly implement VMware's APIs, to integrate within VMware platforms.
For details, refer to VMware's official website.
TBD (We will add more  design details and implementation details here)

**The Backend API list (in progress) :

| Interface | Description |
|--|--|
| ListStorages | List all of the storages that adapters support |
| CreateVolume | Create a volume |
| DeleteVolume | Delete a volume |
| ListVolumes | Batch query volume info |
| AttachVolume | Attach a volume to host |
| DetachVolume | Detach a volume from host |
| GetStoragePool | Get storage pool info |
| ListStoragePools | Batch query storage pools info |

### Object Definitions (in progress/TBD)

1. Device Info
    class DeviceInfo {
        string name;
        string model;
        string sn;
        string status;
        string vendor;
    }

2. Volume Object
    class Volume {
        string name;
        string id;
        string wwn;
        ALLOC_TYPE allocType;
        int capacity;
    }
    
    enum ALLOC_TYPE {
        thin, thick
    }



### Interface Definitions (in progress/TBD)

1. CreateVolume
   - parameters
     **name**: volume name
     **allocType**: thin or thick
     **capacity**: volume capacity
     **poolId**: storage pool ID which volume belongs
   - return
     **Volume object**

2. DeleteVolume
   - parameters
     **volumeId**: ID of volume to delete
   - return
     **void**

3. ListVolumes
   - parameters
     **poolId**: (Optional)query all volumes belong to pool if specified, otherwise query all volumes in storage
   - return
     **List of volume object**

4. AttachVolume
   - parameters
     **volumeId**: ID of volume to attach
     **connect**: connect object
   - return
     **void**

5. DetachVolume
   - parameters
     **volumeId**: ID of volume to detach
     **connect**: connect object
   - return
     **void**

6. GetStoragePool
   - parameters
     **poolId**: ID of storage pool to query
   - return
     **Storage pool object**

7. ListStoragePools
   - parameters
     **void**
   - return
     **List of storage pool objects**

## Alternatives considered

No.

## Open issues

1. All VMware plugins may not be handling the backend storage alone. Hence, needs to analyse and confirm each plugin before integration to OpenSDS
2. Some plugins uses features like Orchestration, Telemetry and Analytics for VMWare platform. Need to analyse so as to utilize the existing such features from OpenSDS.
