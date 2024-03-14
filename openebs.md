

**OpenEBS**

**Indroduction**

_OpenEBS is a 100% Open Source CNCF project with MayaData and the community. Prominent users include Arista, Optoro, Orange, Comcast, and the CNCF itself. It builds on Kubernetes to enable Stateful applications to easily access Dynamic Local PVs or Replicated PVs._

**Project summary**
| website | https://openebs.io/ |
|--|--|
| organization/foundation name | CNCF |
| license | Apache License 2.0 |
| Open proprietary | Open source |
| Source path| https://github.com/openebs/openebs.git 
|Brief descriotion | OpenEBS helps Developers and Platform SREs easily deploy Kubernetes Stateful Workloads that require fast and highly reliable container attached storage. OpenEBS turns any storage available on the Kubernetes worker nodes into local or distributed Kubernetes Persistent Volumes._ |



























**Project Details**

Declarative Provisioning of Container Attached Storage

• Durability and High availability using synchronous data replication Data Protection via Snapshots and

clones

Disaster Recovery via Backup and restore

• Observability using Prometheus metrics and Grafana dashboard

**Architecture**
![enter image description here](https://miro.medium.com/v2/resize:fit:1100/format:webp/1*wcw8D3FP2O2B-2WBCsumLA.png)

**Current usage**

CAE

Baxter

Index exchange

Data core

Walmart

Zeta association

**Technical details**

OpenEBS can create Persistent Volumes using raw block devices or partitions, or using sub-directories on Hostpaths or by using LVM,ZFS, or sparse files.

The local volumes are directly mounted into the Stateful Pod, without any added overhead from OpenEBS in the data path, decreasing latency.

OpenEBS provides additional tooling for Local Volumes for monitoring, backup/restore, disaster recovery, snapshots when backed by ZFS or LVM, capacity based scheduling, and more.

**Other information**

OpenEBS helps Developers and Platform SREs easily deploy Kubernetes Stateful Workloads that require fast and highly reliable container attached storage. OpenEBS turns any storage available on the Kubernetes worker nodes into local or distributed Kubernetes Persistent Volumes.

