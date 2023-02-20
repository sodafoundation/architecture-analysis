 #   Quobyte

### A  Easy enterprise storage

**Quobyte** storage software has its roots in an open source project that was created by several company founders as part of their PhD work. A fundamental aspect of this early research was a focus on distributed systems. Only after solving the challenges of building a fault-tolerant, fully distributed system—one that scales linearly and remains manageable even with thousands of nodes—did they turn their attention towards creating a powerful and equally scalable file system.

● **Quobyte** provides a smart storage software running on plain linux servers that offers a single storage system for block, object and share file system out-of-the box.
It  allows reconfiguration at runtime without storage service interruption

● It’s storage behavior is fully configurable via policies which also control automation and hardware independent configuration

Project summary
|Website |  [https://www.quobyte.com/](https://www.quobyte.com/)
|----------------|-------------------------------|-----------------------------|
|**Organization**| **Quobyte**            |
|**License**     |**other****(GPLv2 or LGPLv3+)**            |
|**Open/proprietary**|**Open source**|
|**Source Path**|https://github.com/quobyte


# Project Detail

## 1.Key Features

 ● **Simplicity by Design**

Unlike the traditional enterprise storage you are used to, Quobyte was designed with simplicity in mind.

As a 100% software storage solution, you can easily download and install Quobyte in mere minutes.
● **Significantly reduce storage cost**

Quobyte is a scale-out, distributed parallel file system that helps you slash storage costs – both capex and opex – while also providing exceptional agility and flexibility.
● **Deploy anywhere** Quobyte is the only 100% software storage that you can easily download and install on any x86 server, public clouds, or Kubernetes. That’s truly real software-defined storage.
● **Scalable performance**

A Quobyte cluster scales linearly so you can always add more hardware to get more performance – and capacity, without diminishing returns.
●**Unconditional simplicity**  Storage doesn’t need to be complicated. That’s why unlike traditional software-defined storage solutions Quobyte is simple – from installation to production.

● Software-only solution (zero hardware or kernel dependencies maximize compatibility)

● Self-monitoring and self-healing (manage large clusters with minimal staff)

● High IOPs, low-latency client (also provides seamless failover)

●  Policy-based data management and placement

●  Volume Mirroring (perfect for disaster recovery, or as a backup source)

●Runtime reconfigurable (eliminates downtime, changes on the fly)

●  Simple, scripted cluster installation (up and running in less than an hour)

●  Compatible with any modern Linux distro

● Supports POSIX, NFS, S3, SMB, and Hadoop file access methods (one platform for all workloads)

● Works with HDDs, SSDs, and NVMe devices

## 2.Architecture: 
Scalable Performance Scale-up storage systems of the past invariably hit performance roadblocks, but so did early scale-out architectures.That's why Quobyte engineers spent years designing a system free of scaling constraints. The company's deepdistributed systems knowledge led to the creation of a non-blocking design that enables Quobyte’s highly optimizedstorage to deliver high throughput with very low latency  The architecture excels at handling metadataheavy workloads like those found in EDA, media & entertainment, the life sciences, and financial modeling.When accessing the Quobyte software distributed parallel file system, the native client can issue parallel reads andwrites to all nodes of the storage cluster a key factor in delivering stellar performance. In contrast, a typical NFS client requires an extra network hop and only connects to a single NFS service or controller, greatly limiting its performance envelope.

This “n-to-m” parallel communication is done via Quobyte’s lightweight RPCs, and clients take advantage of all storage devices in the cluster unless restricted by a placement policy. As more nodes are added to the cluster, the performance increases linearly. Quobyte customers need not worry about “hot-filer” problem**<

 ## 3.Current usage 
 Quobyte’s smart software storage is used by enterprise to store the  software -defined data.  It is a software-defined storage(SDS) used for Video Surveillance There are multiple companies like- 3vGeomatics, Airbus, Yahoo Japan etc,  who use their  services  It is a software-defined storage(SDS) used for Video Surveillance

## 4.Technical Details
 1.[Hybrid cloud support](https://www.quobyte.com/storage-for/video-surveillance/) [](https://www.quobyte.com/storage-for/video-surveillance/)[](https://www.quobyte.com/storage-for/video-surveillance/)
 
 Quobyte runs on-prem or in the cloud. With  tools like volume mirroring and the data mover you can sync clusters in different data centers, including in the cloud

2.[Optimized for AI/ML workload](https://www.quobyte.com/storage-for/video-surveillance/)[**s**](https://www.quobyte.com/storage-for/video-surveillance/)  

Quobyte helps you maximize your investment in GPUs: scale-out performance for low latency at scale, optimal handling of large data files, and a native TensorFlow plugin.

3.Unified Storage

● Traditional storage systems have led to storage server sprawl problems. These aging, monolithic system architectures lead to IT infrastructures built around data silos, preventing easy accessibility between users and applications.

● Quobyte’s unified approach to storage empowers admins to deliver performance, capacity, and scalability from a single storage infrastructure.



4.Device Tags

Device tags are simple text strings that administrators can assign. The system automatically assigns HDD and SSD tags based on a device's type. But administrators can add additional tags to enable very sophisticated placement strategies.



5.Client Software

● Quobyte client software is available for Mac, Windows, and most Linux variants. When accessing Quobyte storage — a parallel file system — the client can communicate with ALL nodes in the cluster.

●  A typical NFS client requires an extra network hop and only connects to a single NFS service or controller, greatly limiting its performance envelope.

● Extensive client-side performance and monitoring metrics are also collected by the client and can be viewed via the web console. The client requires no local configuration, making rollout out to thousands of nodes easy.

6.Automated Monitoring

Numerous aspects of the Quobyte storage cluster are continuously monitored for proper behavior. If something drifts beyond reasonable presets, an automatic alert is sent to the web console, and an optional email alert can be dispatched.

7.Performance Monitoring

● Understanding and predicting storage needs is difficult, especially when there are many workloads and users hitting the system. Quobyte software provides tools to better understand what is going on in the system, to find “troublemakers” and to identify potential and future performance and capacity before they turn into a problem.

● The software offer two methods for keeping tabs on the cluster to make sure it's running as intended. Built-in real-time analytics let administrators pinpoint the top IOPs, throughput, and metadata consumers. Long-term trends can be studied by exporting data for external analysis.

**Other information**  
**360 Security -**
 Provides holistic data protection  End-to-end AES encryption (In transit / at rest / untrusted storage system)  Selective TLS support, e.g. between data centers  Access Keys for the file system  X.509 certificates  Event stream (metadata, file access)



**Top  Alternatives to Quobyte** 
1.AWS  
2.IDrive Online Backup  
3.SuiteDash  
4.Google Cloud Storage 
5.Boomi  
6.Akeneo PIM 
7.Aws s3  
8.Azure Blob Storage
9.LucidLink

**Link:-** 1.[https://www.quobyte.com/](https://www.quobyte.com/)  2.[https://www.g2.com/products/quobyte/competitors/alternatives](https://www.g2.com/products/quobyte/competitors/alternatives) 3.[https://wiki.openstack.org/wiki/Quobyte](https://wiki.openstack.org/wiki/Quobyte)
