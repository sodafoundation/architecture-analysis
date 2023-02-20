# AZURE DISK STORAGE
## INTRODUCTION
Azure Disk Storage is a Microsoft Azure service that provides disk storage for Azure virtual machines (VMs). It includes Azure managed disks, which provide various disk types and sizes for different workloads, and Azure blob storage, which is a scalable object storage service for unstructured data. Azure Disk Storage allows for the creation and management of VMs, and provides the storage for the operating system and data disks for the VMs. It also provides storage for backups, images, and snapshots of VMs. Additionally, it offers features such as encryption, data replication, and snapshots for data protection and disaster recovery.

## AZURE DISK STORAGE TYPES

Azure Disk Storage offers several types of disks, each with different performance characteristics and pricing options:

 - Azure Standard SSD: A cost-effective SSD option for dev/test scenarios and other lower-performance 			       workloads.
- Azure Premium SSD: A high-performance SSD option for production workloads that require consistent, low-latency disk I/O.
- Azure Ultra Disk Storage: A newer high-performance option that is optimised for IOPS-intensive and throughput-intensive workloads, such as large SQL and NoSQL databases.
- Azure HDD: A cost-effective option for infrequently accessed data and less-performance sensitive workloads.
- Azure Standard NVMe: A cost-effective option for dev/test scenarios and other lower-performance workloads with NVMe disks.

It's important to note that disk types can be also differentiated by capacity and IOPS/Throughput limits and cost.

## PROJECT SUMMARY


|**WEBSITE**	 |https://azure.microsoft.com/enus/services/storage/disks/| 						
|----------------|------------------------------
|**ORGANIZATION**|**MICROSOFT**            
|**LICENCES**         |**NILL**        
|**BRIEF DESCRIPTION**|**Azure Disk Storage is a Microsoft Azure storage service that provides managed, highly available and scalable disk storage for Virtual Machines (VMs) and applications. It offers various options for disk storage such as : Azure managed disks, Unmanaged disks, Premium SSD, Standard SSD, Standard HDD**                        |

## PROJECT DETAILS

 - ## KEY FEATURES

	Some key features of Azure Disk Storage include:

	- Scalability: Azure Disk Storage can scale up or down as needed to meet the storage requirements of your workloads.
	- High availability: Azure Disk Storage provides built-in redundancy and replication options to ensure that your data is always available, even in the event of a disk failure or other disruption.
	- Performance: Azure Disk Storage offers a range of disk types, including Premium SSD and Standard SSD, that are optimised for different performance needs.
	- Security: Azure Disk Storage supports encryption at rest, which helps to protect your data from unauthorised access.
	- Data protection: Azure Disk Storage offers features such as snapshots and incremental backups to help protect your data and ensure that it can be easily recovered in the event of a failure or other disruption.
	- Flexibility: Azure Disk Storage supports various options for disk storage such as Azure managed disks, Unmanaged disks, and more.
	- Integration: Azure Disk Storage can be easily integrated with other Azure services, such as Azure Virtual Machines and Azure Backup, to provide a complete and seamless solution for your storage needs.

 - ## ARCHITECTURE

	Azure Disk Storage is a service provided by Microsoft Azure that allows users to create and manage virtual hard disks (VHDs) in the cloud. These VHDs can be used as the storage for Azure Virtual Machines (VMs), or they can be used to create storage accounts that can be accessed over the network using the SMB or NFS protocols.

