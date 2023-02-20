

# KubeDB by AppsCode
 ## INTRODUCTION :
 Making running production-grade database easy on Kubernetes. This chart bootstraps a KubeDB controller deployment on Kubernetes duster using the Helm package manager. In the early days of Kubernetes, replication controllers were used to running a single pod for a database with the introduction of statefulset , it became easy to run a Docker container for any database.
 
 KubeDB is a framework for writing operators for any database that support the following operational requirements:
 * create a database declaratively using CRD.
 * take one-off backups or periodic backups to various cloud    stores.
  * use cli to manage database like kubectl for kubernetes.
 * keep track of deleted database,cleanup prior snapshots with a single command.
 * apply deletion lack to avoid accidental deletion of the database. 
## PROJECT SUMMARY

| website | [https://kubedb.com/](https://kubedb.com/) |
|--|--|
|  Organization/Foundation Name |AppsCde  |
|Open/Proprietary|Open|
|Source path|[https://kubedb.com/pricing/]|(https://kubedb.com/pricing/)
|Brief description|KubeDB **simplifies Provision, Upgrade, Scaling, Volume Expansion, Monitor, Backup, Restore for various Databases in Kubernetes on any Public & Private Cloud**|

## PROJECT DETAILS 
###  KubeDB Key Features 

 ### Lower administrative burden

KubeDB simplifies many of the difficult or tedious management tasks of running a production grade databases on private and public clouds. Maintain one stack for all your stateless and stateful applications and simplify the operational complexity.
### Native Kubernetes Support

Standard Kubernetes is all you need. If you can run Kubernetes, you can provision and manage databases using KubeDB. Use standard Kubernetes CLI and API to provision and manage databases.
### Performance

KubeDB uses Persistent Volume Claims (PVC) to dynamically provision disks for database instances. Using appropriately defined StorageClasses, KubeDB provisioned database instances are designed to scale from small development workloads up to performance-intensive workloads on private and public cloud environments.
### Availability and durability

KubeDB will backup your database and transaction logs at a user defined frequency and store both for a user-specified retention period in a cloud object store (S3, GCS, etc.) or local filesystem (like NFS, etc.). You can also initiate one-off backups. KubeDB uses Stash by AppsCode to take deduplicated and encrypted backups so that you only incur the cost of incremental storage use. You can create a new instance from a database snapshots whenever you desire.
### Manageability

KubeDB comes with native support for monitoring via Prometheus. You can use builtin Prometheus scrapper or CoreOS Prometheus Operator to monitor KubeDB supported databases as well as KubeDB operator itself. You can use the Grafana to view key operational metrics, including compute/memory/storage capacity utilization, I/O activity, and instance connections. You can also use any metrics solutions like Datadog with KubeDB.
### Cost-effectiveness

KubeDB is free to use on any supported Kubernetes engines. There is no up-front investment required, and you pay only for the resources you use to your infrastructure provider. And, when you’re finished with a database instance, you can easily delete it.
### Security

You can secure your Databases with TLS using KubeDB. Also, KubeDB allows you to use encrypted storage for your databases using keys you manage through your cloud provider’s key management service. 

## ARCHITECTURE
From here you can learn all about KubeDB’s architecture and how to deploy and use KubeDB.

- ![Understanding Kubernetes Architecture with Diagrams](https://phoenixnap.com/kb/wp-content/uploads/2021/04/full-kubernetes-model-architecture.png)
- KubeDB **uses Persistent Volume Claims (PVC) to dynamically provision disks for database instances**. Using appropriately defined StorageClasses, KubeDB provisioned database instances are designed to scale from small development workloads up to performance-intensive workloads on private and public cloud environments.
# Deploy & Manage Databases on Kubernetes 
### Cost-effectiveness

KubeDB community edition is FREE to use on any supported Kubernetes engines. You can deploy and manage your databases on Kubernetes using KubeDB. There is no up-front investment required. We offer a 30 days license FREE of cost to try KubeDB Enterprise edition.
### Reserved instances

Cloud providers like AWS and Azure have the option to reserve virtual machines in advance for 1-3 year terms and provide a significant discount (up to 75% - 80%) compared to on-demand VM pricing. KubeDB operators can provision database instances on reserved instances and significantly reduce your TOC for the DB instances.
### Halt and Resume

KubeDB allows you to easily halt and resume your database instances. This makes it easy and affordable to use databases for development and test purposes, where the database is not required to be running all of the time.
