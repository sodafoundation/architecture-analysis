# CSI Plug & Play Requirements & Design

Author(s): [Sushantha](https://github.com/sushanthakumar), [Sanil Kumar](https://github.com/skdwriting), [Kiran Mova](https://github.com/kmova)

March 2020, Initial Version and Analysis

## Goals

Provide a plug and play option for standard CSI drivers to SODA On prem data platform

## Motivation and background

1.  All the available CSI drivers for Storage backends can be supported with SODA quickly. (Large storage backend support quickly)
    
2.  Easy management of CSI backends for the user from Kubernetes (Single CSI plugin from SODA can manage huge number of drivers)
    
3.  All the SODA platform features will be available for all those backends (migration, telemetry and all)
    
4.  CSI Drivers are mostly supported by the vendors directly , hence easy production ready solutions for kubernetes on SODA platform

## Benefits for Storage Platforms with CSI Plug-n-Play

-   Manage the complexity of installion of CSI drivers on multiple northbound CSI compatible platforms. Flavor of K8s + Operating systems. 

-   SODA can help get additional telemetry information at cluster level by using Native Storage drivers. CSI can only volume level basic metrics can be obtained.

-   Additional features: Telemetry, Smart Alerting, Auditing, Migration across clusters/platforms
    

### Non-Goals

Do not replace the need for CSI Drivers for kubernetes

No new specification or interface

### Assumptions and Constraints

The current csi flow in SODA is working fine and no performance issues.

Avoid becoming a single point of failure in the IO path compared to when Storage Platforms are directly integrated into Container Orchestrators. 

## Architecture Analysis

### Current Flow in SODA for CSI

![](https://lh4.googleusercontent.com/R9W3cb8oOKEE9qGoi9jDkY4E1J6lI1GT5kyoE03_kngRW1V80f-5LokBGrEBeY_6c9A5SZLuZwU4aB2fJF5XUF90Gnm5X6nsSQZGLGDuX6TbzVFRUefw-eYRoIeYyk2gP2xCAzWF)

-   SODA CSI Plugin runs along with k8s sidecar containers

-   Profile info is the one helps to determine the desire backend

-   SODA CSI Plugin converts CSI calls to SODA API calls

-   All the storage resources provisioned are visible at SODA ecosystem through API Server
    

### Proposed Architecture - High Level

### ![](https://lh5.googleusercontent.com/LFYUsJO1KCoTs80sivsNbIUGiEuNloafRCBou_jyGJBvKnheduhgb7fR1cgViy-6zhWs69JClEX_7oCKxeSS6gEB0j9pAb8Mg5dp3110ZgZ9dcju1qNc5MIBoFRCroIUqEpx_9LE)

### Architecture Tenets

1.  Does not break the current API, Controller and DOCK interfaces
    
2.  Does not impact on the current API spec
    

### High Level Module Architecture

TBD

## Detailed Design

![](https://lh6.googleusercontent.com/PoPjP8D7UoDlCr0a7w-UBtkSU2Krqc2RcRPSb28WLYokK8lHhXtl_KduFCvh6Mu6jI7dtZ-l6EcTtPE9279vtkaIbJpyVhiyJOiXWDQUBT6HFEVEUrmeXCKhdZQ1J4-DQy6_OFOn)

Next:

We are checking the feasibility, prototyping. If you want to join, please join [https://sodafoundation.io/slack/](https://sodafoundation.io/slack/) and query in general channel

### /** Fix Me : The rest of the sections not updated **/

### Use case View

//Provide system context and typical use cases to determine the scope and boundaries for the module.

#### List of Typical Usecases

//Provide a list of typical usecases

#### Usecase context model

//How is the module in the overall conext of the usecase..typically for a usecase flow...how the module needs to behave...a network diagram with module could help

#### Interface Model

//What are the interfaces for the Modules needed and the view

External Interfaces

//Provide the details of the interface, type, why ? any limitations or alternates etc…

#### End User Context

//Analysis and design inputs for the end user of the module

### Functional Context

//Based on the functional requirements, what are the design aspects to be considered. Give all the details and block diagrams etc..

### Non Functional Context

//Based on the non functional requirements, what are the design aspects to be considered. Give all the details and block diagrams etc..

#### Performance

//What are the design considerations for performance

#### Security

//What are the design considerations for security

#### Other Non Functional Aspects

//What are the design considerations for other non functional requirements

### Data View

#### Data and Control Data Contexts

//Provide the details on data and control data flow

#### Data Model

Data Structures, key points considered, open and alternate points etc…All the data structure to be added here

### Development and Deployment Context

#### Code

//Provide inputs for code structure, language, any open source code can be resused, coding methods, development env etc

#### Debug Model

//how to debug the module, specific logging, debug options etc…

#### Build & Package

//How this module is built along with other modules etc…What is the package model

#### Deployment

//How to install and deploy the module in the system, hardware resource requirements etc. Any other network or such requirements..like client or http server needed etc…

### Execution View

//During the run time, any specific aspects to be considered...like logging to be done for the module etc..It is not functional logs, it is specific to the module maintenance; OR Runtime replication or any such requirements to be considered during the design

## Sequence Diagrams

//Provide the key control and data flow sequence diagrams here

## Design Alternatives and other notes

//If you have any other ideas or alternate suggestions or notes which needs further analysis or later consideration, please add here

## Open Issues

//All the open issues go here. Please track it else where to closure

## Design Requirements / Tasks

//List of detailed tasks for this module go here. Based on all the design and analysis, please list all the tasks to be completed for the implementation and release of the module. If you are updating the overall task list or location, please provide the links or ids here...This is to get an overall consolidation of task items for this module

## Scratchpad

//All raw inputs or discussion points or etc can be added here
