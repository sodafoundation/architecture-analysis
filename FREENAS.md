# FREENAS

**INTRODUCTION:** The FreeNAS Project is an [open source](https://www.techtarget.com/whatis/definition/open-source)  [storage](https://www.techtarget.com/searchstorage/definition/storage) operating system ([OS](https://www.techtarget.com/whatis/definition/operating-system-OS)) that allows the sharing of storage over a [network](https://www.techtarget.com/searchnetworking/definition/network). It was created in 2005 and is based on the open source FreeBSD OS and the OpenZFS OS.

FreeNAS [software](https://www.techtarget.com/searchapparchitecture/definition/software) can be downloaded at no cost from freenas.org, and runs on most x86-64 [commodity](https://www.techtarget.com/whatis/definition/commodity) hardware. It supports [iSCSI](https://www.techtarget.com/searchstorage/definition/iSCSI) and file transfer protocol for [block storage](https://www.techtarget.com/searchstorage/definition/block-storage) or Common Internet File System/Server Message Block ([CIFS](https://www.techtarget.com/searchstorage/definition/Common-Internet-File-System-CIFS)/[SMB](https://www.techtarget.com/searchnetworking/definition/Server-Message-Block-Protocol)), AFP or [Network File System](https://www.techtarget.com/searchenterprisedesktop/definition/Network-File-System) for storing files. It supports most major [virtualization](https://searchservervirtualization.techtarget.com/definition/virtualization) platforms, including [Citrix](https://www.techtarget.com/whatis/definition/Citrix), [VMware](https://www.techtarget.com/searchvmware/definition/VMware) and [Microsoft](https://www.techtarget.com/searchwindowsserver/definition/Microsoft).

**Project Summary**
|Website|**/https://www.truenas.com**  |
|--|--|
|**Organisation or Foundation Name**  |ixsystems
| **Liscense** | **BSD Liscense** |
| **Open/Repository** | **Repository** |
| Source Path | NIL |
| **Brief Description** | FreeNAS® first made the leap onto the internet in 2005 and over the last decade, has become a household name with over 10 million downloads and 1 million deployments worldwide. For many years, FreeNAS and TrueNAS® grew alongside each other at iXsystems®— FreeNAS as the community-supported software edition and TrueNAS as the enterprise edition for mission-critical storage applications. The two were maintained separately despite having much in common, including a near-identical codebase. |

  |

## KEY FEATURES:

Encryption: FreeNAS features 256-bit [encryption](https://www.techtarget.com/searchsecurity/definition/encryption), which only protects drivers from being read if they're physically removed from the system. This means the encryption feature does not protect data in transit.

Snapshots and replication: [Snapshots](https://www.techtarget.com/searchdatabackup/definition/storage-snapshot) of the entire file system can be scheduled or made upon request so the system can be rolled back to the most recent snapshot at any given time. Snapshots can be viewed and deleted or [cloned](https://www.techtarget.com/searchnetworking/definition/clone) using the FreeNAS Web interface. Snapshots can also be replicated to a remote ZFS file system for [disaster recovery](https://www.techtarget.com/searchdisasterrecovery/definition/disaster-recovery).

File sharing: Most major OSes can connect to FreeNAS using SMB shares with little additional configuration. In iSCSI environments, VMware vStorage APIs for Array Integration ([VAAI](https://www.techtarget.com/searchvmware/definition/vStorage-APIs-for-Array-Integration-VAAI)), Microsoft [ODX](https://www.techtarget.com/searchstorage/definition/ODX-Offloaded-Data-Transfer), and [Windows Server 2008](https://www.techtarget.com/searchwindowsserver/definition/Windows-Server-2008-R2) or 2012 R2 [clustering](https://www.techtarget.com/whatis/definition/cluster) is also supported.

## Installing and configuring FreeNAS

To install FreeNAS, developers recommend using a [USB](https://www.techtarget.com/searchstorage/definition/USB-drive) flash drive with at least 8 gigabytes ([GB](https://www.techtarget.com/searchstorage/definition/gigabyte)) of storage or booting from a burned CD. Once installed, users must point a Web [browser](https://www.techtarget.com/whatis/definition/browser) to the provided [IP address](https://www.techtarget.com/whatis/definition/IP-address) to get FreeNAS running. It can be installed on physical hardware or a [virtual machine](https://searchservervirtualization.techtarget.com/definition/virtual-machine).

FreeNAS supports most physical hardware. Recommended hardware requirements include a 64-bit processor, 16 GB boot drive, 16 GB RAM, at least 2 direct-attached disks and at least 1 physical network port. Pre-configured and pre-certified [servers](https://www.techtarget.com/whatis/definition/server) or storage can also be purchased from iXsystems. The FreeNAS 1U, 2U or 4U storage appliances ship with 32 terabytes ([TB](https://www.techtarget.com/searchstorage/definition/terabyte)), 96 TB and 192 TB of serial ATA ([SATA](https://www.techtarget.com/searchstorage/definition/Serial-ATA)) and serial-attached SCSI (SAS) hard drives, respectively, and are pre-installed with the FreeNAS OS.

IXsystems also offers the TrueNAS unified storage platform, which offers hybrid [flash](https://www.techtarget.com/searchstorage/definition/flash-storage) or all-flash storage. There are four TrueNAS configurations with varying capacities of 320 TB, 888 TB, 1.54 petabytes ([PB](https://www.techtarget.com/searchstorage/definition/petabyte)) and 30 TB.
  

## **Architecture:**

Technical Details:

FreeNAS relies on ZFS and RAID-Z. Like RAID 5, it protects against a hard disk failure, but provides higher performance through the Copy-On-Write function. In addition, RAID-Z2 and RAID-Z3 are available, offering a double or triple redundancy. Each ZFS file system is provided with checksums for data integrity.

### Snapshots

Because of the ZFS file system, snapshots can be taken at any time. They can be triggered either manually or cronjob via the web interface. The entire file system can be rolled back to the latest snapshot at any time. The web interface shows the memory usage of the snapshots, they can be deleted, cloned or even rolled back.

### Replication

ZFS snapshots can not only be stored locally as a backup, remote storage is also possible. A complete duplicate is created when replicating to a remote ZFS file system. Additional snapshots of the filesystem can also be transferred incrementally, which reduces the size of each backup, since only changes are saved.

### Encryption

FreeNAS is the first and only open source solution to support ZFS volume encryption. Full disk encryption with AES-XTS is possible when creating the volume. Hardware acceleration of encryption is possible for processors with AES-NI function.
### Backup functions

FreeNAS offers the following backup functionalities:

-   Windows Backup
    
-   rsync
    
-   Apple Time Machine
    
-   TrueOS Life Preserver
    

### Plugins

FreeNAS offers the possibility to add additional functions via plugins. This means, for example, that the well-known backup solution Bacula or cloud software such as Nextcloud or Owncloud can be supplemented.

## OTHER INFORMATIONS

  

**Core Services Configuration**

FreeNAS provides built-in support for a variety of network file-based protocols, including CIFS and NFS. CIFS (Common Internet File System) is commonly used in Windows environments or those with a mixture of Windows and Unix-like operating systems. NFS (Network File System) is commonly used for high-performance applications and in heterogeneous environments. FreeNAS also supports AFP for Mac OS X environments, including Time Machine backups. Each service can be configured from its corresponding wrench icon (Figure 2).
The data to be shared is configured through the Sharing section of the web UI. ZFS datasets allow for complex sharing scenarios, enabling the administrator to tailor a configuration to meet the network’s needs, such as user/group quotas, user/group permissions, data properties, and customized snapshot and backup requirements.

For block-based applications, FreeNAS supports iSCSI target mode for file extents, physical disks, or zvols, a ZFS feature that creates a virtual disk device for which the blocks are stored in the parent filesystem. Regardless of which filesystem the target is formatted, shared block devices will adhere to the snapshot and backup policies on the storage side, with no interaction required from clients. CHAP and mutual CHAP are included for target and initiator authentication.

Built-in directory services support for Active Directory, LDAP, and NIS allows large organizations to integrate FreeNAS into their existing infrastructure. Other built-in services include UPS support, SNMP, and SSH as remote management alternatives, as well as rsync for more specialized replication and backup scenarios.

**Save the Data: FreeNAS Backups**

FreeNAS supports a wide variety of backup methods. As just mentioned, the rsync service provides built-in incremental backup capability. iSCSI extents can be used with native backup software on applicable platforms. A Mac OS X client can use its FreeNAS AFP share as a Time Machine target; finally, any network share can be used as a destination for an administrator’s scripts or programs.

ZFS snapshots also provide a powerful tool for preserving and recovering older versions of data. ZFS snapshots preserve all the blocks in use by files at the time of the snapshot and can be sent to or received from other ZFS-supporting hosts (using SSH or another transport method) as a way of replicating or restoring data from a known point in time.

Snapshots take up only as much space as the metadata used to describe them and, of course, that allocated to the “older blocks” themselves. If you need to recover files from an older snapshot, you can easily clone and share it. If the current version of a filesystem becomes totally useless, the entire filesystem can also be rolled back to the state of a previous snapshot. These capabilities give admins a wide variety of options in creating data preservation and recovery policies.

FreeNAS supports scheduled snapshots in increments as small as five minutes, with preservation times of as little as an hour (Figure 3).
As previously mentioned, snapshots can also be scheduled to replicate to a compatible remote system running the same version of ZFS. Snapshot replication only transmits the differences since the last snapshot, conserving bandwidth.

**Great Responsibility: A ZFS Primer**

A good working knowledge of ZFS is critical to deploying FreeNAS successfully in an enterprise environment. Poorly configured ZFS pools can experience suboptimal performance. Conversely, a well-planned deployment that takes best advantage of ZFS’s features can be a definite success.

A ZFS filesystem resides in a pool, which may contain one or more groups of devices called vdevs. The possible types of vdev are individual disks, mirrors, RAID-Z1, RAID-Z2, and RAID-Z3 sets. All of the component groups of a ZFS pool are combined in a manner similar to striping, although the distribution of blocks is not a stripe in the strictest sense of traditional hardware RAID. This means that if even a single group within the pool is damaged beyond its tolerance for recovery, the entire pool is faulted. Mirroring and parity arrangements are only possible within groups, not among them. The upshot is that there is no equivalent of RAID 0+1 or 51 in ZFS – only device arrangements similar to RAID 10, 50, or 60.

By default, ZFS will use any idle RAM in the system as the primary Adaptive Replacement Cache (ARC). A common rule of thumb for ARC is to allow 1GB of RAM for each 1TB of data the system will store. Although solid state drives represent the leading edge of performance in data storage today, they do not yet have the capacity (or cost efficiency) to replace HDDs entirely. ZFS can therefore take best advantage of the higher performance of SSDs as secondary ZFS Intent Log (ZIL) and Level 2 Adaptive Replacement Cache (L2ARC) devices.

Such L2ARC devices can provide more space for caching and significantly improve read latency and throughput, especially random reads. In scenarios where an L2ARC device is used, you’ll want to try to match the size of the device to the size of the working set (data that will be read frequently). ZFS supports multiple L2ARC devices, and L2ARC devices may be safely added or removed using the FreeNAS UI. FreeNAS also provides the zilstat command-line utility to help the administrator determine whether the system’s workload would benefit from the addition of an L2ARC.

The ZIL records synchronous writes, fulfilling the guarantee that synchronous writes are committed to stable media before returning success. ZFS also allows the creation of a separate intent log (SLOG) on a separate device or set of devices. The SLOG reduces contention for the disk resources of the pool, but if a low-performing device is used for the ZIL, it can become a performance bottleneck. Ideally, you should choose a flash device with low latency and high throughput. Capacity is not critically important for ZIL devices, because the ZIL is flushed to the pool often (usually around every five seconds). Asynchronous writes do not benefit from ZIL devices.

ZFS supports deduplication, or the block-level removal of duplicate data. Deduplication is extremely resource intensive, and a rule of thumb calls for 5GB of RAM per terabyte of storage to be deduplicated. Think very carefully before using dedup on systems intended to back “live” applications, as it is more suitable for backup devices. As an alternative to deduplication, compression can be configured on ZFS pools or datasets.

The default compression method, LZ4, offers nearly real time compression rates. Deduplication and compression share a caveat: Blocks written to a pool where deduplication or compression are enabled will remain that way until the blocks are deleted. Thus, even temporarily enabling deduplication or compression can have long-lasting performance implications, reinforcing the need for proper planning in setting up ZFS and FreeNAS.

All the metadata needed by ZFS is stored in its pool, so a ZFS pool from one system can be exported and then imported by another operating system with a compatible ZFS version. In theory, this means that ZFS pools created on FreeNAS or another system using the same underlying ZFS version can be imported across operating systems and are compatible for the purpose of remote snapshot replication. In practice, you should always test and document such data recovery solutions before making them part of any business continuity or disaster recovery plan.

Encrypted disks are an exception to the rule of portability for ZFS pools. It is recommended that you use another FreeNAS system running the exact same OS (and therefore ZFS) version if the situation calls for mounting an encrypted ZFS pool elsewhere, because encryption methods and key management methodologies can change.

The ZFS Scrubs feature augments the functionality provided by fsck . A ZFS scrub checks every block on the disk, rather than just filesystem metadata. During a scrub, every block is compared with its checksum, which is stored with its block pointer elsewhere on the disk. If the checksum does not match, ZFS will attempt a repair and indicate the number of mismatches that it found in its scrub report. This feature can provide an early indication of a failing disk. Because it reads all the data from disk, a ZFS scrub is an I/O-intensive operation and is best scheduled for low-load periods. Scrubs should be performed regularly; FreeNAS automatically schedules a scrub for every ZFS volume on every fifth Sunday at midnight. You can use the FreeNAS UI to change the schedule as needed.

**Putting It Together**

When planning for any enterprise or business-critical deployment scenario, you must choose between various requirements for performance, data protection, capacity, and future growth, all within the constraints of a given budget. Much of the performance of FreeNAS rests on the parameters of the ZFS pool and the amount of RAM in the system. Certain use cases benefit from faster CPU or more CPU cores, and naturally, the network can also become a bottleneck without the right switch and NIC hardware.

For smaller working sets, more RAM or a fast L2ARC may significantly increase the performance of random, read-heavy workloads. Highly sequential workloads or workloads that often read a large portion of the stored data will benefit less from this approach. On the write side, synchronous write applications (i.e., NFS or iSCSI for virtualization purposes) will benefit from low-latency, high-throughput ZIL devices.

Regardless of the specific application, any FreeNAS deployment can benefit from using an optimal RAID-Z. When using RAID-Z, you should use equal-sized disks in groups of some power of two, plus the parity level of the group. Thus, the optimal RAID-Z1 group sizes are 3, 5, and 9 disks, RAID-Z2 are 4, 6, and 10 disks, and RAID-Z3 are 5, 7, 11 disks. When combining multiple RAID-Z groups in a pool, it is best to have an even number of identical groups to avoid a performance penalty and to allow ZFS to keep block arrangements as even as possible. Finally, like many filesystems, ZFS performance is proportional to the amount of free space. ZFS begins experiencing noticeable performance degradation when the pool is around 80 percent full. Take this into account when planning for future growth.

For networking, FreeNAS supports both Gigabit and 10 Gigabit Ethernet. Network configuration can be performed from the console as well as from the web user interface. FreeNAS also supports a number of link aggregation protocols, including LACP, link failover, and round-robin. Link aggregation requires a properly configured switch or router. The ability to saturate a given network connection depends on the rest of the performance considerations discussed above.

**Setup and Maintenance**

One of the easiest parts of a FreeNAS deployment is the actual installation. By design, the FreeNAS boot disk is separate from the disks in the storage pool. This segregation ensures that operating system upgrades do not threaten storage: In the worst case of a completely failed upgrade, the storage disks will be untouched and may be imported on a fresh FreeNAS instance. The boot disk image is typically written to a USB thumb drive or SSD of at least 2GB in size. Performance of the boot device itself is of almost no concern because FreeNAS runs from memory after boot.

After booting, the FreeNAS console, seen in Figure 4, will display options to configure networking, reset the system, access a root shell, reboot, or shut down.
Once the system has received an IP address from a DHCP server, the address will be displayed on this screen and can be used to access the web interface. The rest of the configuration must be performed through the web interface. The [FreeNAS documentation](http://doc.freenas.org/) contains both a Quick Start Guide and comprehensive instructions for configuring a FreeNAS system.

As the system is configured, a copy of the configuration can be saved at any point in the configuration to the administrator’s system. This lets you easily restore a previously working configuration. Similarly, if the disks are encrypted, make sure to save a copy of the passphrase and keys.

FreeNAS also provides a “GUI Upgrade” option through the web interface. The upgrade process reminds you to download a copy of the configuration database before performing the upgrade. If the upgrade fails, simply reboot and select the previous version of the operating system from the boot menu.

**Beyond NAS: Plugins**

Out of the box, FreeNAS contains many of the most popular services for NAS. Although they cover the vast majority of use cases, additional customization may be required. To that end, FreeNAS includes a plugin system with support for a wide variety of add-on software. Each plugin runs in its own FreeBSD jail, which allows it to be run isolated from the host operating system.

In FreeNAS, jails are installed on individually segregated ZFS datasets. The admin creates transparent mount points to storage outside of the jail’s dataset, all of which can be done from the web UI. The plugin API also allows a plugin to communicate through an IPC interface to the web UI, allowing it to be configured and enabled/disabled as a service without resorting to the command line. Further details of the FreeNAS plugin system can be found in the FreeNAS documentation.
