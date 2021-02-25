# SODA Dashboard requirements for I release

  

**Author(s)**: [Anvith KS](https://github.com/anvithks)

Major Version Updates

|**Date** | **Version** | **Description** |
|---------|-------------|-----------------|
| 25th February 2021  | v0.1  | This document lists the requirements and changes needed for SODA Dashboard for I release. |



## Goals
- Multicloud UI 
    - Support Heterogenous object storage
    - Bucket Create / Update / Delete on cloud storage
    - Bucket tiering based on policy
    - Bug fixes and improvements
- Delfin UI updates
    - Add new storage driver registration support 
    - Add new methods of access to Drivers
        - CLI
        - SMI-S
    - Change the configure performance metrics API as per the changes made to Delfin.
    - Add new resource types for only NAS storage device  to support NAS capabilities
        - Qtree
        - File Systems
        - File share   
- Bug fixes and improvements
- SODA Dashboard support for HA


## Motivation and background

The handling of buckets on cloud storage using SODA Multicloud APIs is undergoing a design change as per this pr [architecture-analysis/99](https://github.com/sodafoundation/architecture-analysis/pull/99). This will change the fundamental design of how SODA Multicloud deals with registration of backends and buckets. This requires some modifications in API parameters and some changes to how the Dashboard lists and operates on backends, buckets and objects within those buckets. SODA Delfin is adding new drivers support on a regular basis and this requires the Dashboard to be updated to allow the user to register storages for these new drivers.Additionally any bug fixes and improvements will be covered as part of this release


### Requirement Analysis


#### Multicloud - Bucket Management
---
* Create bucket on cloud backend (AWS, Azure, GCP and all supported vendors) based on custom tier policy
* Delete bucket 
* Set bucket lifecycle rules
* Set bucket ACL.
* Upload objects to bucket.
* Download object from bucket.
* Create a folder in bucket.
* Delete folder in bucket.
* Upload Object to folder.
* Delete object in folder.
* Download object from folder.
* Archive / Restore object based on storage class
* Copy / Paste object across buckets

#### Multicloud - High Availablility
---
* 
#### Delfin UI Requirements
---
* Register new storage drivers from SODA Dashboard
    * VPLEX driver registration
    * VNX driver registration
    * IBM DS8000 driver
    * EMC PowerMax
    * EMC ExtremeIO
    * IBM FlashSystem
    * NetApp FAS
* Add new methods of access to storage devices
    * CLI method
    * SMI-S method
* Change the configure performance metrics API as per the changes made in Delfin.
    * Two new APIs will be added.
    * Current API will be changed; URI will be changed. Parameters will be changed.
    * New API to get supported metrics for storage and resource (device, pool, volume).
    * Currently only device level metrics are available.
    * Pool level and volume level metrics will be provided. (Currently in the design phase.)
    * Performance metrics may be different based on the driver. The new API will give the supported metrics for the driver and the dashboard visualization should allow customization by the user.(Need to discuss how this will be implemented)
*  Add new resource types for only NAS storage device  to support NAS capabilities
    * Qtree
    * File Systems
    * File share
* Improve Grafana monitoring with additional dashboards and customization.

### Input Requirements

* TBD
  
##### Functional Requirements
* User can create, update, delete a bucket on the cloud vendor backend from SODA Dashboard based on custom tier policy
* User can create, delete folders on cloud vendor buckets
* User can upload, download and delete objects to and from the cloud vendor buckets
* User can create, update and delete lifecycle rules on buckets
* User can archive and restore objects based on storage tier
* User can copy / paste objects across cloud buckets
* User is able to register new storage devices supported by Delfin
* User is able to view new resource types for NAS storage devices
* User is able to configure performance metric collection for storage devices and customize the Grafana dashboards to show the visualizations.


##### Non Functional Requirements

  
## Architecture Analysis

### Module Architecture


The current SODA Multi-cloud architecture can be seen in Fig-1 below.
![Current SODA Multi-cloud architecture](resources/multi-cloud-fig1.png)

The updated proposed architecture can be seen in Fig-2 below.
![Updated SODA Multi-cloud architecture](resources/multi-cloud-fig2.png)


### Use case View
To create a bucket in SODA a user must register a backend. This registration process requires the user to create a bucket using the cloud console and provide the name of the bucket at the time of backend registration. All further bucket operations in SODA multicloud are currently operations on folders under the bucket created manually.
This poses many challenges and issues when versioning, encryption and other bucket operations are considered.
To solve this issue, the new changes proposed will allow the user to create a bucket on the cloud backend from SODA. The user will be able to set a custom tier policy and apply this tier to the bucket.


