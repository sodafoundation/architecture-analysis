# Multi-Cloud feature enhancements

**Author(s)**: [Shufang Zeng](https://github.com/sfzeng), [Neelam Gupta](https://github.com/neelamgupta1491), [Ashit Kumar](https://github.com/kumarashit) 

## Summary

OpenSDS provides multi-cloud management platform. The goal of multi-cloud is to enable data autonomy for multi-cloud environments. Currently OpenSDS multi-cloud (Gelato) provides multiple features for Object Data Migration, Lifecycle and Management across multiple clouds like AWS, Azure, GCP, Huawei, IBM. Additionally, Gelato need to increase the multi-cloud supportability matrix. In the next release, OpenSDS will add some key features, add new on-premise Object storage i.e. YIG (Yet another Index Gateway) and integrate with YIG team to enhance the multi-cloud features of OpenSDS.
The purpose of this design is to add new features, analyse and facilitate the OpenSDS multi-cloud (Gelato) integration with YIG.


One of the key components of this is 'S3 service'. S3 service handles S3 API requests for communicating with Object Storage backends. OpenSDS multi-cloud API gateway should be compatible with any S3 compatible API service. This S3 service should provide or should be flexible to provide all the multi-cloud features. [YIG](https://github.com/journeymidnight/yig ) is Object Storage Framework which is s3 compatible. YIG brings in multiple features which fits into the multi-cloud requirements. 

## Motivation

Currently OpenSDS multi-cloud comes with a set of feature and storage backends.

### Here is the list of features available in OpenSDS:
* Multi-cloud Management across public and private clouds (Data Migration, Lifecycle Management)
* Cloud Vendor Agnostic
* Policy based data mobility
* S3 based REST API
* Containerized deployment
* Metadata Management and monitoring
* Easy integration of backend storage adapters (on-prem or Cloud)

### List of Object Storage backends supported:
    * AWS
    * Azure
    * GCP
    * Huawei OBS
    * IBM COS
    * Ceph S3

### New features to be supported:
    * Encryption of Data at rest
    * Bucket Location
    * Versioning
    * CORS
    * ACL
    * Bucket Policy
    * Configurable buffered copy object
    * HEAD Bucket
    * HEAD Object

### New Object Storage Backend support: 
* YIG

#### Some of the key features of YIG:
YIG can avoid data movement and I/O drop down caused by scaling up of Ceph cluster 
With scale-out Ceph-cluster and ceph-libradosstripper APIs, YIG solves some of the key performance issues of Ceph
These features make YIG Ceph Pool an important on-premise Object Storage backend.

### Why YIG:
YIG brings in some of the features which can complement and enhance OpenSDS multi-cloud management platform. This also brings in opportunity for OpenSDS to be THE Platform for all multi-cloud management. YIG has some features which OpenSDS multi-cloud doesn't have. So YIG can help adding those features into OpenSDS multi-cloud.

 
#### How YIG can help in enhancing multi-cloud capabilities of Gelato:
YIG already supports following features for the YIG Ceph pool:

    * Bucket and Object ACL
    * Bucket policy
    * Copying Objects
    * Server-side encryption
    * Versioning
    * Post object

### Goals
This document will propose the feature enhancement to OpenSDS multi-cloud (Gelato). The new set of features are:

    * Encryption of Data at rest
    * Bucket Location
    * Versioning
    * CORS
    * ACL
    * Bucket Policy
    * Configurable buffered copy object
    * HEAD Bucket
    * HEAD Object

### Non-Goals
N/A

## Design Details
    
### Enhance multi-cloud (Gelato) and integrate with YIG

#### For this approach here is the high-level architecture:

![gelato-multicloud-yig](gelato-multicloud-yig.png?raw=true "Gelato Multicloud with YIG")


#### Expanding the YIG S3:
YIG uses TiDB for storing metadata. These metadata are:
    * Bucket information (includes ACL)
    * Object Metadata(ACL and contenttype)
    * Slice information of multipart
    * Scheduling
    
    YIG caches the metadata for improved performance. 'redis' is used for this
    
In current form, YIG supports Ceph as the Object storage. 
OpenSDS multicloud will leverage existing YIG code and extend it to build all the required features.
Multicloud S3 service flow will remain the same.
Here is the flow for S3 service:
    * Client issues an API request
    * API Gateway receives the request, authenticates and sends it to s3 server
    * S3 server handles the request. It processes the request on a bucket, updates metadata into DB and cache
    * S3 server will get backend information from backend service
    * S3 server will connect with the backend and do the required operations
    

### Data model impact

Gelato follows the database per service microservices architecture. This means that all the components will have their own database. Gelato microservices architecture currently provides the flexibility, per service, to have their own DB adapter and interact with the respective database for persistent storage.
With this release, Gelato will support MongoDB for services like Datamover, Dataflow and Backend. 
S3 service can support both TiDB and MongoDB. YIG project already comes with TiDB support, which can be leveraged.
Note: In future OpenSDS multi-cloud will be enhanced to support multiple DBs for all components based upon the Go Plugin arch.

#### TiDB:
TiDB ("Ti" stands for Titanium) is an open-source NewSQL database that supports Hybrid Transactional and Analytical Processing (HTAP) workloads. It is MySQL compatible and features horizontal scalability, strong consistency, and high availability.

#### MongoDB:
MongoDB is a document database designed for ease of development and scaling. 

Some comparisons:

|   | **MongoDB** | **TiDB** |
| --- | --- | --- |
| License | SSPL (Server Side Public License) | Apache 2.0 |
| Horizontal scalability | Yes | Yes (ease of use. Just add node) |
| Schema free | Yes | No  |
| Transactional support | Yes (with 4.0) | Yes |
| Replica sets/Fault tolerant | Yes (Performant for read. Not so good for writes) | Yes (Read-write performant. Scale-out replication) |
| Strong consistency | Yes (with 4.0 it has consistency across multiple documents. Prior it was only for single document transactions) | Yes |
| Consistency concept | Eventual as well Individual consistency (decided by writes) | Individual consistency |
| SQL Syntax support | No | Yes |
| Use case | Best suited for document stores | Suited for RDBMS |


#### Modified schema for adding new features
**Bucket**

| Column | Type | Nullable | Remarks |
| --- | --- | --- | --- |
| id | string | F |   |
| bucketname | string | F | User defined name |
| ownerid | string | F | Owner id  |
| ownername | string | F | Owner display name |
| cors | string | T | JSON |
| lifecycle | string  | T | JSON |
| acl | string | T | JSON |
| policy | string | T | JSON |
| createtime | uint64 | T | Epoch |
| usages | unint64 | F |   |
| versioning | string/int | T | Enable/Disable/Suspend |
| deleted | bool | T |   |
| defaultlocation | string | T |   |
| replicationconf | string | T | JSON |
| sseconf | string | T | JSON (Data at rest) |
| websiteconf | string | T | JSON |
| tier | int32 | T | Default 1(standard) |

**Object**

| Column | Type | Nullable | Remark |
| --- | --- | --- | --- |
| key | string | F | Object key |
| bucketid | string | F |   |
| tenantid | string | F | Object owner id |
| userid | string | F |   |
| location | string | T | Backendname |
| versionid | string | T | Random string |
| size | int64 | T |   |
| objectID | string | T | For backend of YIG Ceph:Random string used in ceph cluster.For other backends: the key in the backend related bucket. For example:aws\_bucket/obj1.jpg |
| lastmodifiedtime | uint64 | T | Epoch |
| etag | string | T | Object tags |
| contenttype | string | T |   |
| customattributes | string | T | JSON |
| acl | string | T | JSON |
| nullversion | bool | T | Used for versioning |
| deletemarker | bool | T | Used for versioning |
| ssetype | string | T | Data at rest (encryption) |
| encryptionkey | string/blob | T | Data at rest (encryption) |
| initializationvector | string/blob | T | Data at rest (encryption) |
| tier | int32 | F | Storage tier |
| storageClass | string | T | Storage class name |

**Storage Tier**

| Column | Type | Nullable | Remark |
| --- | --- | --- | --- |
| tier | int32 | F |   |
| backendtype | int32 | F |   |
| storageclass | int32 | F | Each backend has it's own definition of storage classes,and each storage class can be mapped to a specific tier in OpenSDS. |

**Cluster**

| Column | Type | Nullable | Remark |
| --- | --- | --- | --- |
| clusterid | string | F | The system to which cluster belongs to. Ex. YIG system the cluster belongs to|
| fsid | string| F | |
| pool | string | T | |
| weight | int | T | |



### REST API impact

The API Handler won't change so REST API impact will not be there

### Security impact

Need to be analysed if there is any impact for the Objects in transition or in store

### Other end user impact

No. This is just backend change so end-user will have the same seamless experience.

### Performance impact
With new feature support and Integration of YIG and Gelato, should not impact the performance. As it will use ‘caching’, performance should improve for very large set of metadata being stored.
The goal is to add new features without any performance impact.


### Other deployer impact
If OpenSDS gelato supports multiple DB, the selection and deployment of DB should be decided upon the config.

### Developer impact

No impact on other developers except multi-cloud.

## Use Cases

OpenSDS should support a scalable and enriched multi-cloud management platform. Also, it should have a performant on-premise Object Storage backend. YIG s3 compatible object storage API with a highly performant Ceph storage Backend should support multi-cloud management.

### Encryption of Data at rest
    * Regulatory compliance
    * Data Security

### Bucket Location
    * Specify region where the bucket needs to be created
    * Use the specific endpoint to access
Note: This can be supported in future

### Versioning
    * Keep multiple variants of an object in same bucket
    * Recoverability from accidental deletions
    * Additional data protection (Backup)

### CORS
    * Cross Origin Resource Sharing
    * Intra Domain resource sharing for client Web application
    * Web App in one domain share resource in different domain

### ACL
    * Access Control List
    * Security, Permissions
    * Define type of access for a Bucket or Object for accounts or groups

### Bucket Policy
    * Security
    * Compliance
    * Policy to grant other accounts or IAM users access permissions for the bucket or object in it
    * Access Management

### Configurable buffered copy object
    * Handles movement/transition of large Objects (in-cloud/cross-cloud)
    * Performance improvement

### HEAD Bucket
    * Determine if bucket exists
    * Access permission validity

### HEAD Object
    * Retrieve metadata from an object (not object)
    * Validate the Data Migration or Transitioning


## Implementation

* YIG currently supports only Ceph as Object Storage Backend. This code base will have to be expanded to other Storage Backends too and then can fit into gelato
* S3 API impact
* YIG code will have to support go-micro framework. OpenSDS multi-cloud uses go-micro services architecture
* Need to develop DB Adapter and Cache Adapter. DB Adapter should be scalable to support   any relevant DB. YIG provides this capability
* OpenSDS multi-cloud need to support YIG Ceph Pool as storage backend

### S3 API impact for new features

| **Feature** | **S3 API Impact** |
| --- | --- |
| SSE | Get Object |
| Put Object |
| Delete Object |
| Post object (Browser based Uploads Using POST v4) |
| New multipart upload |
| Put Object part |
| Abort multipart upload |
| Complete multipart upload |
|   |
| Buffered upload/download (configurable) | Copy Object |
|   |   |
| Bucket location | PUT bucket |
| GET Bucket |
|   |   |
| ACL | GET Bucket ACL |
| PUT Bucket ACL |
| GET Object ACL |
| PUT Object ACL |
|   |   |
| CORS | GET Bucket CORS |
| PUT Bucket CORS |
| DELETE Bucket CORS |
|   |   |
| Policy | GET Bucket Policy |
| PUT Bucket Policy |
| DELETE Bucket Policy |
|   |   |
| Versioning | PUT Bucket Versioning |
| GET Bucket Versioning |
|   |   |
| HEAD | HEAD Object (metadata of object) |
| DELETE (Specific object version) |
| GET (Specific Object version) |

## Alternatives considered
N/A


## Open issues

* SQL DB ex, TiDB comes with relational SQL server and TiKV helping to get the scalability and performance of NoSQL. Currently OpenSDS multi-cloud management uses MongoDB.
If the data stored is only Object metadata, do we need to support SQL DB?
* Licensing of MongoDB: Apache 2.0 changed to SSPL for recent releases
* Going ahead OpenSDS multi-cloud management feature will not be limited only to Object storage and migration. It may encompass other key feature requirements like Bucket Management, Cost analysis, DR, Compute provisioning etc..
Should we change the design in consideration to that?



