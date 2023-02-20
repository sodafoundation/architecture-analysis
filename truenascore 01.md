Project Name: Truenascore
> ## History
The FreeNAS project was started in October 2005 by Olivier Cochard-Labbé who based it on the  [m0n0wall](https://en.wikipedia.org/wiki/M0n0wall "M0n0wall")  embedded firewall and  [FreeBSD 6.0](https://en.wikipedia.org/wiki/FreeBSD "FreeBSD"). Volker Theile joined the project in July 2006 and became the project lead in April 2008. In September 2009, the development team concluded that the project, then at release .7, was due for a complete rewrite in order to accommodate modern features such as a  [plug-in](https://en.wikipedia.org/wiki/Plug-in_(computing) "Plug-in (computing)")  architecture. Volker Theile decided that the project best be reimplemented using Debian  [Linux](https://en.wikipedia.org/wiki/Linux "Linux")  and shifted his development efforts to the interim CoreNAS project and eventually  [OpenMediaVault](https://en.wikipedia.org/wiki/OpenMediaVault "OpenMediaVault")  where he continues as the project lead. Cochard-Labbé responded to community objections to "The Debian version of FreeNAS" and resumed activity in the project and oversaw its transfer to FreeNAS user   Developers Daisuke Aoyama and Michael Zoon continued developing FreeNAS 7 as the  [NAS4Free](https://en.wikipedia.org/wiki/NAS4Free "NAS4Free")  project. Meanwhile, iXsystems rewrote FreeNAS with a new architecture based on  [FreeBSD](https://en.wikipedia.org/wiki/FreeBSD "FreeBSD")  8.1, releasing FreeNAS 8 Beta in November 2010  The  [plug-in](https://en.wikipedia.org/wiki/Plug-in_(computing) "Plug-in (computing)")  architecture arrived with FreeNAS 8.2 and FreeNAS versioning was synchronized with FreeBSD for clarity. FreeNAS 8.3 introduced full-disk  [encryption](https://en.wikipedia.org/wiki/Cryptography "Cryptography")  and FreeBSD 9.1-based FreeNAS 9.1 brought an updated  [plug-in](https://en.wikipedia.org/wiki/Plug-in_(computing) "Plug-in (computing)")  architecture that is compatible with the  [TrueOS](https://en.wikipedia.org/wiki/TrueOS "TrueOS")  Warden jail management framework. FreeNAS 9.1 was also the first version of FreeNAS to use the community-supported OpenZFS v5000 with Feature Flags. FreeNAS 9.2, based on FreeBSD 9.2 included performance improvements and introduced a  [REST](https://en.wikipedia.org/wiki/Representational_state_transfer "Representational state transfer")  [API](https://en.wikipedia.org/wiki/Application_programming_interface "Application programming interface")  for remote system administration.FreeNAS 9.3, based on FreeBSD 9.3 introduced a ZFS-based boot device, an initial  [Setup Wizard](https://en.wikipedia.org/wiki/Wizard_(software) "Wizard (software)")  and a high-performance  [in-kernel](https://en.wikipedia.org/wiki/Kernel_(operating_system) "Kernel (operating system)")  [iSCSI](https://en.wikipedia.org/wiki/Internet_SCSI "Internet SCSI")  9.10, based on FreeBSD 10.3-RC3 brought an end to the FreeNAS/FreeBSD synchronized naming and introduced  [Graphite](https://en.wikipedia.org/wiki/Graphite_(software) "Graphite (software)")  monitoring support and experimental support for the  [bhyve](https://en.wikipedia.org/wiki/Bhyve "Bhyve")  hypervisor.
In October 2015, ten years after the original FreeNAS release, FreeNAS 10 ALPHA was released, providing a preview of what would become FreeNAS Corral  [GA](https://en.wikipedia.org/wiki/Software_release_life_cycle#General_availability_.28GA.29 "Software release life cycle")  on March 15, 2017 FreeNAS Corral introduced a new  [graphical user interface](https://en.wikipedia.org/wiki/Graphical_user_interface "Graphical user interface"),  [command-line interface](https://en.wikipedia.org/wiki/Command-line_interface "Command-line interface"), underlying  [middleware](https://en.wikipedia.org/wiki/Middleware "Middleware"),  [container](https://en.wikipedia.org/wiki/Docker_(software) "Docker (software)")  management system and  [virtual machine](https://en.wikipedia.org/wiki/Virtual_machine "Virtual machine")  management system. FreeNAS Corral departs from FreeNAS by providing not only  [NAS](https://en.wikipedia.org/wiki/Network-attached_storage "Network-attached storage")  functionality but also  [hyper-converged](https://en.wikipedia.org/wiki/Hyper-converged_infrastructure "Hyper-converged infrastructure")  functionality thanks to its integrated  [virtual machine](https://en.wikipedia.org/wiki/Virtual_machine "Virtual machine")  support. However, on April 12, 2017 iXsystems announced that FreeNAS Corral would instead be relegated to being a 'Technology Preview', citing issues such as "general instability, lack of feature parity with 9.10 (Jails, iSCSI, etc), and some users experiencing lower performance than expected" and the departure of the project lead. Instead, the decision was made to revert to the existing 9.10 code and bring Corral features to 9.10.3 and further.

In May 2017, iXsystems announced that FreeNAS 11 would be imminently released, which was based on 9.10 but included features such as an update of the FreeBSD operating system,  [virtual machine](https://en.wikipedia.org/wiki/Virtual_machine "Virtual machine")  management, updates to jails, and a new beta user interface along the lines of Corral but based on  [Angular](https://en.wikipedia.org/wiki/Angular_(web_framework) "Angular (web framework)").

In March 2020, iXsystems announced that the 12.0 release will merge the FreeNAS code base with that of their commercial TrueNAS offering. FreeNAS will become TrueNAS CORE while TrueNAS will be renamed TrueNAS Enterprise.This change was made official with the release of TrueNAS 12.0 on October 20, 2020.

In October 2020, iXsystems announced a new product, TrueNAS SCALE would be developed. TrueNAS SCALE would still utilize ZFS, but be based on Debian Linux.[

In February 2022, iX announced that TrueNAS SCALE has reached General Availability quality for their 22.02 release. 
In May 2022, iX announced that TrueNAS CORE, their FreeBSD Based version of TrueNAS, has reached General Availabilty and is suitable for large deployments.
> ## Introduction:
> *****TRUENASCORE**** is the brand for  iXsystems' open source network attached storage platform. It includes the following:
>  **TRUENASCORE** (previously FreeNAS) –  ***a free file server and expandable platform based on* FreeBSD**.
>   **TRUENASCORE** Enterprise – an enterprise file server for commercial use, also based on FreeBSD.
**TRUENASCORE** range includes free public versions (TrueNAS CORE, previously known as FreeNAS), commercial versions (TrueNAS Enterprise), and Linux versions (TrueNAS SCALE). It also oﬀers hardware, from small home systems to large petabyte arrays, based on the above versions.
**TRUENASCORE** supports Windows, macOS and Unix clients and various virtualization hosts such as XenServer and VMware using the SMB, AFP, NFS, iSCSI, SSH, rsync and FTP/TFTP protocols. Advanced TrueNAS features include full-disk encryption and a plug-in architecture for third-party software*
**LICENSE:**BSD license 
Website: truenas.com/truenas-core
 ## project summary
 **website :** https://www.truenas.com/download-truenas-core/
 **organisation/foundation name :** TrueNas Enterprise
 **License:**TrueNAS CORE Software is authorized for use on any TrueNAS Device. TrueNAS Devices can include hardware provided by iXsystems or third parties. TrueNAS Devices may also include virtual machines and cloud instances. TrueNAS CORE software may not be commercially distributed or sold without an addendum license agreement and express written consent from iXsystems.
 **Proprietary**:TrueNAS supports Windows, macOS and Unix clients and various virtualization hosts such as XenServer and VMware using the SMB, AFP, NFS, iSCSI, SSH, rsync and FTP/TFTP protocols. Advanced TrueNAS features include full-disk encryption and a plug-in architecture for third-party software.
 **Open Source** : TrueNAS CORE will always be completely Open Source and free. TrueNAS Enterprise and iXsystems support are available for more critical storage applications. The upgrade from FreeNAS to TrueNAS CORE is as simple as clicking a button in the UI. The upgrade is free and the software is still Open Source.
 **Brief description:**
TrueNAS Open Storage Logo Menu
TrueNAS Open Storage Logo Menu
TrueNAS M-Series TrueNAS CORE
World’s Most Popular Storage OS Free and Open Source
Uniﬁed Storage Built on OpenZFS Installs on Hardware or VMs Community-Supported .
## project details
**Key features**

Every TrueNAS model supports data corruption protection, replication, ﬁle and block protocols, inline storage optimization, thin and thick provisioning, online capacity,expansion, storage controller redundancy, hot spares, and redundant power and cooling.
**Administrative features**
-   Web-based  [graphical user interface](https://en.wikipedia.org/wiki/Graphical_user_interface "Graphical user interface")  with optional SSL encryption
-   Localized into over 20 language
-   Web, console, and  [SSH](https://en.wikipedia.org/wiki/SSH "SSH")  access configurable
-   Plug-in Architecture (see list below)
-   Performance graphing
-   Wide range of configurable alerts and alerting mechanisms, including log emails and reporting notification
-   Downloadable configuration file and encryption keys
-   S.M.A.R.T. disk diagnostics
-   Local certificate management, including Certificate Authority role.
-   2 factor authentication, LDAP, Active Directory, RADIUS, IPSec, Kerberos and other authentication/user management systems supported in FreeBSD and therefore available for TrueNAS (Note: some but not all supported in GUI).

**[File system](https://en.wikipedia.org/wiki/File_system "File system")  **features****

-   Highly resilient  [ZFS](https://en.wikipedia.org/wiki/ZFS "ZFS")  file system with Feature Flags ([OpenZFS](https://en.wikipedia.org/wiki/OpenZFS "OpenZFS")  v5000) and theoretical storage limit of 16 Exabytes. ZFS file system features are fully configurable and include:

-   Compression (including  [lz4](https://en.wikipedia.org/wiki/LZ4_(compression_algorithm) "LZ4 (compression algorithm)")  and  [gzip](https://en.wikipedia.org/wiki/Gzip "Gzip")),
-   Full-volume encryption (Disk encryption with GELI and AESNI hardware acceleration),
-   [Snapshots](https://en.wikipedia.org/wiki/Snapshot_(computer_storage) "Snapshot (computer storage)")  (which can be near-continual; snapshotting every 15-30 minutes is not uncommon),
-   **Data deduplication**
-   User quotas
-   Physical disks are fully portable and can be moved without data loss to other FreeNAS servers, or to any other Operating System that supports a compatible version of OpenZFS.
Data reliability features -  [mirroring](https://en.wikipedia.org/wiki/Disk_mirroring "Disk mirroring")  /  [RAID](https://en.wikipedia.org/wiki/RAID "RAID")  (including ZFS RaidZ), multiple copies of selected data and metadata for reliability, and entire-system  [checksumming](https://en.wikipedia.org/wiki/Checksum "Checksum")  and background data repair as needed ("scrubbing")  _(see also:  [ZFS](https://en.wikipedia.org/wiki/ZFS "ZFS")  generally, which was designed expressly with the aim of ensuring data preservation)_  *Server reliability features -**

-   [**Replication**](https://en.wikipedia.org/wiki/Replication_(computing) "Replication (computing)")  and  **failover**
-   Multi-version boot environment - the boot menu provides access to previous versions of TrueNAS which have been upgraded. In the event of a boot issue or system problem, TrueNAS can also load any of these at boot, as "known working" versions, without "rolling back" the system.

-   Disk read and data import for UFS2, NTFS, FAT32 and EXT2/3
-   User/Group permissions - Classic Unix/Linux permissions and/or ACL based (including ACLs for Microsoft file systems)

**Built-in network services and features**

-   Protocols as standard -  [Samba](https://en.wikipedia.org/wiki/Samba_(software) "Samba (software)")/[SMB](https://en.wikipedia.org/wiki/Server_Message_Block "Server Message Block")/[CIFS](https://en.wikipedia.org/wiki/CIFS "CIFS")  (for  [Microsoft](https://en.wikipedia.org/wiki/Microsoft "Microsoft")  and other networks),  [AFP](https://en.wikipedia.org/wiki/Apple_Filing_Protocol "Apple Filing Protocol")  (Apple),  [NFS](https://en.wikipedia.org/wiki/Network_File_System "Network File System"),  [iSCSI](https://en.wikipedia.org/wiki/ISCSI "ISCSI"),  [FTP](https://en.wikipedia.org/wiki/FTP "FTP")/[TFTP](https://en.wikipedia.org/wiki/TFTP "TFTP")
-   LDAP and Active Directory client support with Windows ACLs
-   Apple Time Machine and Microsoft File History/Previous Versions support
-   [rsync](https://en.wikipedia.org/wiki/Rsync "Rsync")  data sync and replication (server/client)
-   Link aggregation and failover
-   VLAN networking
-   Dynamic DNS client
-   Remote syslogd forwarding
-   SNMP monitoring
-   Wide range of networking hardware and environments supported by FreeBSD, including copper cable, fiberoptic cable, WiFi
-   Supports jumbo frames, hardware offloading (exact features offloaded vary by adapter), high bandwidth servicing (10G+)

**Other**

-   UPS (Uninterruptible power supply) support
-   Virtual Machine host and management with GUI based management
-   Jail management and templates - As of 11.0 release, TrueNAS(Formerly FreeNAS) is part-way switched from warden to iocage as jail manager; full iocage support is planned for 11.1
-   iozone, netperf, OpenVPN, tmux and other utilities
-   Over 20,000 packages and ports available and able to be installed from FreeBSD repositories.
- 

# Archtiecture
![Data Mattsson — Welcome, TrueNAS CORE Container Storage Provider!](https://64.media.tumblr.com/46629f13da2418a682a4986a574693ff/0e087c780dfd255b-c1/s2048x3072/ae5941dc06f82dca1f2341f519f2892981cc09cf.png)
![TrueNAS CORE | The World's Most Popular Open Storage OS](https://www.truenas.com/wp-content/uploads/2022/02/TrueNAS_CORE_Software_Features_Half_Circle_Illustration_2022-1024x599.png)
![Reduce Virtualization Costs with TrueNAS - iXsystems, Inc ...](https://www.ixsystems.com/wp-content/uploads/2019/08/Virtualization-1.png)
 
The 8.0 reimplementation of FreeNAS moved the project from a m0n0BSD/architecture to one based on  [FreeBSD](https://en.wikipedia.org/wiki/FreeBSD "FreeBSD")'s NanoBSD embedded build system, the  [Python](https://en.wikipedia.org/wiki/Python_language "Python language")  programming language, the  [Django](https://en.wikipedia.org/wiki/Django_(web_framework) "Django (web framework)")  web application framework and the  olkit")Lighttp web server, but this was replaced with  [nginx](https://en.wikipedia.org/wiki/Nginx "Nginx")  in FreeNAS 8.2. The terminated successor to 9.10.2, known as FreeNAS Corral, retained the  [nginx](https://en.wikipedia.org/wiki/Nginx "Nginx")  web server and ZFS-based boot device of FreeNAS but replaces the Django/dōjō web application framework with an original one. FreeNAS 11 implemented a new interface using  [Angular](https://en.wikipedia.org/wiki/Angular_(web_framework) "Angular (web framework)").




**current usage**

TrueNAS® CORE (formerly known as FreeNAS®) is the world's most popular storage OS because it gives you the power to build your own professional-grade storage system to use in a variety of data-intensive applications without any software costs.

**Technical details**

Minimum Hardware Requirements Processor	Memory	Boot Device
2-Core Intel 64-Bit or AMD x86_64 processor	8 GB Memory 16 GB SSD boot dev
## other information
**Overview**
 1. **Experience Storage Freedom**
TrueNAS® CORE (formerly known as FreeNAS®) is the world’s most popular storage OS because it gives you the power to build your own professional-grade storage system to use in a variety of data-intensive applications without any software costs. Simply install it onto hardware or a VM and experience the true storage freedom of open source storage.
 2. **Share Files**.
  Serve Media. Store Anything.
TrueNAS CORE can be used from the home to the oﬃce to the data center for a variety of data-intensive use cases. IT professionals, photographers, designers, audio/video producers and editors, developers, and any user serious about storing and protecting lots of data can take advantage of TrueNAS CORE. Pair it with your favorite backup software to archive large quantities of infrequently used data oﬀ of your local devices and even sync and push data to the cloud.
 3. **Nothing Protects Your Data Like ZFS**
At the heart of TrueNAS is the self-healing OpenZFS ﬁlesystem. Previously only available on the highest-end enterprise storage systems, TrueNAS gives you direct, user-friendly access to ZFS. With its built-in RAID, powerful data management tools, and ability to automatically detect and repair silent data corruption (and bit rot), TrueNAS and OpenZFS ensure data integrity from start to ﬁnish.
 4. **Go Beyond “NAS**”
More than a traditional “Network Attached Storage”, TrueNAS is Uniﬁed Storage that integrates seamlessly into any environment with a variety of ﬁle, block, or object access protocols. You can also expand its functionality with a variety of free plugins, like Plex Media Server, NextCloud, Zoneminder surveillance, and many others.
 5. **Manage Your Data Like a Pro**
Unlimited snapshots, clones, and replication add additional layers to safeguard your data from human error or external threats like ransomware. Add system memory (DRAM) and ﬂash to supercharge performance. Use built-in data compression to maximize space.
Manage it all from a user-friendly web interface.
 6. **JoiTrueNASn the  Community**
Need help? TrueNAS CORE has detailed documentation and a global community of over 250,000 to learn from and collaborate with.
 7. **If your data is critical or you simply need professional support,**
  TrueNAS Enterprise and the full line of TrueNAS storage appliances provide enhanced features and the
around-the-clock support necessary to keep your storage online.





