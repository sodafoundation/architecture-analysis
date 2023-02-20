


> Written with [StackEdit](htt*emphasized text*ps://stackedit.io/).
> ## Amazon Elastic Block Store
> <![endif]-->

**Introduction**

EBS is a block storage system used to store persistent data. Amazon EBS is suitable for EC2 instances by providing highly available block level storage volumes. It has three types of volume, i.e. General Purpose (SSD), Provisioned IOPS (SSD), and Magnetic. These three volume types differ in performance, characteristics, and cost.

![amzebs](https://digitaledge.org/wp-content/uploads/2016/02/amazon-ebs.png)

| website | https://www.amazonaws.cn/en/ebs/  |
|--|--|
| Organization Name | Amazon AWS |
|License|not open source|
|  |  |




## Key Features



**It provides the High-Performance Database.**

Amazon Elastic Block Storage creates the storage volume and further attaches them to the Amazon EC2. After the attachment, the user is allowed to create the file system to run a database. Also, the AWS EC2 introduces a variety of databases as per the need of the user to maintain the cost and requirement of the user. Further, For security purposes, these are placed in the special availability zone to protect data from the failure of the single component.

**It helps in Managing Access.**

In Amazon Elastic Block Storage, the Amazon-managed key and the keys created by the user to provide the security to the data EBS encryption enables the data at the rest security by encrypting the data volumes, boot volumes, and the snapshots. Further, the Access management is done with the help of IAM which is the feature of the AWS account offered at no additional charge. Also, the user will charge only for the use of other AWS services.

**It provides Encryption.**

Amazon Elastic Block Storage encryption offers the encryption of EBS data volumes, boot volumes, and snapshots that removes the need to build and manage the secure key infrastructure. The encryption occurs on servers that host EC2 instances. Thus providing encryption of data as it moves between the EC2 instances and EBS. Amazon EC2 is integrated with the AWS Identity and Access Management(IAM) which enables access control to the Amazon EBS volumes for security reasons.

**It provides Elastic Volumes.**

Amazon Elastic Block offers Elastic Volumes which allows the user to create storage and attach them to Amazon EC2. The Elastic Volume dynamically increases capacity and tune performances. It works with no downtime or performance impact.

**It provides the Amazon Elastic Block Storage Snapshots.**

Amazon Elastic Block Storage offers point-in-time snapshots of EBS volumes for long-term durability. They are further backed up to the Amazon S3. Amazon EBS Snapshots are simple and automated. Further Amazon EBS Snapshots are automatically saved to the Amazon Simple Storage Service (Amazon S3) for long-term retention. Also, AWS further simplifies the lifecycle management of users snapshots through the integration with the DLM which further allows users to create policies so that users can automate multiple tasks including creation, deletion, retention, and sharing of the snapshots.



## Architecture


This is the basic structure of **AWS EC2**, where **EC2** stands for Elastic Compute Cloud. EC2 allow users to use virtual machines of different configurations as per their requirement. It allows various configuration options, mapping of individual server, various pricing options, etc. We will discuss these in detail in AWS Products section. Following is the diagrammatic representation of the architecture.

![EBS architecture](https://www.tutorialspoint.com/amazon_web_services/images/architecture.jpg)

<![endif]-->

In the above diagram **S3** stands for Simple Storage Service. It allows the users to store and retrieve various types of data using API calls. It doesn’t contain any computing element. We will discuss this topic in detail in AWS products section.



## Amazon EBS Benefits



**Reliable and secure storage**  − Each of the EBS volume will automatically respond to its Availability Zone to protect from component failure.

**Secure**  − Amazon’s flexible access control policies allows to specify who can access which EBS volumes. Access control plus encryption offers a strong defense-in-depth security strategy for data.

**Higher performance**  − Amazon EBS uses SSD technology to deliver data results with consistent I/O performance of application.

**Easy data backup**  − Data backup can be saved by taking point-in-time snapshots of Amazon EBS volumes.



## Technical Details



**EBS Volume Types**

Following are the three types.

**EBS General Purpose (SSD)**

This volume type is suitable for small and medium workloads like Root disk EC2 volumes, small and medium database workloads, frequently logs accessing workloads, etc. By default, SSD supports 3 IOPS (Input Output Operations per Second)/GB means 1 GB volume will give 3 IOPS, and 10 GB volume will give 30 IOPS. Its storage capacity of one volume ranges from 1 GB to 1 TB. The cost of one volume is $0.10 per GB for one month.

**Provisioned IOPS (SSD)**

This volume type is suitable for the most demanding I/O intensive, transactional workloads and large relational, EMR and Hadoop workloads, etc. By default, IOPS SSD supports 30 IOPS/GB means 10GB volume will give 300 IOPS. Its storage capacity of one volume ranges from 10GB to 1TB. The cost of one volume is $0.125 per GB for one month for provisioned storage and $0.10 per provisioned IOPS for one month.

**EBS Magnetic Volumes**

It was formerly known as standard volumes. This volume type is suitable for ideal workloads like infrequently accessing data, i.e. data backups for recovery, logs storage, etc. Its storage capacity of one volume ranges from 10GB to 1TB. The cost of one volume is $0.05 per GB for one month for provisioned storage and $0. 05 per million I/O requests.

**Volumes Attached to One Instance**

Each account will be limited to 20 EBS volumes. For a requirement of more than 20 EBS volumes, contact Amazon’s Support team. We can attach up to 20 volumes on a single instance and each volume ranges from 1GB to 1TB in size.

In EC2 instances, we store data in local storage which is available till the instance is running. However, when we shut down the instance, the data gets lost. Thus, when we need to save anything, it is advised to save it on Amazon EBS, as we can access and read the EBS volumes anytime, once we attach the file to an EC2 instance.

<![endif]-->

**

## Additional Info

**

In a typical use case, using EBS would include formatting the device with a filesystem and mounting it. EBS supports advanced storage features, including snapshotting and cloning. As of September 2020, EBS volumes can be up to 2 TIB in size using the MBR partitioning scheme, and up to 16 TiB using the GPT partitioning scheme.

EBS volumes are built on replicated back end storage, so that the failure of a single component will not cause data loss.
