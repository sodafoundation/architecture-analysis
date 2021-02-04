# Multi-Cloud High Availability Design

**Author(s):** [Rajat Verma](https://github.com/rajat-soda)
## Background
In the real world, there can be situations when a dip in performance of your servers might occur from events ranging from a sudden spike in traffic can lead to a sudden power outage. It can be much worse and your servers can be crippled- irrespective of whether your applications are hosted in the cloud or a physical machine. Such situations are unavoidable. However, rather than hoping that it doesn’t occur, what you should actually do is to gear up so that your systems don’t encounter failure.
The answer to the problem is the use of High Availability (HA) configuration or architecture. High availability architecture is an approach of defining the components, modules or implementation of services of a system which ensures optimal operational performance, even at times of high loads. Although there are no fixed rules of implementing HA systems, there are generally a few good practices that one must follow so that you gain the most out of the least resources.

**Why do we need it?**

Let us define downtime before we move further. Downtime is the period of time when your system (or network) is not available for use, or is unresponsive. Downtime can cause a company huge losses, as all their services are put on hold when their systems are down. In August 2013, Amazon went down for 15 minutes (both web and mobile services), and ended up losing over $66000 per minute. Those are huge numbers, even for a company of the size of Amazon.
There are two types of downtimes- scheduled and unscheduled. A scheduled downtime is a result of maintenance, which is unavoidable. This includes applying patches, updating softwares or even changes in the database schema. An unscheduled downtime is, however, caused by some unforeseen event, like hardware or software failure. This can happen due to power outages or failure of a component. Scheduled downtimes are generally excluded from performance calculations.
The prime objective of implementing High Availability architecture is to make sure your system or application is configured to handle different loads and different failures with minimal or no downtime. There are multiple components that help you in achieving this, and we will be discussing them briefly.

## Goal
The document provides the use case, design and implementation guidelines for supporting High Availability in SODA Multi Cloud project. It proposes a generic method of providing High Availability for the Multi Cloud project to begin with and include the other projects in the subsequent releases. To begin with, Kubernetes is considered to provide the High availability for the containers of the Multi Cloud service. 


## Motivation and background
As SODA provides a critical data management platform for both On-Prem and for Cloud based workloads, it is imperative that there is no bottleneck and the data and services be available even in case of any node going down. However, the current  architecture where all the components of SODA installed on a single node are a Single Point of Failure. In case of any failure of any of the services or the node going down, the data will not be accessible to the user. Hence, it's recommended to have the SODA installation in a High Available configuration, which can withstand failure of nodes and the data and services are accessible in case of any node or service going down. 

## Non-Goals
 - Clients access to the data and services is seamless. 
 - The Services should scale up / down to provide data and services to users.
 - Installation / Uninstallation of SODA provides options to install SODA Multi Cloud in standalone / HA mode. 


## Assumptions and Constraints
TBD

## Use case View
- High Availability : To provide High Availability to the SODA Multi Cloud Deployment.
Scale up / down as per the need of data movement. 



![Multi Cloud HA Design](resources/multicloud_ha.png)

                                       Figure - 1

Here is the High Level Architecture for providing HA for SODA Multi Cloud services.  


As seen in Figure-1, the scope of I release is to provide  High Availability for the SODA Multi Cloud Services to begin with and gradually migrate the other components over the subsequent releases. 
With this in mind, the SODA node can serve as the  Container Orchestrator (K8s / Mesos / Docker Swarm) Master node and the Multi Cloud services be deployed on the Deployment Cluster as shown in Figure - 2.
This is a generic architecture giving flexibility to the customer to choose any of the orchestration tools as required. For the PoC K8s is the tool of choice, however it can be replaced with the customer’s choice (after necessary tests are conducted). 

*Though, this architecture would still not be fully HA , as there is still a single point of failure with the SODA Node not being fully HA. 
To be fully HA, the  Master node should be clustered in a multi-master configuration, however due to resource restrictions , it is proposed to go with the Architecture depicted in Figure - 1. 

## Development and Deployment Context : 

### Installation:
  Installation would need to take into account the requirements for the Deployment Cluster for the containers. The Cluster would need to be set up during the installation phase and the prerequisite softwares would need to be installed on the Nodes. Communication would need to be established between the Master SODA node and the cluster. 

### Uninstallation:
 Uninstallation would need to clean up and return the Nodes in the Cluster to their initial state. This would require uninstalling the components like Databases, Services and the connection established between the Master and the MC-Nodes. 

### Database: 
Multi Cloud Database (Mongo) should be distributed over the three MC-Nodes, so as to make the data redundant over the three nodes which will make the data accessible in case of failure of any of the MC-Nodes. 

### Client Access:
 The clients would need to access the Multi-Cloud Cluster rather than the SODA node, on which the MC-Containers were deployed earlier. 

### Failure Cases :
 In case of failure of any of the MC-Nodes, the services would still be available via the other redundant nodes. However, in case of the failure of the SODA Master node, the orchestrator will not be available. 
 In order to handle the failure of the Master node, the Master node should also be clustered.
