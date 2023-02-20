**GLUSTER**

-  **Red Hat Software**

Gluster, which is also known as the GlusterFS (Gluster File System) is an open-source Distributed File System that can scale out in building-block fashion to store multiple petabytes of data. The company was privately funded and headquartered in Sunnyvale, California with an engineering center in Bangalore, India. Gluster was funded by Nexus Venture Partners and Index Ventures. Gluster was acquired by Red Hat on October 7, 2011. Linux. In 2011, Red Hat Inc. acquired Gluster Inc., which developed GlusterFS. The vendor supports a commercial version of GlusterFS, which ships with its Red Hat Gluster Storage product. An open-source version of GlusterFS is still updated and maintained by a community of developers.

P.  S. : A petabyte is a measure of memory or data storage capacity that is equal to 2 to the 50th power of bytes. 


**Website** | [https://www.gluster.org/](https://www.gluster.org/)
-------- | -----
**Organization Name**  | Red Hat Software
**License** | Other (GPLv2 or LGPLv3+)
**Open/Proprietary** | Open Source
**Source Path**|https://github.com/gluster/glusterfs.git

**Key features:**

•  **Storage management that scale**

Using an elastic hashing algorithm, Red Hat Gluster Storage stores data without the need for a metadata server, eliminating any single point of failure. Red Hat Gluster Storage scales as you grow, so you can respond to business changes without creating a standalone storage environment for each new project.

**•**  **Fast, simple, and no vendor lock-in**

Deploy a multi-tenant storage system in a few minutes—then scale it in a few seconds. Red Hat Gluster Storage is based on the open source GlusterFS project and stabilized by Red Hat so enterprises get a reliable storage system suited for unstructured, semi structured, and big data storage requirements. Unlike proprietary storage solutions, Red Hat Gluster Storage doesn't tie you to rigid innovation cycles or specific hardware configurations.

**•** **Optimized for containers**

Containers package code and runtime dependencies for greater application portability across deployment environments—but they're ephemeral by design. Red Hat Gluster Storage gives containers persistent storage that's scalable and highly integrated with Openshift.

**•** **Large-scale media**

Storage needs for media and entertainment are outpacing capacity and budgets. The transition to digital workflows and content has increased demand for digital storage. With lower costs than proprietary storage vendors and the ability to scale on demand, Red Hat Gluster Storage is ideal for the media industry.

**•** **Virtualization**

Hyperconvergence—IT architectures provided by 1 vendor that uses software to bind compute, storage, and virtualization resources on commodity hardware—is gaining traction. Integrating Red Hat Gluster Storage with Red Hat Virtualization helps you build cost-effective, agile, and optimal virtual server and storage infrastructures across heterogeneous hardware platforms.

**Architecture:**

A gluster volume is a collection of servers belonging to a trusted storage pool. management daemon (glusterd) runs on each server and manages a brick process (glusterfs) which in turn exports the underlying on disk storage (XFS file system). The client process mounts the volume and exposes the storage from all the bricks as a single unified storage namespace to the applications accessing it. The client and brick processes stacks have various translators loaded in them. I/O from the application is routed to different bricks via these translators.

P.S. XFS is a high performance 64-bit journaling file system created by Silicon Graphics(which comes with Red Hat Software).

![Architecture of Gluster](https://docs.gluster.org/en/main/images/GlusterFS_Translator_Stack.png)

**Current Usage:**

Use cases for GlusterFS include cloud computing, streaming media and content delivery. Scale-out storage systems based on GlusterFS are suitable for unstructured data such Use cases for GlusterFS include cloud computing, streaming media and content such as documents, images, audio and video files, and log files. GlusterFS essentially creates a traditional cloud infrastructure. Clients connected to a network will have access to the storage space that’s been made available. Such an approach is well-suited for massive networks with plenty of resources to spare for the creation of an organized internetworking system.

• 63 companies that use red hat gluster storage. The companies using red hat gluster storage are most often found in the United States and in the Information Technology and Services industry.

**Company** | **Website**|**Country**|**Revenue**
-------- | ----- | -----|----
 **U.S. Department of Agriculture**| usda.gov |United States|>1000M
**Persistent Systems, LLC** |persistentsystems.com |United States|1M-10M
**Red Hat Inc** | redhat.com| United States|>1000M
**Quad City Times** | qctimes.com |United States| 50M-100M



**Technical Details:**

**➢** **Performance**

It is intended to complement use of iozone benchmark for measuring performance of large-file workloadsIt is intended to complement use of iozone benchmark for measuring performance of large-file workloads.

**➢** **Scalability**

GlusterFS is a scalable network filesystem suitable for data-intensive tasks such as cloud storage and media streaming.

**➢** **Advantages of Gluster**

❖  Optimum use of currently available resources

❖  Improved trustworthiness

❖  Distributing the work on the network

❖  Excellent ability to grow

**➢** **Disadvantages of Gluster**

❖ Design and construction of a sophisticated network structure.

❖  Added administrative work required during the setup process

❖ We require immediate network infrastructure.

❖  Technical security necessitates additional effort.

**GlusterFS vs. Ceph**

In 2014, Red Hat acquired Inktank Storage, the maker of Ceph open-source software. Red Hat supports commercial versions of both GlusterFS and Ceph, but leaves development work on each project to the open-source community.

Red Hat's acquisition of Inktank Storage and Ceph does not duplicate the functionality of GlusterFS in the company's product line. Rather, Ceph adds object and block-based storage to Gluster's file storage. For Red Hat customers, GlusterFS meets archiving needs, while Ceph is used in many OpenStack implementations. Ceph is aimed at IT shops that build large-scale clouds, while GlusterFS is for customers who just need a file system.

**Top 10 Alternatives to GlusterFS**

• Apache Karaf

• REX-Ray

• Diego

• Ondat

• Hedvig

• Apache Edgent

• OpenEBS

• Apache ServiceMix

**References:**

[https://www.gluster.org/](https://www.gluster.org/) :  Gluster’s Official website

[https://docs.gluster.org/en/main/Quick-Start-Guide/Architecture/](https://docs.gluster.org/en/main/Quick-Start-Guide/Architecture/) : Gluster’s Architecture

[https://docs.gluster.org/en/latest/](https://docs.gluster.org/en/latest/) : Gluster documentation
