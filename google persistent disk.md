 
![Multi-Writer File Storage on GKE. Coursemology needs a shared file system…  | by LH Fong | ESTL Lab Notes](https://miro.medium.com/max/256/1*U7-ShLiSRGLo01Bek5mSkw.png)


**GOOGLE PERSISTENT DISK**
**INTRODUCTION:**
 Persistent Disk_ is _Google's_ local durable storage service, fully integrated with _Google_ Cloud products, Compute Engine and _Google_ Kubernetes Engine.
*Persistent Disks are designed for durability. We automatically store your data redundantly to ensure the highest level of data integrity.*
##
**PROJECT SUMMARY:**
 **WEBSITE**: (https://cloud.google.com/persistent-disk)    
                       (https://tutorialsdojo.com/google-cloud-storage-vs-persistent-disks-vs-local-ssd-vs-cloud-filestore/)
    **Foundation Name**:  *Google*.
    **LICIENCE**:[https://github.com/mcuadros/gce-docker/blob/master/LICENSE]
    **OPEN PROPRIETOR**:*NOT OPEN SOURCE*
     **BREIF DISCRIPTION**:*Reliable, high-performance block storage for virtual machine instances. Enterprise scale, limitless flexibility, and competitive price for performance.*
 ##

**Key Features**

 - High-performance block storage for any workload.
 - Durability and availability that keep  your business running.
 - Automatic security and encryption.
 - Decoupled compute and storage.
 - Persistent Disk performance scales with the size of the disk and with the number of vCPUs on your VM instance. 
 ##

### ARCHITECTURE:
![Design considerations for resilient workloads with regional persistent disks  | Compute Engine Documentation | Google Cloud](https://cloud.google.com/static/architecture/images/design-considerations-for-resilient-workloads-with-regional-persistent-disks-stateful-two-zones.svg)
##
**_CURRENT USAGE :_**

 - *PAYPAL*
 - *TWITTER*
 - *AIRBUS*
 - *BLUE APRON*
 - *PROCTER AND GAMBLE*
 ##
**TECHNICAL DETAILS:**
 
 
 - *Block storage that is easy to deploy and scale*
 - *Flexibility that comes with no downtime*
 - *Industry-leading price and performance*
 ##
 **Other information** **:**
### Block storage—Persistent Disk and Local SSD
[Persistent Disk](https://cloud.google.com/persistent-disk)  and  [Local SSD](https://cloud.google.com/local-ssd)  are block storage options. They are integrated with Compute Engine virtual machines and Kubernetes Engine. With block storage, files are split into evenly sized blocks of data, each with its own address but with no additional information (metadata) to provide more context for what that block of data is. Block storage can be directly accessed by the operating system as a mounted drive volume.

Persistent Disk is a block store for VMs that offers a range of latency and performance options. I have covered persistent disk in detail in  [this](https://cloud.google.com/blog/topics/developers-practitioners/google-cloud-block-storage-options-cheat-sheet?utm_campaign=CDR_pve_Storage_blockstorageoptionsc_041521)  article. The use cases of Persistent Disk include disks for VMs and shared read-only data across multiple VMs. It is also used for rapid, durable backups of running VMs. Because of the high-performance options available, Persistent Disk is also a good storage option for databases.

[Local SSD](https://cloud.google.com/local-ssd)  is also block storage but it is ephemeral in nature, and therefore typically used for stateless workloads that require the lowest available latencies. The use cases include flash optimized databases, host caching layers for analytics, or scratch disks for any application, as well as scale out analytics and media rendering.
##



















