# Multi-Cloud Design

This document serves as the design specification for the multi-cloud data engine in OpenSDS. This proposal was drafted [here](https://docs.google.com/document/d/1qd2p_-I7ryXxz9Owa1KaLH1Uoe-eH8kjtHxOWFFp1Rs/edit?usp=sharing).

## Background

Multi-cloud data engine provides policy-based data mobility across public and private clouds.  This can be used in the following cases:

* User may want to upload some datasets to the public cloud for data analysis and delete the data after the work is complete.
* User can leverage multi-cloud data engine to move data to the public cloud for high CPU computing when that is required for a period of time.
* User can use multi-cloud data engine to move data to a location near to where it is needed for convenience.
* Multi-cloud data engine allows data to be available and accessible all the time.  In the case where one cloud is down, user can still have access to data in another cloud that is up and running.
* Frequently accessed data can be stored on-premise. Data not accessed for a long time can be stored in archival storage in public cloud.  Data can be moved to different location depending on usage, age, access frequency.


## Objectives

### Goals

* Goal 1: Develop multi-cloud data management mechanism. Allow tenants to manage multi-cloud through dashboard.

    * Goal 1 - sub goal 1: Support multi-cloud data management for backends include AWS S3, Azure, and local object storage that is S3 compatible such as Ceph (or Swift).

    * Goal 1 - sub goal 2: Provide S3 REST API support such as PUT/POST/GET/DELETE for object storage.

    * Goal 1 - sub goal 3: Support policy-based data migration.  Allow tenants to configure data migration policies, allow data to be migrated based on policies (either manually started or automatically triggered) across object stores in the clouds or between local NAS storage and object storage in the cloud.

* Goal 2: Provide integration with Zenko. Allow tenants to use Zenko to manage multi-cloud through OpenSDS dashboard.

* Goal 3: Try to make our metadata design compatible with Zenko’s.

### Non Goals

* Goal 1: The ability to display relationships of source and destination data after migration will be handled in the future.

* Goal 2: The ability to search objects in buckets based on key-value pairs will be added in the future.

* Goal 3: Only data migration in the multi-cloud scenario will be supported in phase 1. Data replication/backup/archive and the life cyclement management of data (including deleting data after it has expired or migrating to storage with lower cost) will be addressed in the future.


## Design Details

The multi-cloud data engine design is shown in the following diagram:

![Multi-Cloud Architecture Diagram](multicloud_architecture.png?raw=true "Multi-Cloud Architecture Diagram")

Multi-cloud data engine includes the following components:

* Identity Service: Keystone will be used for authentication and authorization.

* S3 Service: S3 Service contains a S3 controller that handles S3 API requests, communicates with database and object storage backends on premise or in the cloud, and returns results to the caller.  S3 Service also has a LocationMapper that converts the location of an object passed in from the API into the actual location on the object storage.

* Backend Service: Backend service manages object storage backends on premise or in the cloud.  One backend in OpenSDS multi-cloud data engine is corresponding to a bucket in S3 (or a container in Azure blob).  There will be create/delete/update/get/list APIs for the backend.  Backend Service communicates with the backend on premise or in the cloud to validate the information about the bucket.  Metadata about the bucket will be saved in the database.

* Dataflow Service: Dataflow Service is in charge of data migration.

* Data Mover: Data Mover is responsible for moving the data from one location to another.

* Database: MongoDB will be used for database.  Metadata will be saved in the database after a backend bucket is added. Information on other resources defined in multi-cloud data engine will also be saved in this database.

* Backend Adapters: A Backend Adapter communicates with a backend such as AWS to support backend management.


### Dataflow Service

Dataflow Service includes a Manager and a Scheduler.  The Manager includes 4 components.  They are Policy Manager, Connector Manager, Plan Manager, and Job Manager.

The Manager makes plans for data movement such as migration.  Scheduler automatically executes data movement tasks based on plans made by the Manager.  Data Mover is the one responsible for actually moving the data.

Policy Manager: A policy includes a schedule that describes when and how often a migration will happen.  This can leverage the design for the OpenSDS snapshot profile.

Connector Manager: A Connector contains information to communicate with a bucket in the backend.

Plan Manager: A plan contains 2 connectors and 0 or 1 policy.  The 2 connectors include source and destination connectors.  0 policy implies the plan will not be automatically executed but it can be manually run.

Job Manager: Job will be created after a plan starts to be executed based on the schedule.  It tracks the status of a job.

![Multi-Cloud Dataflow Diagram](multicloud_dataflow.png?raw=true "Multi-Cloud Dataflow Diagram")

* API Handler receives a request and authenticates via Keystone.  It passes the request to the corresponding Dataflow Manager.
* Dataflow Manager receives a request from the API.  It processes the request, searches DB for metadata, saves metadata to DB, and returns a response.
* Plan Manager sends a request to Scheduler to run a plan. Scheduler can also periodically read the metadata and find plans to run. Scheduler parses the policy in the plan and decides when and whether to start a migration. If it is time to run a plan, it sends the request (a job) to the DataMover through the Apache Kafka, a distributed streaming platform.
* Data Mover retrieves requests (jobs) from Kafka and migrates data based on the job description.
* Data Mover has different plugins to handle different migration tasks.
* Job Manager has external interface that can be queried to find out the status of a job.

#### Metadata

##### Policy Metadata

Metadata for Policy is as follows:

`name`: Name of the policy.
`description`: Description of the policy.
`schedule`: Schedule of the policy, including `type` (once/day/week/month), `day` (1, 2, 3,...31,lastday), and `time`.

##### Connector Metadata

Metadata for Connector is as follows:

`selfDefine`: true or false. True means the connector will be defined automatically based on backends information. False means user input is needed to define the connector.
`bucket`: Name of the bucket. Required when `selfDefine` is false.
`type`: AWS-obj, Azure-obj, Ceph-obj, HWS-obj, NAS-cifs, etc.
`location`: Valid endpoint.
`selfDefBucket`: Corresponding bucket name when `selfDefine` is true.
`ak`: Access key.
`sk`: Secret key.
`name`: User name. (valid for NAS)
`password`: Password. (valid for NAS)
`uuid`: Unique identifier of the connector.

##### Plan Metadata

A Plan contains 2 Connectors, 0 or 1 Policy.  Metadata for Policy is as follows:

`name`
`description`
`status`: active/deactive
`isScheduling`: true/false
`schedServer`: ID of the scheduler server
`type`: migration is the only valid type currently.
`sourceConnector`
`sourceDir`
`destConnector`
`destDir`
`overWrite`: true/false
`remainSource`: true/false
`lastSchedTime`
`policy`:
`filter`: Filter for the object.

##### Job Metadata

When the scheduler executes a plan, a job is created.  Metadata for Job is as follows:

`id`
`type`: migration
`planName`
`totalCount`
`passedCount`
`totalCapacity`
`sourceBackend`
`destBackend`

#### Dataflow Interfaces

Dataflow service supports the following interfaces:

* CreatePolicy
* UpdatePolicy
* DeletePolicy
* GetPolicy
* CreateConnector
* UpdateConnector
* DeleteConnector
* GetConnector
* CreatePlan
* UpdatePlan
* DeletePlan
* GetPlan
* RunPlan
* GetJob


### Backend Service

![Multi-Cloud Backend Diagram](multicloud_backend.png?raw=true "Multi-Cloud Backend Diagram")

* API Handler receives a request and starts authentication with Keystone.  It sends the request to the Backend Manager.
* The Backend Manager receives a request to create, update, delete, get a backend.
* The Backend Manager communicates with the Backend to determine whether the bucket is valid.
* The Backend Manager updates Backend metadata in the database and returns results.

#### Metadata

Backend service is defined by the following metadata.

`type`: Type of backend, including AWS S3, Azure Blob, Ceph, Huawei OBS, etc.
`endpoint`: The endpoint of the corresponding physical backend bucket.
`physicalBucketName`: Name of the physical backend bucket.
`ak`: AWS access key (It is called sn in Azure Blob).
`sk`: AWS secret key
`uuid`: Unique identifier for the backend.

#### Backend Interfaces

Backend service supports the following interfaces.

* CreateBackend
* UpdateBackend
* DeleteBackend
* GetBackend
* ListBackend


### S3 API Service

![Multi-Cloud S3 Service Diagram](multicloud_s3.png?raw=true "Multi-Cloud S3 Service Diagram")

* API Handles receives a request and authenticates it. It processes the request and sends it to S3 controller.
* S3 controller handles S3 API requests. It processes a request on a bucket, updates metatdata, and returns results.  It also processes a request on an object by finding the location from the LocationMapper, obtaining the backend physical location where the object resides, and selecting the appropriate BackendAdapter to handle the request.
* BackendAdapter communicates with Backend to complete the task and returns results.
* LocationMapper is responsible for searching the database to find the physical location of an object.  Cache can be introduced in the future to improve performance.
* A bucket in OpenSDS is a virtual bucket that is mapped to a physical bucket in the object store on premise or in the cloud. The relationship between the virtual bucket and the physical bucket is many to one.

#### Bucket Metadata

`name`: Name of the bucket.
`owner`: Owner of the bucket.
`ownerDisplayName`: Display name of the owner.
`creationDate`: Creation time of the bucket.
`acl`: Policy of the bucket.
`transient`: true/false. Indicates whether the bucket is temporary.
`deleted`: true/false. Indicates whether the bucket is deleted.
`serverSideEncryption`: Encryption method.
`versioningConfiguration`: Version configuration.
`locationConstraint`: Location constraint of the bucket.
`websiteConfiguration`: Website configuration.
`cors`: Cross region configuration.
`replicationConfiguration`: Replication configuration.
`lifecycleConfiguration`: Life cycle configuration.
`readLocationConstraint`: Location constraint on where to read from the bucket.
`isNFS`: true/false
`uuid`: Unique idenfication.

#### Object Metadata

`key`: Name of the object.
`ownerId`: Name of the owner of the object.
`ownerDisplayName`: Display name of the owner.
`cacheControl`: Cache control.
`contentDisposition`: Content disposition.
`contentEncoding`: Content encoding type.
`expires`: Response expiration time.
`contentLength`: Length of the content. 
`contentType`: MIME type of the content.
`contentMD5`: MD5
`x-amz-version-id`: Version id.
`x-amz-server-version-id`: Version id of the server.
`x-amz-storage-class`: Storage class of the object.
`x-amz-server-side-encryption`: Encryption method.
`x-amz-server-side-encryption-aws-kms-key-id`: Key id.
`x-amz-server-side-encryption-customer-algorithm`: Encryption algorithm.
`x-amz-server-side-encryption-customer-key`: Encryption key.
`x-amz-server-side-encryption-customer-key-MD5`: MD5 for encryption key.
`x-amz-website-redirect-location`: Redirect location.
`acl`: Permissions control.
`location`: Information on uploading in chunks.
`is-null`: true/false. True indicates no version. 
`null-version-id`: Null version id.
`isDeleteMarker`: Marked for deletion.
`backendName`: Name of the backend bucket.
`tags`: Tags.
`replicationInfo`: Configuration info on replication.
`lastAccessTime`: Last access time since 1970-01-01 00:00:00.

#### S3 Interfaces

* PutBucket
* GetBucket
* HeadBucket
* DeleteBucket
* PutBucketACL
* GetBucketACL
* PutBucketCors
* DeleteBucketCors
* GetBucketCors
* PutObject
* PutObjectCopy
* GetObject
* HeadObject
* DeleteObject
* Multi-Object Delete
* PutObjectACL
* GetObjectACL
* MultipartUpload
* GetService
* Put Bucket Website
* Get Bucket Website
* Delete Bucket Website
* Put Bucket Versioning
* Get Bucket Versioning
* V2 Authentication
* V4 Authentication (Transferring payload in a single chunk)
* V4 Authentication (Transferring payload in multiple chunks)
