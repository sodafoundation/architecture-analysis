                                         **

## OpenEBS



  
  

**Indroduction**

  

OpenEBS is a 100% Open Source CNCF project with MayaData and the community. Prominent users include Arista, Optoro, Orange, Comcast, and the CNCF itself. It builds on Kubernetes to enable Stateful applications to easily access Dynamic Local PVs or Replicated PVs.

  

**Project summary**
|website  |https://openebs.io/ |
|--|--|
| **organization/foundation name** |CNCF  |
| **license** | Apache License 2.0 |
|**Open proprietary**|Kubernetes|
| **Source path**(if open source) | https://github.com/openebs/openebs.git |
| Brief description | OpenEBS helps Developers and Platform SREs easily deploy Kubernetes Stateful Workloads that require fast and highly reliable container attached storage. OpenEBS turns any storage available on the Kubernetes worker nodes into local or distributed Kubernetes Persistent Volumes. |


  


**Project Details**

  

Declarative Provisioning of Container Attached Storage

  

• Durability and High availability using synchronous data replication Data Protection via Snapshots and

  

clones

  

Disaster Recovery via Backup and restore

  

• Observability using Prometheus metrics and Grafana dashboard

  

**Architecture**

![](https://lh3.googleusercontent.com/VZsPcHfHq_7cr_VRHW1ale17alrbR0_oR2_6w0xCp5gUH6nYhrci_OwaTdmf63xiNjPymvYrqW8f90mTO-Zufv1qqecT2DMdfmUFik4Vgfctx8KwFtALI4XAEEIbkcAf6kM7hTdW1DKa0xRDRxkTTj8)

  
  
  
  

**Current usage**

-   CAE
    
-   Baxter
    
-   Index exchange
    
-   Data core
    
-   Walmart
    
-   Zeta association
    

  

**Technical details**

-   OpenEBS can create Persistent Volumes using raw block devices or partitions, or using sub-directories on Host-paths or by using LVM,ZFS, or sparse files.
    
-   The local volumes are directly mounted into the Stateful Pod, without any added overhead from OpenEBS in the data path, decreasing latency.
    
-   OpenEBS provides additional tooling for Local Volumes for monitoring, backup/restore, disaster recovery, snapshots when backed by ZFS or LVM, capacity based scheduling, and more.
    

**Other information**

OpenEBS helps Developers and Platform SREs easily deploy Kubernetes Stateful Workloads that require fast and highly reliable container attached storage. OpenEBS turns any storage available on the Kubernetes worker nodes into local or distributed Kubernetes Persistent Volumes.
