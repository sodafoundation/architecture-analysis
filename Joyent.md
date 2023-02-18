# Joyent
**Joyent Inc.** was a software and services company based in San Francisco, California. Specializing in cloud computing, it marketed infrastructure-as-a-service. On June 15, 2016, the company was acquired by Samsung Electronics

## Technical Details
Joyent uses and supports open source  projects, including Node.js,Illumos and SmartOS, which is its own distribution of Illumos, featuring its port of the KVM Hypervisor for abstracting the software from the hardware, DTrace for troubleshooting and systems monitoring, and the ZFS file system to connect servers to storage systems. The company open-sourced SmartOS in August 2011.

Joyent took software that evolved over time in the running of their hosted business and licensed that software under the name Triton DataCenter (formerly "Triton Enterprise", "SDC" or "SmartDataCenter") 3 to large hardware companies such as Dell.

# Uses of Joyent
## Firmware over the Air

We deliver robust compute, storage and networking features to allow firmware solutions update devices/phones firmware at global scale. Users rely on our private cloud solutions to serve billions of devices around the world at low latency ensuring their customers stay up to date with the latest features and security patches. As the mobile first world continues to grow, we continue to build next generation edge, security and networking optimization solutions to make sure device updates happen on time, every time.

## VPN

Our global datacenter presence, private networking solutions and container services allow VPN solutions to provide global mobile connectivity services to users everywhere. Optimizing for low bandwidth areas and securing sensitive data requires a high performance, always on network and compute platform.

## Data Analysis Platform

To make sure organization data is secure, reliable and easy to use in one place, data lake solutions combining 3rd party software, and in house ETL, tagging and distributed storage integrations is paramount. Combine structured, semi-structured, and unstructured data of any format, even from across clouds and regions. By replacing data silos with a single home, our data lake platform is the foundation of a cost-effective, highly scalable data lake serving a variety of use cases from marketing and product improvements to analytics and machine learning.

## Live Video Solutions

Samsung private cloud capabilities provide a rich set of developer and operator features to allow live video streaming solutions to cost-effectively deliver live communications across a global team in the cloud. We’ve built a highly available, global architecture that delivers a reliable, low latency real-time viewing for business needs.

## IoT Smart Connectivity

IOT device and platforms entrust our cloud to deliver updates, compute, security and managed services across a variety of needs and IOT use cases to ensure their customers have best in class experiences. Global user bases require advanced cloud capabilities like autoscaling, load balancing and CI/CD services to allow them to meet their growing device base at low latency and SLAs. Serverless and container based solutions allow IOT use cases to continue to innovate for their customers as we continue to build robust next generation products and services for an increasingly connected world

# Services
## Data Analytics 

SQL and NoSQL Batch Processing Data Lake Service Data Labeling and Processing Logging and Monitoring

## Compute Service 

Windows & Linux Images Custom Image Management Custom Instance Sizing Bare Metal Provisioning

## Microservices 

Kubernetes & Docker Services Managed Container Registry CI/CD Services API Gateway

## Customer Engagement 

24x7x365 Helpdesk Global NOC Integrated Procurment Technical Account Team

## Database 

Map Reduce Service Managed Database Service Data Lake Multi-DB/Storage

## Developer Enablement 

Cloud Management Console Secrets Management Documentation & Tutorial Portal SDK, CLI, and Provider Libraries

## Machine Learning and AI 

GPU Compute Cloud Managed Kubernetes Service Data Lake 3rd Party Integration

## Cloud Management 

Identity & Access Management GUI & API Management 3rd Party Provider Modules Usage Reports & Quotas

## Network Services 

VPC & DNS Network Services Certificate Management Hardware VPN & DDoS Services HW/SW Nework Firewall

## Security & Compliance 

Cloud Audit Logging Firewall/Networking Logging Secrets Management PCI-DSS & ISO27001

## Serverless Computing 

Events Processing Hub Service Functions as a Service Notification Serviec API Queue Service API

## Storage Services )

Global Object Storage Network Block Storage Ephemeral Instance Storage Object Storage Gateway Service

# About Triton Object Storage
### Simple and Developer Friendly

Triton Object Storage is simple for developers familiar with Unix. Interact with a simple CLI and API, without the need to calculate object number or size.

### Secure

Robust security is built-in, including object level security and access, deep role based access control, client SSH, and data encryption.

### Durable & Scalable

Proven at production scale, Triton Object Storage delivers replication, clustering, failover, backup,. and recovery capabilities.

# Capabilities of Triton

### REST with JSON API

Direct access to perform CRUD operations, search & transform using REST API supporting JSON. All REST APIs are modeled as streams for memory optimization.

### Granular, Replicated, Distributed

Distributed and highly scalable, clustered object store with object level granularity. Replication across multiple data centers with per object replication controls.

### Unlimited Objects, No Size Limits

No restrictions placed on the type, size, or number of objects that can be stored. Linear scalability with infrastructure provisioned.

### Read after Write Consistency

Read after Write consistency protects against data corruption due to loss or disconnection of filesystems or mounted hardware disks.

### Arbitrary Object Versioning

SnapLinks allow you to create a point-in-time reference to the data and metadata that constitutes another object. SnapLinks are immutable helping to create arbitrary versioning schemes.

### Unix-like Interface

