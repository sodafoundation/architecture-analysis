# Scality RING


Scality RING is software to create an object storage system on servers with attached storage.
It is used in a scale-out, distributed shared-nothing architecture.
The RING uses a distributed hash table with key-value pairs.
It runs on standard x86 servers and is designed for multi-application environments
The RING provides scaling beyond petabytes

| Website |	https://spring.io/projects/spring-framework|
| :--------- | :--------------------------------------: |
| **License** |	Hewlett Packard Enterprise |
| **Open/Proprietary** |	Proprietary |
| **Source Path** |	NA |
| **Brief description** |	The RING enables many different applications, including aggregating multiple applications into a single storage environment |










## Key Features

⦁	**High scalability and performance**: Scality Ring can scale to petabytes of data and billions of objects, while delivering high throughput and low latency.
⦁	**Multi-cloud support**: Scality Ring can run on-premises, in public clouds, or in a hybrid configuration that spans both.
⦁	**Data durability and availability**: Scality Ring uses erasure coding and data replication to ensure data is always available, even in the event of hardware failures.
⦁	**Geo-distribution**: Scality Ring can be deployed across multiple geographic locations, providing a highly available and resilient storage infrastructure.
⦁	**S3 compatibility**: Scality Ring is compatible with the Amazon S3 API, making it easy to integrate with existing S3-compatible applications and tools.
⦁	**Data security**: Scality Ring provides features such as encryption and access controls to ensure data is protected from unauthorized access.
⦁	**Easy management**: Scality Ring includes a web-based GUI and API for easy management and monitoring of the storage environment.

![Scality RING architecture](http://lifestore.typepad.com/.a/6a013480ec4aff970c0134896e52ad970c-pi)


Scality Ring is a distributed object storage software that uses a shared-nothing architecture, meaning that each node in the cluster operates independently and does not rely on shared storage or metadata servers. Here are some key aspects of Scality Ring's architecture:

⦁	**Ring**: The "ring" is the core component of the Scality Ring architecture. It is a logical representation of the entire storage cluster and consists of multiple nodes (servers), each of which runs the Scality Ring software. The ring manages data distribution, metadata, and cluster membership.

⦁	**Nodes**: Each node in the Scality Ring cluster is a self-contained storage server that communicates with other nodes in the cluster over the network. Each node can store and retrieve data, as well as handle metadata operations.

⦁	**Objects**: Scality Ring stores data as objects, each of which is assigned a unique identifier (object key) and stored across multiple nodes for redundancy and performance. The object data is divided into smaller segments, and each segment is distributed across the nodes in the cluster.

⦁	**Erasure coding**: Scality Ring uses erasure coding to protect data against hardware failures. Each object segment is encoded and distributed across multiple nodes, so that if one or more nodes fail, the data can be reconstructed from the remaining nodes.

⦁	**Data access**: Scality Ring provides a RESTful API that supports the S3 protocol, enabling applications to access data stored in the cluster. Clients can also access the data using a file system interface or a native API.
Overall, the Scality Ring architecture is designed to provide high scalability, durability, and performance for large-scale object storage environments.



## Current Usage

⦁	AP-HM’s Private
⦁	BIGLOBE
⦁	Blooberg Media Group
⦁	Catapult
⦁	Centric
⦁	Chu Dijon Bourgogne
⦁	Com Hem
⦁	Comcast
⦁	Dailymotion
⦁	Deluxe
⦁	Dmm .com
⦁	Kadokawa Connected
⦁	MediaHub
⦁	Logitech
⦁	Tottenham Hotspurs
⦁	Lake Solutions
⦁	National Library of Scotland
⦁	University Hospital Basel


## Technical Details

⦁	**Connectors**: Interfaces to the outside/applications
--->	Zimbra/Dovecot mail system connector
--->	Native web service interface(REST API)
--->	Amazon S3 compatible interface
--->	C API for custom connectors
⦁	**Storage Nodes**:Building block of the pool
 ---> Provide both computing and storage resources to the cloud
 --->  Fully distributed addressing using peer to peer
⦁	**IO Daemon**:Low level storage hypervisor
--->	High performance IO operations
--->Can use different types of storage hierarchically, for example SSD, local  disks(SAS or SATA) and ISCSI at the same time


## Other Information



⦁	Scality RING, an Object Storage platform(built for unstructured content)
⦁	Cost effectiveness: Delivering on economic and operational promises
⦁	Ring allows for flexible architectures(Fits existing deployment model and allows non- disruptive growth and architecture evolution)
--->	Availability(99,99% to 99,999999..%)
--->	Performance(Tiering, Cachin, GEO redundancy)
--->	Scalability to ExaBytes platforms
--->	Migration from any kind of platform
--->	No hardware locked in
⦁	Scality has team experienced in operating systems at scale in this industry
⦁	Ring is proven in production today(Mail environment platforms, Multiple cloud storage providers)
⦁	There are several other object storage solutions available on the market that can be compared to Scality Ring. Some comparisons of Scality Ring to two other popular object storage solutions are Ceph and Amazon S3
⦁	There are several alternatives to Scality Ring that offer similar capabilities for object storage. Here are a few examples 
--->	**Ceph**: Ceph is an open-source distributed object storage software that provides high scalability, data durability, and multi-cloud support. It uses a similar architecture to Scality Ring, storing data as objects and using erasure coding for data protection. 

--->	**SwiftStack**: SwiftStack is a commercial object storage solution that provides a high-performance, scalable, and easy-to-manage storage infrastructure. It uses a ring-based architecture similar to Scality Ring, but includes additional features such as data analytics, policy management, and a unified namespace. 

--->	**MinIO**: MinIO is an open-source object storage software that provides high performance, scalability, and multi-cloud support. It uses an architecture based on Kubernetes and Docker, and supports Amazon S3-compatible APIs. 

--->	**OpenIO**: OpenIO is an open-source distributed object storage software that provides high scalability, performance, and data durability. It uses a ring-based architecture similar to Scality Ring, and includes features such as erasure coding, multi-tenancy, and data replication across multiple data centers.



