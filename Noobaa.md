




# **Introduction**

Noobaa is a data service for cloud environments, provides S-3 object store interface with Flexible tiering, mirroring, and it spreads placement policies, over any storage resources that allows GET/PUT including S3, GCS, Azure Blob, file systems etc.

   ⮚  Provides data administration by connecting to any if the storage silos from private or public clouds.

   ⮚ Provides single scalable data service.

   ⮚ Allows full control over data placements.

Early-stage startup NooBaa is situated in Tel Aviv, Israel, and creates software for managing data storage services in hybrid and multicloud systems. It has created software that abstracts the underlying cloud storage architecture and offers applications a single interface as well as a variety of data services, such as tiering, migration, or moving data between clouds.

### **Project Summary**

|                     |                      |
|-------------------------------|-----------------------------|
|Website|   [https://www.noobaa.io/](https://www.noobaa.io/)              |
|Organization Name         |Red Hat Software                       |
|Licensing         |Apache License 2.0|
|Open/proprietary         |Open Source Software|
|Source Path        |[https://github.com/noobaa/noobaa-operator.git](https://github.com/noobaa/noobaa-operator.git)|
|Brief Description        |Noobaa is an object data service for Hybird and Multi-cloud environments. It runs in Kubernetes and provides an S3 object store service to clients both inside and outside the cluster.|




### **Project Details**

**Key Features**

● **Cloud-Native Storage Orchestration Anywhere**

Regardless of the backing store, NooBaa's Kubernetes Operator delivers the NooBaa core to provide a self-managed, self-scaling, and self-healing object storage architecture. It facilitates the automation of resource management, monitoring, scaling, upgrading, migrating, scaling up, and bootstrapping across many public cloud providers.

● **Operational Agility**

By giving operation teams the freedom to employ S3 compatible backend storage and the cloud-native S3 API for a consistent service, NooBaa relieves them of the burden of long-term planning. NooBaa eliminates the need for difficult storage decisions and allows operations to alter the location of the real data while still providing continuous support to the application throughout its lifecycle.

● **Data Protection**

NooBaa enforces, by default, data encryption with a separation between keys management and the data, providing ultimate data protection on-premise and in the cloud. NooBaa also supports an optional Server-Side Encryption which adds an additional security layer on top of the default data protection. NooBaa tags each object with a fingerprint, and periodically validates the fingerprint of its data and rebuilds it if suspected as untrusted.

● **Data Efficiency**

NooBaa reduces the amount of network traffic needed for replication, mirroring, rebuilding, and other tasks. Additionally, NooBaa splits each item into a number of pieces to enable effective data mirrored streams.

● **Modern UI**

The NooBaa Operator for Kubernetes offers an advanced configuration for event-driven triggers for data cleansing, data migration, and other functions, as well as a contemporary UI for monitoring data consumption across applications. Prometheus metrics are also exported by the NooBaa Operator for use with third-party dashboards like Grafana.

### **Architecture**
![](https://files.speakerdeck.com/presentations/d445c56f66004040bd80950569eaa9fc/slide_25.jpg)



![](https://assets.openshift.com/hubfs/Imported_Blog_Media/image2-52-1.png)





  

This image shows the system and the three layers are described so that F to an API is here in front facing the application and in the backend we can see backing stores of different types could be on firm, different technologies anything consumable who either  the cloud API such as the S3 API above google cloud storage or just plain drives using volumes and file systems on top. Multi-cloud buckets taking a buck or spreading it or mirroring over multiple clouds. Multi-side buckets is between two data centers.

### **Current Usage**

Users can access and manage data stored in several cloud storage systems, including Amazon S3, Microsoft Azure, and Google Cloud Storage, using the open-source, multi-cloud Noobaa platform. Organizations who need to manage data across numerous cloud providers or that want to employ different cloud storage services for different sorts of data are often the ones who use it.Organizations who need to manage data across numerous cloud providers or that want to employ different cloud storage services for different sorts of data are often the ones who use it. Any organisation or person, including companies, academics, and developers, who wishes to manage their data across several cloud storage services can utilise it.

General Motors are using Noobaa storage.

### **Technical Details**

|               Term |a.k.a                        |Description                        |
|----------------|-------------------------------|----------------------|
|Noobaa Core|“The Brain”           |           |
|Noobaa Endpoint        |“S3 Server”          |Stateless      |
|Noobaa Agent      |“The Daemon Storage”|Stateful for local FS volume,embeds endpoint|





By adding more storage nodes, Noobaa's distributed, object-based storage design enables it to scale horizontally. It employs a global namespace to provide data from all cloud providers as a single, unified view and a distributed metadata layer to track where data is kept.

Data tiering and migration are supported by Noobaa, enabling customers to shift data automatically between several cloud storage services based on parameters like data age, access frequency, or cost. By enabling customers to make snapshots, replicate, and backup data to other cloud providers, it also enables data protection and disaster recovery.

As a result of using Kubernetes as its orchestration platform, Noobaa can be set up on-premises, in the cloud, or in a hybrid setting. It may be coupled with other data management technologies, such as data lakes and databases, and it also supports S3, NFS, and SMB protocols.

Multi-tenancy, which Noobaa provides, enables several users and organisations to share the same infrastructure while maintaining the privacy and security of their respective data. Additionally, it provides role-based access management, which enables administrators to limit users' access to data in accordance with their roles and permissions.

### **Other Information**

**Comparison**

The following companies compete with Noobaa in the multi-cloud data management market:

●  A software-defined storage solution from NetApp called Cloud Volumes ONTAP enables users to manage data across numerous cloud service providers, including AWS, Azure, and Google Cloud. Similar to Noobaa, it offers capabilities like data tiering, data protection, and disaster recovery.

● A scale-out object storage platform called Cloudian HyperStore enables users to manage data across numerous cloud service providers, such as AWS, Azure, and Google Cloud. Similar to Noobaa, it offers capabilities like data tiering, data protection, and disaster recovery.

● With the aid of StorReduce, customers may manage data across numerous cloud service providers, including AWS, Azure, and Google Cloud. It offers attributes like data deduplication, compression, and data protection that Noobaa does not.

● CloudBerry Lab is a multi-cloud data management tool that lets users manage data across AWS, Azure, and Google Cloud, among other cloud service providers. Similar to Noobaa, it offers capabilities including data backup, data archiving, and disaster recovery.

Though they all seek to deliver a unified multi-cloud data management solution experience across several cloud providers, these solutions vary in the particular features and capabilities they offer. When selecting a multi-cloud solution, it's critical for enterprises to assess their unique demands and requirements.

### **Other Information**

A software-driven data storage gateway called NooBaa is intended for storage administrators and experts. It enables users to manage the anonymization of DICOM records, accelerate inline established deduplication and compression, streamline access to data repositories, distribute catalogue to multiple distribution channels, link anonymized record with the actual patient information, archive data and manage lifecycle flow policies automatically, and transfer data between sites and the cloud based on access patterns and data policies.

### **Reference Links**

1. [www.redhat.com](http://www.redhat.com)

2. [www.google.com](http://www.google.com)

3. [www.github.com](http://www.github.com)

4. [www.noobaa.io](http://www.noobaa.io)
