# Multi-Cloud feature enhancements

**Author(s)**: [Shufang Zeng](https://github.com/sfzeng), [Neelam Gupta](https://github.com/neelamgupta1491), [Ashit Kumar](https://github.com/kumarashit), [Yu Zou](https://github.com/hopkings2008)

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
A high-level comparision of Ceph and YIG:

| Ceph (radosgw) | YIG (Yet another Index Gateway) |
|----------|-----------|
| S3 and Swift compatible Object Storage Gateway | S3 compatible Object Storage Gateway |
| Performance impact while storing small files | Uses KV store to overcome small file storage issue |
| Performance impact for read and write of large files | Overcome read write issue of large files using optimized libradosstripper API |
| Not so scalable Ceph OSD nodes | Using write consistency and dynamic buf it enables scaling of ceph OSD nodes |
| | Avoids data movement and IO drop due o scale out clusters | 
| | Allows more than one ceph cluster to work together and extend the storage pool capacity |


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

#### Integrate YIG Ceph pool as a storage backend into DataStorageEngine
##### Motivation

Refactor DataStorageEngine and provide a uniform framework to add a storage backend easily and integrate the existing Yig Ceph pool as a new storage backend.

##### Goal of DataStorageEngine

The DataStorageEngine will only provides I/O function to upper layer and mananges all kinds of different storage backends.

##### Terms

    * StorageDriver: It is equivalent to the storage backend and performs I/O function for specific storage backend

##### Components of DataStorageEngine

Below are the components for DataStorageEngine:

    * Driver Factory Manager: Responsible for getting a correct driver factory which can create the StorageDriver
    * Driver Factory: Responsible for creating the specific StorageDriver
    * Storage Driver: It is the interface which abstracts all I/O functions for a stroage backend.  
                        It defines all the interface which can be called by upper layer

##### Steps to add a driver for a new storage backend

    * Implement StorageDriver for the storage backend
    * Implement DriverFactory for the new driver
    * Register the new driver factory into DriverFactoryMgr. It can register the new driver factory in init() function under the module
    * Import the new storage backend module into the init.go file under DataStorageEngine module

##### The storage driver for YIG storage backend

As there can be many instances of YIG storage backend, each one will serve as the related endpoint. Each storage backend will have a working storage driver, so there is only one driver for each endpoint. This means that for mulitple requests from user to the same endpoint, there will be the same storage driver which handles the request.

##### How YIG regsiters as a storage backend for a specific endpoint

When storage driver of YIG starts, it will get the configuration from a file. Currently, each config file will contain a section for endpoint information, and the driver factory will create the storage driver from it and relate the driver to the endpoint. To simplify the initial process of YIG storage driver registration, currently, the driver factory of YIG will monitor the changes of the specific folder which contains the configuation file. If a configuration for a new endpoint is added, the driver factory will create one new driver and relate it to the newly added endpoint. Then, the newly created driver can serve for requests to the new endpoint.

##### Configuration for YIG storage driver

Toml is used by the configuration for YIG storage driver. Below are the sections for the configuration of storage driver:    

```toml
# this section contains endpoint information
[endpoint]
url="xxx"
# this section should be according to the common log module
[log]
log_path="/var/log/yig"
log_level=
# this section define the redis access info, and should be according to common cache module.
[cache]
# the redis mode we use: 0 for normal client, 1 for cluster, 2 for sentinel.
redis_mode = 0 
# for cluster nodes or sentinel nodes.
redis_nodes = "192.168.1.1:6379, 192.168.1.2:6379"
# the master name of the sentinel.
redis_master_name = "master"
redis_connect_timeout = 1 
redis_read_timeout = 1 
redis_write_timeout = 1 
redis_keepalive = 60
redis_pool_max_idle = 3 
redis_pool_idle_timeout = 30
# this section defines the database access info and should be according to the db layer
[database]
db_type=1
db_url="root:@tcp(10.5.0.17:4000)/yig"
db_password=""
```

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
This relates to the data being encrypted at rest, in SODA, for the following backend object storage types

	AWS S3 https://aws.amazon.com/s3/
	GCP https://cloud.google.com/storage/docs/json_api/v1/objects
	Azure Blob storage https://docs.microsoft.com/en-us/azure/storage/blobs/storage-blobs-introduction
	HWS https://intl.huaweicloud.com/en-us/product/obs.html
	CEPH https://docs.ceph.com/docs/mimic/radosgw/
	YIG https://github.com/journeymidnight/yig
	
Today, most of the cloud object storage providers that are S3 compatible, chiefly support 3 types of encryption on the server side, of client data at rest (give or take a few exceptions to this subset among different storage providers, listed in the matrix below)

    1. Encryption with server managed keys, which we abbreviate to SSE_SMK
    2. Encryption with customer provided keys, abbreviated to SSE_CPK
    3. Encryption with KMS (example of a KMS here) provided keys, abbreviated to SSE_KMS.
       In this option too, the key is provided by the customer, but stored in a key vault
       instead of at the client side

![gelato-multicloud-sse](ssetable.jpg?raw=true "SSE support comparison")

For the DAITO release, we will support the Server Managed Key variant, explained below

SSE_SMK

![gelato-multicloud-ssesmk](ssesmk.jpg?raw=true "SSE SMK")

    1. The encryption is handled on the server, with symmetric key AES 256 cipher used for encryption
    2. Since it is symmetric key encryption, the same key is used to encrypt and decrypt the data
    3. The key itself is also further encrypted, decrypted and used as needed, on the server side, without any explicit effort on the user side
    4. On most providers, this is enabled by default on a bucket
    5. A typical upload sequence would work like
            Upload object
            Use Key to encrypt data (NOT the metadata)
            Encrypt and store encrypted key alongside encrypted data
    6. Further, some providers also support bucket level policies on encrypted buckets like
    7. Do not allow non-encrypted objects
    8. Do not allow PUT requests that do NOT request encryption for the object
       and so on…


How this will work in OpenSDS

    For SODA to support encryption of data on multiple backends, we follow the approach 
     
    1. Encrypt/Decrypt the data in SODA
     
    2. Store the encrypted byte[ ] on the cloud backend
     
    3. Do NOT enable encryption on the cloud backend
    
![gelato-multicloud-ssesmk](sseseq.jpg?raw=true "SSE SMK")
   


### Bucket Location
    * Specify region where the bucket needs to be created
    * Use the specific endpoint to access
Note: This can be supported in future

### Versioning
    * Keep multiple variants of an object in same bucket
    * Recoverability from accidental deletions
    * Additional data protection (Backup)
    the requirements and use cases for support for versioned object storage in SODA. These have been arrived at after looking primarily at the Versioning support in AWS S3, Azure (or the lack of it here) and GCP object storage.
    
    This document also lists the cross feature interactions with versioning and 
    1. Object migration in SODA
    2. Object lifecycle in SODA
    
Requirements

    1. Support versioning on a bucket
    2. Support suspend versioning on a bucket
    3. Support resume versioning on a bucket
    4. Support the below state transitions on a bucket w.r.t versioning  

![gelato-multicloud-versioningst](versioningst.jpg?raw=true "Versioning State Machine")

    5. Support recovery from accidental deletion or overwrite. For example:
        If you delete an object, you can always restore the previous version
        If you overwrite an object, it results in a new object version in the bucket. 
        You can always restore the previous version
    6. For a versioning enabled bucket, support
        Adding Objects
            - Using all supported means, including but not limited to the UI, API and CLI
        Listing Objects
            - Using all supported means, including but not limited to the UI, API and CLI
            - Each request returns up to 1,000 versions, unless you specify a lower number. If the 
            bucket contains more versions than this limit, you send a series of requests to retrieve 
            the list of all versions. This process of returning results in "pages" is called pagination. 
            - To show how pagination works, the examples limit each response to two object versions. 
            - After retrieving the first page of results, each example checks to determine whether the version 
            list was truncated. If it was, the example continues retrieving pages until all versions have 
            been retrieved. As an example, In your bucket, if you have 2 objects a and b, a has 900 versions 
            and b has 300 versions, the list call will return 900 versions of a and the most recent 100 
            versions of b (total 1000)
            - Object versions will be returned in the order in which they were stored, with the most recently 
            stored returned first
        Retrieval of a subset of all object versions in a bucket, for example, retrieve all 
            versions of a specific object (filter by name or object identifier)
            - When the response exceeds the max-value (1000), a second request can be submitted to retrieve 
            the remaining object versions
            - Retrieving Object Versions
            - Using all supported means, including but not limited to the UI, API and CLI
            - By default, always retrieve the most recent version
            - Retrieval of a specific version of an object by specifying the Version Id
        Deleting Object Versions
            - Using all supported means, including but not limited to the UI, API and CLI
            - On a versioning enabled bucket, DELETE will not delete the object permanently
            - To delete the object permanently, support ‘DELETE Object VersionId’
        Transitioning Object Versions
            - Using all supported means, including but not limited to the UI, API and CLI
            - An Expiration action that applies to the current object version and instead of deleting the current object version, marks it as non-current
            - A NoncurrentVersionExpiration action that applies to noncurrent object versions, which will be permanently removed and cannot be recovered
        Restoring Previous Versions
            - Using all supported means, including but not limited to the UI, API and CLI
            - To Copy a previous version of the object into the same bucket. The copied object becomes the current version of that object and all object versions are preserved
            - To Permanently delete the current version of the object. This, in effect, turns the previous version into the current version of that object
        Versioned Object Permissions
            - PUT Object ACL Version Id to set the ACL for a specific object version
            - GET Object Version Id to get the ACL for a specific object version
            
    7. For a versioning suspended bucket, support
        Adding objects
        Getting objects
        Deleting objects

    All the above requirements are to be supported for the following Object stores
        AWS S3 https://aws.amazon.com/s3/
        GCP https://cloud.google.com/storage/docs/json_api/v1/objects
        Azure Blob storage https://docs.microsoft.com/en-us/azure/storage/blobs/storage-blobs-introduction
        HWS https://intl.huaweicloud.com/en-us/product/obs.html
        CEPH https://docs.ceph.com/docs/mimic/radosgw/
        YIG https://github.com/journeymidnight/yig
            
        
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



