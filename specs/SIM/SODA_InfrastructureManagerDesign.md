
# SODA Infrastructure Manager Design Document
**Authors:** [Najmudheen CT](https://github.com/NajmudheenCT), [Sanil Kumar D](https://github.com/skdwriting), [Xulin](https://github.com/wisererik),  [Ashit Kumar](https://github.com/kumarashit)

This documentation serves as the design spec for SODA Infrastructure Management
## Goal
To build an open source infrastructure management platform for heterogeneous storage environments. 
* Framework for heterogeneous resource management 
* APIS for Storage resource monitoring and alerts
* Common Storage resource models

## Non-Goals
* GUI for infrastructure management.
* Performance metric models
* Switch and Host Discover requirements
* Switch and Host Models
* Resource configuration requirements and design
* deployment model

## Assumptions and Constraints
NA
## Motivation and background
Today’s IT infrastructure spans across multi vendor devices and technologies. Administration of heterogeneous platforms is a challenge to storage admins.  Vendor supplied management software is either vendor locked in or not open source.  SODA foundation has vision to standardize and bring heterogeneous device management using a single platform.

## Requirement Analysis

One single pane of glass for managing heterogeneous storage devices, switch and host . A standard API model to manage devices from different vendors and a single source to collect monitoring and alert information from heterogeneous platforms.  
### Storage Discovery Requirements
#### Storage Discovery:-
User should be able to add supported device models to the system for discovery
#### Storage Monitoring and Alerts: 
Get Basis storage information, hardware information, logical resources information, alert information , performance information  and configure resources and services in the storage device. 

### Feature Requirements
#### Requirement Analysis
Analyzed Open Source monitoring solution.

1.	Stor2rrd : [Analysis](https://drive.google.com/open?id=1yJn6wcji24NvObbrxvfSVzmo6--QGSI_v5P8n4Kcc8M),
    [code analysis ](https://drive.google.com/open?id=1FC_QEadAMb2ZPBKo-FYxQ2cGRraUV72z)

#### List of Requirements
##### Functional Requirements
* Registering a storage device
* Removing a storage device
* Get registered storage device list
* Updating storage device access information
* Get storage details
* Querying storage device details
* Querying other resource  Information like (LUN,Pool,Disk, etc)
* Querying performance metrics of resources and services
* Trigger all registered  device collection
* Trigger a particular  device  collection
* Register alert source to receive alerts
* Receive alert and forward to clients
* clear alert
* Configure resources

##### Non Functional Requirements
* Performance requirements
* Security requirements
* Other non functional requirements (scalability  , HA etc…)


## Architecture Analysis
### High level Architecture
![Architecture](./resources/Architecture.jpg)



