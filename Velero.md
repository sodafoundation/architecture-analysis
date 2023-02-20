



# VELERO



 ***Project Analysis***

***INTRODUCTION:***

It is a open source tool to safely backup and restore, perform disaster recovery and migrate kubernetes cluster resources and persistent volumes.

>Take backups of your cluster and restore in case of loss.

>Migrate cluster resources to other clusters.

>Replicate your production cluster to development and testing clusters.


**PROJECT SUMMARY:**


|                |                                                  |
|----------------|---------------------------------------------|
|website|[https://www.vmware.com/in.html](https://www.vmware.com/in.html)                |
|   Organization name    |`VMware`                     |
|License         |`Apache lisence 2.0`| 
|Open/Proprietary				|Open source project
| Brief Description|Each Velero operation is a custom resource, defined with a Kubernetes [Custom Resource Definition (CRD)](https://kubernetes.io/docs/concepts/api-extension/custom-resources/#customresourcedefinitions) and stored in [etcd](https://github.com/coreos/etcd). Velero also includes controllers that process the custom resources to perform backups, restores, and all related operations. Velero is ideal for the disaster recovery use case, as well as for snapshotting your application state, prior to performing system operations on your cluster, like upgrades							





## Project details

**KEY FEATURES:**
>Back up your cluster and restore it in case of loss.

>Recover from disaster.

>Copy cluster resources to other clusters.

>Replicate your production environment to create development and testing environments.

>Take a snapshot of your application’s state before upgrading a cluster.

>Backup your Kubernetes resources and volumes for an entire cluster, or part of a cluster by using namespaces or label selectors.

>Set schedules to automatically kickoff backups at recurring intervals.

>Configure pre and post-backup hooks to perform custom operations before and after Velero backups.



**ARCHITECTURE:**

![Velero backup/restore for K8s Stateful Applications managed ...](https://miro.medium.com/max/1400/1*A9sBekZZTpfWeFBMXlUUbw.png)


**CURRENT USAGE:**
>Tata Communications 

>Infinity Systems Engineering

>CAESARS ENTERTAINMENT

>Acquia

>Planning & Analysis Inc


**TECHNICAL DETAILS:**

##### **Disaster Recovery:**

Reduces time to recovery in case of infrastructure loss, data corruption, and/or service outages.

##### **Data Migration:**

Enables cluster portability by easily migrating Kubernetes resources from one cluster to another​.

##### **Data Protection:**

Offers key data protection features such as scheduled backups, retention schedules, and pre or post-backup hooks for custom actions.