Triton Object Storage provides a Unix filesystem-like interface (directories, objects and links). CLI commands (e.g. `mput` for put, or `mfind` for find, `mlist` for list...) for developers familiar with Unix.

### Durability

By default, data is dual replicated and placed in separate data centers. ZFS RAID-Z stores your objects for higher durability. Data is erasure encoded across distributed disks.

# Uses for Triton
 

 -  Backup & Recovery
Backup and recovery of database snapshots for     transaction integrity, fail over, and re-balancing 
 - Image and Video Processing 
 Backup of videos and photos taken from mobile phones stored on the cloud, transcoding, and thumbnail processing  
 - Checksums for Data Integrity
 Checksum verification for integrity of data payloads that are stored in the cloud 
 - Content Storage & Distribution
 Mobile and at the edge apps needing CDN to accelerate delivering data to customer end points 
 - Data at Scale for Modern Apps
 Modern apps on mobile, real-time platforms, or the IoT generating exponential workloads of structured and unstructured data
 -  Big Data Mining and Analytics
 Using standard tools like NumPy, SciPy, and R

# Architecture 
## Starter Package
![](https://lh4.googleusercontent.com/fgppUD2JybEl3kj4WPjr7sNaC1iUgmO2wn15Ny2bm323fNHyxMeG6yq8zs4OKf4D889tTbxwOpmMUe1fSbf3dU5pxIhEMUQAsYX5Wemav9_b4ZmCs5OYe9vIHJa8SCCTYHl51AslwtWVyvnB7AiVyJA)

Starter package The basic Joyent package includes all network infrastructure and application support for a basic cloud-based data center or Web-based business application. Included is access to the Joyent network, a standard Joyent SmartMachine with a configurable amount of RAM and disk storage space, as well as a MySQL database for integration with applications. Joyent SmartDataCenters complete regular backup of all data, including database files and logs.

## SQL Storage
![](https://lh4.googleusercontent.com/5K3ij5cV-DgG7pQKRBgeR17E_3-fh3l8QMZNhABzRazDfyqvxR7pPgJK4dcc3LNzJBAL-BsKzvXIYMo7xSl_IROzxWu0d3f-twG9DO-TW9YlMWeaNUcq3-GmAVKlgF96GhO8TJx-n9B53jvQp2qJxBg)

In a tier 1 scenario, companies can move toward separating their database applications from the main site and also house data on yet another system. Under this scenario the SQL database application runs on its own SmartMachine while its data, logs, and archives are stored on a network attached storage device. In parallel, the site’s main applications are hosted on a separate SmartMachine, and its data is stored on the NFS volume as well. The Joyent SmartDataCenter then backs up the NAS volume on a regular basis.

## Web High Availability 

![](https://lh3.googleusercontent.com/lYjfN8BBUA9MqCy9h2VlUeXxDvsSRi4K2RAx-RALney0v-8xNLk-QGcgkKMlda94MpcCoh1sClA1poTPjscrKidqfYJhLS849NPIeeVqkRr-C-RCeuaJCQPt5w-pkJsciDYFYmMe_0Z2Pojavf1r2Ns)
An important upgrade to basic cloud computing data centers is the addition of load balancers. In the above scenario, a Zeus Load Balancer is placed at the head of the site access point, serving as a traffic manager for all incoming access to the site. Load balancers perform two functions: 1) to evenly distribute traffic among network connections to avoid overloading any one network path, and 2) to reroute traffic from a failed network path to a viable network path, insuring that the site remains up and running despite a segment failure. 
 -  **Image and Video Processing** 
 -  **Checksums for Data Integrity**
 - **Content Storage & Distribution**
 - **Data at Scale for Modern Apps**
 - **Big Data Mining and Analytics**

## Web HA and database master/slave
![](https://lh4.googleusercontent.com/bzg5So4_gEYMCaNmfVuPGd8XIDcCwYy6NmUFYA8nPSMm5XHFhwtb8tUkcajun5Kn2PzxYgUjnxpbLGvb80nkYwHdFhW6NZULtgWna0432bfugs77tl5DMOLHfvcN7Qs32xqv9BG7juROHVqBNylJhvs)
In an effort to add greater reliability to core applications, companies can add a redundant, Slave MySQL database to their configuration. As illustrated above, in this scenario all data coming through either SmartMachine-hosted application are sent to both the Master and the Salve databases. In addition, the Master database continually poles the Slave database to check that its contents match. The Slave database, on the other hand, continually checks the Master database to make sure it is up and running. In the event that the Master goes down, the Slave database takes on all database functions. In addition, the Master and Slave databases can create separate NFS storage pools on the NAS device, capable of being interchangeably mounted in event of a failure.


## High availability up to storage
![](https://lh3.googleusercontent.com/O2DsmskkOxWgbgjcTE17UWF-27WcQGbucadw9791jFMtvIwkpNUC58umUdWkfoGVUPl4aIfODn0XcbScwI0XgpWYUxo0MhVRBg8wqCkGg5tTV3OvhFhhDBO6TjnLuNLtFVftQWL0PR5juLd1ftfO8Ns)
In a tier 4 implementation, a second Zeus Load Balancer has been added to the system architecture. This provides even greater traffic management, but more importantly, it provides a redundant load balancer in the event that one fails. Now all critical elements of the data center have redundant backup units, from Web head to database applications.Architecture 




