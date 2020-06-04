# VRO Plugin for OpenSDS Analysis and Design
**Author(s)**:  [Najmudheen](https://github.com/NajmudheenCT)
## 1. Introduction
VMware vRealize Orchestartor is an automation and management engine provided by VMware. This Analysis bringsout the objectives , scope, dependencies and design of this project.
  ###  1.1 Purpose of the system
  Enable Vmware vRealize Orchetrator client to orchestrate storage management tasks of  OpenSDS or any thirdparty storage platform. The system will include VMware-integrated workflows that enable automation of storage provisioning and protection operations .
### 1.2 Scope of the system
Include workflows that can be automated.
Probable workflows that can be included from openSDS
* Register storage platform
* Unregister storage platform
* create datastore
* delete datastore
* expand datastore
* Add RDM to VM
* Delete RDM 
* create Volume
* delete Volume
* expand Volume
* volume snapshot
* Disk exists in ESXi

### 1.3 Objectives and success criteria of the project
* User should be  able to see predefined workflows in VRO client
* User should be  able to execute predefined workflows.
* User should be  able to create new workflows by  including predefined  workflows from VRO plugin.

### 1.4 Dependency
* VMware vRealize Orchestrator Client
* VMware vRealize Orchestrator SDK
* VMware vRealize Orchestrator Appliance  
* vCenter server managing one or more ESX servers.


## 2.VRO Architecture

![](vro_architecture.png)

[VMware VRO Architecture](https://docs.vmware.com/en/vRealize-Orchestrator/7.5/com.vmware.vrealize.orchestrator-install-config.doc/GUIDEF1E6F31-D414-4A9B-9CF5-4919FA22DEB8.html)

##  3.OpenSDS VRO Plugin Design

![](Plugin_design.png)

#### Workflow package
Workflow  Packages can contain workflows, actions, policies, configurations, and resources. This is separate package which can be imported and use in VRO client.
#### Plugin Package
Plugin package is the external Orchestrator plug-ins, you can access and control external technologies and applications. We have to expose OpenSDS/third-party functionalities/objects to VRO . Plug-in lets you incorporate objects and functions in workflows and run workflows on the objects of OpenSDS/third-party.
##### Plugin Adapter
This is the layer which implements interfaces which defines an adapter between a plug-in and the Orchestrator server.
##### OpenSDS Plugin
This is the layer which invokes opensds-hotpot APIs