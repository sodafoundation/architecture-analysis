                         
                  
  ![](https://lh4.googleusercontent.com/_nTl76hzkp3qjo453k2pb-hQ1pxys2kSXkSSLKpt3pNRMYJ--AOOXySO6emr1O8YzChYxbHdjR4hgjZk7O0WM5a8-kdJ1DJYG4tCk_CPZfHhTibcjGONCRrt557X8riUt6NpVPPwZgnQ3S0TcA0Myt4)
 ## Project Analysis

  

#### INTRODUCTION

The Lustre® file system is an open-source, parallel file system that supports many requirements of leadership class HPC simulation environments.It is  generally used for large-scale [cluster computing](https://en.wikipedia.org/wiki/Cluster_computing).It is highly scalable and can support many thousands of client nodes, petabytes of storage, and hundreds of gigabytes per second of I/O throughput.

Lustre is purpose-built to provide a coherent, global POSIX-compliant namespace for very large scale computer infrastructure, including the world's largest supercomputer platforms. It can support hundreds of petabytes of data storage and hundreds of gigabytes per second in simultaneous, aggregate throughput. Some of the largest current installations have individual file systems in excess of fifty petabytes of usable capacity, and have reported throughput speeds exceeding one terabyte/sec.

  

## Project Summary

  
<table class="c39"><tbody><tr class="c20"><td class="c35" colspan="1" rowspan="1"><p class="c3"><span class="c2">Website</span></p></td><td class="c35" colspan="1" rowspan="1"><p class="c3"><span class="c18 c13"><a class="c14" href="https://www.google.com/url?q=https://www.lustre.org/&amp;sa=D&amp;source=editors&amp;ust=1676836119680120&amp;usg=AOvVaw0ecpHxAF1LBueuQstiTfwx">https://www.lustre.org/</a></span></p></td></tr><tr class="c20"><td class="c35" colspan="1" rowspan="1"><p class="c3"><span class="c18 c22">Lustre</span><span class="c2">&nbsp;Repository </span></p></td><td class="c35" colspan="1" rowspan="1"><p class="c3"><span class="c13 c18"><a class="c14" href="https://www.google.com/url?q=https://git.whamcloud.com/&amp;sa=D&amp;source=editors&amp;ust=1676836119680874&amp;usg=AOvVaw0AexNdC1aOfLcxP-7yXXdQ">https://git.whamcloud.com/</a></span><span class="c2">&nbsp;</span></p></td></tr><tr class="c20"><td class="c35" colspan="1" rowspan="1"><p class="c3"><span class="c2">Organization</span></p></td><td class="c35" colspan="1" rowspan="1"><p class="c3"><span class="c18 c13"><a class="c14" href="https://www.google.com/url?q=http://www.opensfs.org/&amp;sa=D&amp;source=editors&amp;ust=1676836119681553&amp;usg=AOvVaw3z6cR1HRGiP3_wTLsN23_4">OpenSFS</a></span><span class="c18 c22">&nbsp;and </span><span class="c18 c13"><a class="c14" href="https://www.google.com/url?q=http://www.eofs.org/&amp;sa=D&amp;source=editors&amp;ust=1676836119681792&amp;usg=AOvVaw3x4h_oWUDNUlCoxusps6lt">EOFS</a></span></p></td></tr><tr class="c20"><td class="c35" colspan="1" rowspan="1"><p class="c3"><span class="c2">License</span></p></td><td class="c35" colspan="1" rowspan="1"><p class="c3"><span class="c18 c13"><a class="c14" href="https://www.google.com/url?q=https://en.wikipedia.org/wiki/GNU_General_Public_License&amp;sa=D&amp;source=editors&amp;ust=1676836119682566&amp;usg=AOvVaw3LrW66JCz5RI2j7uT1mISY">GPL v2</a></span><span class="c18 c22">&nbsp;, </span><span class="c18 c13"><a class="c14" href="https://www.google.com/url?q=https://en.wikipedia.org/wiki/GNU_Lesser_General_Public_License&amp;sa=D&amp;source=editors&amp;ust=1676836119683102&amp;usg=AOvVaw3WpM5DJutHy5yG53YE1BG5">LGPL</a></span></p></td></tr><tr class="c20"><td class="c35" colspan="1" rowspan="1"><p class="c3"><span class="c2">Open/Proprietary</span></p></td><td class="c35" colspan="1" rowspan="1"><p class="c3"><span class="c2">Open</span></p></td></tr><tr class="c20"><td class="c35" colspan="1" rowspan="1"><p class="c3"><span class="c2">Source code</span></p></td><td class="c35" colspan="1" rowspan="1"><p class="c3"><span class="c18 c13"><a class="c14" href="https://www.google.com/url?q=https://git.whamcloud.com/?p%3Dfs/lustre-release.git;a%3Dsummary&amp;sa=D&amp;source=editors&amp;ust=1676836119684762&amp;usg=AOvVaw08VR3AwzTNC8V5g-8cDRuo">https://git.whamcloud.com/?p=fs/lustre-release.git;a=summary</a></span></p><p class="c3 c34"><span class="c2"></span></p><p class="c3"><span class="c18 c13"><a class="c14" href="https://www.google.com/url?q=https://github.com/lustre/lustre-release&amp;sa=D&amp;source=editors&amp;ust=1676836119685576&amp;usg=AOvVaw3Jttm8seeY6O3QV9gvrt2F">https://github.com/lustre/lustre-release</a></span><span class="c18 c22">&nbsp;(Github mirror)</span></p></td></tr><tr class="c20"><td class="c35" colspan="1" rowspan="1"><p class="c3"><span class="c2">Documentation</span></p></td><td class="c35" colspan="1" rowspan="1"><p class="c3"><span class="c18 c13"><a class="c14" href="https://www.google.com/url?q=https://doc.lustre.org/lustre_manual.xhtml%23part.intro&amp;sa=D&amp;source=editors&amp;ust=1676836119686732&amp;usg=AOvVaw39f3-WbM0aco3CGzP9y47t">https://doc.lustre.org/lustre_manual.xhtml#part.intro</a></span></p></td></tr></tbody></table>

  

   ## PROJECT DETAILS

**Lustre Features**

• POSIX Compliance: With few exceptions, Lustre passes the full POSIX test suite. Most operations are atomic to ensure that clients do not see stale data or metadata. Lustre also supports mmap() file IO.

• Online file system checking: Lustre provides a file system checker (LFSCK) to detect and correct file system inconsistencies. LFSCK can be run while the file system in online and in production, minimizing potential downtime.

• Controlled file layouts: The file layouts that determine how data is placed across the Lustre servers can be customized on a per-file basis. This allows users to optimize the layout to best fit their specific use case.

• Support for multiple backend file systems: When formatting a Lustre file system, the underlying storage can be formatted as either ldiskfs (a performance-enhanced version of ext4) or ZFS.

• Support for high-performance and heterogeneous networks: Lustre can utilize RDMA over low latency networks such as Infiniband or Intel OmniPath in addition to supporting TCP over commodity networks. The Lustre networking layer provides the ability to route traffic between multiple networks making it feasible to run a single site-wide Lustre file system.

• High-availability: Lustre supports active/active failover of storage resources and multiple mount protection (MMP) to guard against errors that may result from mounting the storage simultaneously on multiple servers. High availability software such as Pacemaker/Corosync can be used to provide automatic failover capabilities.

• Security features: Lustre follows the normal UNIX file system security model enhanced with POSIX ACLs. The root squash feature limits the ability of Lustre clients to perform privileged operations. Lustre also supports the configuration of Shared-Secret Key (SSK) security.

• Capacity growth: File system capacity can be increased by adding additional storage for data and metadata while the file system is online.

  
  


<table class="c39"><tbody><tr class="c20"><td class="c16" colspan="1" rowspan="1"><p class="c3"><span class="c4 c28">Dates recorded</span></p></td><td class="c19" colspan="1" rowspan="1"><p class="c3"><span class="c10">modification (mtime), attribute modification (ctime), access (atime), delete (dtime), create (crtime)</span></p></td></tr><tr class="c20"><td class="c16" colspan="1" rowspan="1"><p class="c3"><span class="c4 c28">Date range</span></p></td><td class="c19" colspan="1" rowspan="1"><p class="c3"><span class="c10">2^34 bits (ext4), 2^64 bits (ZFS)</span></p></td></tr><tr class="c20"><td class="c16" colspan="1" rowspan="1"><p class="c3"><span class="c4 c28">Date resolution</span></p></td><td class="c19" colspan="1" rowspan="1"><p class="c3"><span class="c10">1 s</span></p></td></tr><tr class="c20"><td class="c16" colspan="1" rowspan="1"><p class="c3"><span class="c4 c28">Forks</span></p></td><td class="c19" colspan="1" rowspan="1"><p class="c3"><span class="c15">No</span></p></td></tr><tr class="c20"><td class="c16" colspan="1" rowspan="1"><p class="c3"><span class="c4 c28">Attributes</span></p></td><td class="c19" colspan="1" rowspan="1"><p class="c3"><span class="c10">32bitapi, acl, checksum, flock, lazystatfs, localflock, lruresize, noacl, </span><span class="c10">nochecksum</span><span class="c10">, noflock, </span><span class="c10">nolazystatfs</span><span class="c10">, nolruresize, nouser_fid2path, nouser_xattr, user_fid2path, user_xattr</span></p></td></tr><tr class="c20"><td class="c16" colspan="1" rowspan="1"><p class="c3"><span class="c4 c12"><a class="c14" href="https://www.google.com/url?q=https://en.wikipedia.org/wiki/File_system_permissions&amp;sa=D&amp;source=editors&amp;ust=1676836119694511&amp;usg=AOvVaw2ugNvy-f26DHXfY27uxhk8">File system permissions</a></span></p></td><td class="c19" colspan="1" rowspan="1"><p class="c3"><span class="c10 c38"><a class="c14" href="https://www.google.com/url?q=https://en.wikipedia.org/wiki/POSIX&amp;sa=D&amp;source=editors&amp;ust=1676836119695288&amp;usg=AOvVaw2YWStf2yjX_eRYc_qMwSKr">POSIX</a></span><span class="c10">, </span><span class="c10 c38"><a class="c14" href="https://www.google.com/url?q=https://en.wikipedia.org/wiki/Access_Control_List&amp;sa=D&amp;source=editors&amp;ust=1676836119695712&amp;usg=AOvVaw3fs9inud33AIwcIvhrHPDb">POSIX.1e ACL</a></span><span class="c10">, </span><span class="c10 c38"><a class="c14" href="https://www.google.com/url?q=https://en.wikipedia.org/wiki/Security_Enhanced_Linux&amp;sa=D&amp;source=editors&amp;ust=1676836119696134&amp;usg=AOvVaw3OrKKfTj3dCtxGHFOkwFMy">SELinux</a></span></p></td></tr><tr class="c20"><td class="c16" colspan="1" rowspan="1"><p class="c3"><span class="c4 c28">Transparent compression</span></p></td><td class="c19" colspan="1" rowspan="1"><p class="c3"><span class="c10">Yes (ZFS only)</span></p></td></tr><tr class="c20"><td class="c16" colspan="1" rowspan="1"><p class="c3"><span class="c4 c12"><a class="c14" href="https://www.google.com/url?q=https://en.wikipedia.org/wiki/Filesystem-level_encryption&amp;sa=D&amp;source=editors&amp;ust=1676836119697754&amp;usg=AOvVaw2D5BnsKOR5MkW3mm_pHpdF">Transparent encryption</a></span></p></td><td class="c19" colspan="1" rowspan="1"><p class="c3"><span class="c10">Yes (network, storage with ZFS 0.8+, </span><span class="c10">fscrypt</span><span class="c10">&nbsp;with </span><span class="c10">Lustre</span><span class="c10">&nbsp;2.14.0+)</span></p></td></tr><tr class="c20"><td class="c16" colspan="1" rowspan="1"><p class="c3"><span class="c4 c12"><a class="c14" href="https://www.google.com/url?q=https://en.wikipedia.org/wiki/Data_deduplication&amp;sa=D&amp;source=editors&amp;ust=1676836119699077&amp;usg=AOvVaw3DKGzw7k5XcCQlkM6izyEA">Data deduplication</a></span></p></td><td class="c19" colspan="1" rowspan="1"><p class="c3"><span class="c10">Yes (ZFS only)</span></p></td></tr><tr class="c20"><td class="c16" colspan="1" rowspan="1"><p class="c3 c44"><span class="c4">Copy-on-write</span></p></td><td class="c19" colspan="1" rowspan="1"><p class="c3 c44"><span class="c10">Yes (ZFS only)</span></p></td></tr></tbody></table>


**Lustre Components**

• Management Server (MGS) - Provides configuration information for the file system. When mounting the file system, the Lustre clients will contact the MGS to retrieve details on how the file system is configured (what servers are part of the file system, failover information, etc.). The MGS can also proactively notify clients about changes in the file system configuration and plays a role in the Lustre recovery process.

• Management Target (MGT) - Block device used by the MGS to persistently store Lustre file system configuration information. It typically only requires a relatively small amount of space (on the order to 100 MB).

• Metadata Server (MDS) - Manages the file system namespace and provides metadata services to clients such as filename lookup, directory information, file layouts, and access permissions. The file system will contain at least one MDS but may contain more.

• Metadata Target (MDT) - Block device used by an MDS to store metadata information. A Lustre file system will contain at least one MDT which holds the root of the file system, but it may contain multiple MDTs. Common configurations will use one MDT per MDS server, but it is possible for an MDS to host multiple MDTs. MDTs can be shared among multiple MDSs to support failover, but each MDT can only be mounted by one MDS at any given time.

• Object Storage Server (OSS) - Stores file data objects and makes the file contents available to Lustre clients. A file system will typically have many OSS nodes to provide a higher aggregate capacity and network bandwidth.

  

**Architecture**

![](https://lh6.googleusercontent.com/uOs89dW4DCkhXmVVGCv43UGSmuxkZnt2pojR6BGGI3zpq-Xh161lcMEQYC7L8_B-gC8PalT1jDDOMcxXHXEdkoOQhb1OFZCPI0Sl78SvS6IF5bMd3fgK1doKXceygDHvdIuIU-BAxkZhlU3EA6OGu9E)

**Lustre File System Users**

  

Due to the size of their machines and I/O requirements, early adopters of Lustre were the Department of Energy National Laboratories including Lawrence Livermore, Sandia, Oak Ridge and, more recently, Los Alamos’ Cielo supercomputer is supported by the Lustre file system.

## Lustre-Current Projects

  

-   [Directory bulk operations](https://wiki.lustre.org/index.php?title=Directory_bulk_operations&action=edit&redlink=1)
    
-   [File Level Redundancy (FLR) - Phase 3 Erasure Coding](https://wiki.lustre.org/File_Level_Redundancy_Solution_Architecture#Phase_4:_Erasure_Coded_Striped_Files)
    
-   [Metadata Writeback Cache](https://wiki.lustre.org/Metadata_Writeback_Cache)
    
-   [Enhanced Adaptive Compression in Lustre](https://wiki.lustre.org/Enhanced_Adaptive_Compression_in_Lustre)
    

## Lustre-Future Projects

-   [RTDS(Real-Time Dynamic Striping)](https://wiki.lustre.org/index.php?title=RTDS(Real-Time_Dynamic_Striping)&action=edit&redlink=1)
    
-   [File Level Redundancy (FLR) - Phase 2 Immediate Write Mirroring](https://wiki.lustre.org/File_Level_Redundancy_Solution_Architecture#Phase_2:_Immediate_Redundancy_and_Optimized_Resync)
    
-   [External HSM Coordinator](https://wiki.lustre.org/index.php?title=External_HSM_Coordinator&action=edit&redlink=1)
    
-   [HSM control of data migration](https://wiki.lustre.org/index.php?title=HSM_control_of_data_migration&action=edit&redlink=1)