![AZURE DISK STORAGE ARCHITECTURE](https://www.linkpicture.com/q/maxresdefault_139.jpg)
	
The architecture of Azure Disk Storage is based on a distributed storage system that uses a combination of locally redundant storage (LRS) and geo-redundant storage (GRS) to provide high availability and durability. LRS stores multiple copies of data within a single datacenter, while GRS stores multiple copies of data across multiple datacenters in different regions.Azure Disk Storage also uses a hierarchical namespace, which allows users to organise their data into containers called "storage accounts." Each storage account can contain multiple VHDs, and each VHD can be divided into multiple data blocks.

	
In addition, Azure Disk Storage supports various disk types such as standard disk, premium disk, and ultra disk. Each disk type has different performance characteristics and pricing model.

	
Overall, Azure Disk Storage provides a robust and reliable storage solution for Azure customers, with built-in redundancy, scalability, and security features.

## CURRENT USAGE

Azure Disk Storage is widely used in a variety of scenarios and workloads, here are some examples of current usage of Azure Disk Storage:

- Virtual Machine Storage: One of the most common uses of Azure Disk Storage is as the storage for Azure Virtual Machines. Azure Disk Storage is used to create and manage virtual hard disks (VHDs) that can be attached to VMs as the primary storage.

- File Storage: Azure Disk Storage can also be used to create file shares that can be accessed over the network using the SMB or NFS protocols. This allows users to store and share files in the cloud, and access them from anywhere.

- Backup and Disaster Recovery: Azure Disk Storage can be used as the storage for Azure Backup and Azure Site Recovery, which allows users to create and manage backups and disaster recovery scenarios for their VMs and other workloads.

- Databases: Azure Disk Storage can be used as the storage for Azure SQL Database, Azure Cosmos DB and other databases that run on Azure, providing scalable, reliable and secure storage option.

- Big Data and Analytics: Azure Disk Storage can also be used to store and process large amounts of data for big data and analytics workloads using Azure Data Lake Storage, Azure Data Factory, Azure Stream Analytics, and other big data services on Azure.

- Container Storage: Azure Disk Storage can be used to store persistent data for container workloads running on Azure Kubernetes Service (AKS) or other container orchestration platform on Azure.

These are some examples of current usages of Azure Disk Storage, but the possibilities are endless, as the service is versatile and can adapt to many different workloads and use cases.

## TECHNICAL DETAILS

- ## SCALABILITY

	Azure Disk Storage is highly scalable and can handle a wide range of storage and performance requirements. Here are some details on how Azure Disk Storage scales:
	- Disk Scale: Azure Disk Storage supports disk capacity up to 4 TiB for standard and premium disk and 64 TiB for Ultra Disk. You can easily increase or decrease the size of the disk as per your requirement.
	- IOPS and Throughput Scale: The IOPS and Throughput of Azure Disk Storage can be scaled up or down depending on the type of disk you choose. Standard disk can deliver up to 500 IOPS and 60MB/s throughput, Premium disk can deliver up to 100,000 IOPS and 2,000 MB/s throughput and Ultra disk can deliver up to 160,000 IOPS and 2,000 MB/s throughput.
	- Scale-out: Azure Disk Storage can be easily scaled out to meet the needs of large, high-performance workloads by attaching multiple disks to a single virtual machine or by using Azure's storage solutions such as Azure Blob Storage, Azure Data Lake Storage, or Azure Files.
	- Scale-up: Azure Disk Storage also provides options for Scale-up, you can easily scale up the disk size and performance.
	- Automatic Scaling: You can also use Azure's automatic scaling features to automatically scale the number of VMs or the size of disks based on usage patterns, this will help you optimise cost and performance of the storage.
	Overall, Azure Disk Storage is designed to scale to meet the storage and performance needs of a wide variety of workloads, from small-scale development and testing scenarios to large-scale production workloads.

- ## PERFORMANCE

	Azure Disk Storage provides high performance for a variety of workloads, depending on the type of disk chosen. Here are some details on the performance characteristics of Azure Disk Storage:

	- IOPS and Throughput: The IOPS and Throughput of Azure Disk Storage depend on the type of disk, Standard disk can deliver up to 500 IOPS and 60MB/s throughput, Premium disk can deliver up to 100,000 IOPS and 2,000 MB/s throughput and Ultra disk can deliver up to 160,000 IOPS and 2,000 MB/s throughput.

	- Latency: Azure Disk Storage provides low latency access to data, which is important for workloads that require fast data access, such as databases and virtual machines.

	- Caching: Azure Disk Storage uses caching to improve performance. The Premium and Ultra disks have built-in caching that accelerates the read performance.
	- Redundancy: Azure Disk Storage uses a combination of locally redundant storage (LRS) and geo-redundant storage (GRS) to provide high availability and durability. This helps ensure that data is always available and that there is minimal impact on performance due to failures or maintenance operations.

	- Optimised for workloads: Azure Disk Storage is optimised for different types of workloads, such as transactional, big data, and high-performance computing. This allows you to choose the disk type that is best suited for your workload and provides the best performance.

	- Access: Azure Disk Storage can be accessed over the network using the SMB or NFS protocols, or via Azure REST API. This allows you to access your data from anywhere with high performance.

	In general, Azure Disk Storage provides high performance for a wide range of workloads, with low latency and high throughput. It also provides options for caching and redundancy to improve performance and ensure high availability.

- ## STORAGE TYPES

	Azure Disk Storage supports three types of storage: Standard, Premium and Ultra Disk. Each type of storage has different performance characteristics and pricing.

- ## REDUNDANCY

	Azure Disk Storage uses a combination of locally redundant storage (LRS) and geo-redundant storage (GRS) to provide high availability and durability. LRS stores multiple copies of data within a single datacenter, while GRS stores multiple copies of data across multiple datacenters in different regions.

- ## CAPACITY

	Azure Disk Storage supports disk capacity up to 4 TiB for standard and premium disk and 64 TiB for Ultra Disk.

- ## IOPS AND THROUGHPUT

	The IOPS and Throughput of Azure Disk Storage depend on the type of disk, Standard disk can deliver up to 500 IOPS and 60MB/s throughput, Premium disk can deliver up to 100,000 IOPS and 2,000 MB/s throughput and Ultra disk can deliver up to 160,000 IOPS and 2,000 MB/s throughput.

- ## SNAPSHOTS

	Azure Disk Storage supports the ability to create point-in-time snapshots of disks. These snapshots can be used to create new disks, or to roll back a disk to a previous state.

- ## ENCRYPTION

	Azure Disk Storage supports disk-level encryption using Azure Key Vault and Azure Disk Encryption.

- ## ACCESS

	Azure Disk Storage can be accessed over the network using the SMB or NFS protocols, or via Azure REST API.

- ## INTEGRATION

	Azure Disk Storage can be integrated with other Azure services such as Azure Virtual Machines, Azure Backup, Azure Site Recovery, Azure SQL Database, Azure Cosmos DB, Azure Data Lake Storage, Azure Data Factory, Azure Stream Analytics, and Azure Kubernetes Service.
	
## **REFERENCE**

1.  [**https://azure.microsoft.com/en-us/products/storage/disks**](https://azure.microsoft.com/en-us/products/storage/disks)
2.  [**https://www.javatpoint.com/azure-disk-storage**](https://www.javatpoint.com/azure-disk-storage)
3.  [**https://www.youtube.com/watch?v=ddhOU0l01-w**](https://www.youtube.com/watch?v=ddhOU0l01-w)
