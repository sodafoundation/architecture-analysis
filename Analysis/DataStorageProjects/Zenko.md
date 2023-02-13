

# "ZENKO" Project Analysis
### **Introduction**-

Zenko, Scality’s multi-cloud controller, provides an open-source, platform-agnostic gateway to facilitate data replication and management for storage managers handling extreme data volumes over multiple clouds.
Zenko provides a single integration point from which cloud data can be managed in several protocol spaces.  Zenko offers these capabilities using the logic and much of the syntax of Amazon Web Services’ Simple Storage Service protocol (AWS S3) through its Cloud Server module.

### **Project Summary-**

|||
|-|-|
|**Website**| [https://www.zenko.io](https://www.zenko.io)|
|**Organization/Foundation Name**|Scality|
|**License**|Apache License 2.0
**Open/ Proprietary**|Open
**Source Path**|[https://github.com/scality/Zenko](https://github.com/scality/Zenko)
|**Brief Description**|Zenko is Scality’s open source (Apache 2.0) multi-cloud controller, it is a software which enables everyone to stay in control with their data, when storing in public and private clouds.


### **Project Details-**

**Key**  **Features**:

>  - Store all data unmodified so that it can be accessed and acted on in the cloud directly.
> - One-to-Many cross region replication.
> - Multiple storage backend (RING sproxyd, RING S3 Connector. Amazon S3)
> - Enable data mobility so that data can be easily placed in the most efficient location.
> - Provide a single endpoint through which data can be stored, retrieved and searched across any location.

### **Architecture**:

**Zenko Basic Architecture-**

- Zenko provides a layer that mediates between a user or configured storage frontend and one or several storage backends.

- Zenko uses agile application frameworks such as Kubernetes for orchestration and Prometheus for monitoring.

![](https://zenko.readthedocs.io/en/latest/_images/Zenko_hi-level.svg)
					_Figure: Zenko Architecture_


**Zenko Cloud Architecture**-

![](
https://zenko.readthedocs.io/en/latest/_images/Zenko_arch_NoNFS.svg)

_Figure: Zenko cloud architecture_

The following table offers brief descriptions of the Zenko components in this architecture:


| | |
|-|-|
|**Component**|**Description**
|***CloudServer***|CloudServer is an open-source Node.js implementation of a server handling the Amazon S3 protocol. It presents the core logic for translating user inputs and data into object storage on several cloud storage systems. With this component, users can create locations corresponding to different clouds.
***Blobserver***|Blobserver is an open-source Node.js implementation of a server handling Microsoft’s Azure Blob Storage protocol. It is functionally analogous to CloudServer in that it provides logic to translate user inputs and data into Azure Blob-compatible object storage. This feature remains under development.
***Backbeat***|Backbeat manages the queues involved in Zenko cloud event tracing (such as admin_API, etc.) and job queuing for current actions (such as CRR, lifecycle management, synchronous encryption, etc.).
***CLI***|CloudServer accepts commands from command-line interfaces.
***Orbit***|The Orbit UI offers users controls for CloudServer, Blobserver, workflow management, user management, and Metadata (MD) instance configuration using such parameters as location, access key, workflow configuration (CRR, for example), basic search, etc. The UI runs in the cloud and is hosted by Scality.
***MongoDB***|An open-source metadata database, MongoDB works with one or multiple instances in scale-out mode. It also explodes JSON values, allowing powerful searches and potentially indexing to speed up searches.
***Local RING/sproxyd***|For local cloud storage (including transient source), S3 data can be put to an sproxyd RING


**Current Usage:**

Zenko supports Scality RING, Amazon S3, Azure Blob Storage, Google Cloud Storage, Digital Ocean, and Wasabi clouds. Developers can work directly with Zenko via the Amazon S3 API which also means they can use a single API set to manipulate a wide variety of storage systems. Over time, support for additional cloud storage services, object stores and NAS will be introduced into Zenko. It is used by many organizations such as datanami, The Register, CRN, NETWORKWORLD, ComputerWeekly.

**Technical Details:**

Zenko’s CloudServer is the open source microservice that translates API calls from Amazon S3 to other protocols. It can run as a stand-alone container or run as a NodeJS app. You don’t need to run the full Zenko stack if all you need is a S3-compatible storage service without replication rules or expiration policies. Global metadata search keeps track of which objects are stored where. Data replication and workflow policies make it easy to control costs and reduce lock-in from storage providers.

### **Other Information**

**Project Comparison:**

Zenko can be compared with many other multi-cloud storage providers, in that one of the most compared provider is CloudAware. CloudAware is a 1-stop management platform from Change Management to Compliance Engine for large scale AWS, Azure & Google Cloud consumers. It is easy to deploy and manage applications in the Amazon, Azure, and Google Clouds. CloudAware is ranked 55th in Cloud Management while Scality ZENKO is ranked 63rd in Cloud Management. Therefore CloudAware is most compared with Zenko.

**References:**

[https://www.zenko.io/what-is-zenko/](https://www.zenko.io/what-is-zenko/) : Zenko’s official website

[https://zenko.readthedocs.io/en/latest/operation/Architecture/index.html](https://zenko.readthedocs.io/en/latest/operation/Architecture/index.html) : Zenko’s  Architecture and Zenko cloud architecture.

[https://www.peerspot.com/products/comparisons/cloudaware_vs_scality-zenko](https://www.peerspot.com/products/comparisons/cloudaware_vs_scality-zenko) : Comparison of Zenko and CloudAware multi-cloud storage provider.
