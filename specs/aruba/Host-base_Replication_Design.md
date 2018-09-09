# Host-based Replication using DRBD

**Authors:** [Xing Yang](https://github.com/xing-yang), [Roland Kammerer](https://github.com/rck)

In the document, we will discuss how to use DRBD for host-based replication.  This is based on the [OpenSDS Replication Design Proposal](https://docs.google.com/document/d/1ymjJdBjFntaVcnR-m--VdSILkzOOj3CM4mZA1Sg5Mk0/edit#) and [DRBD user guide](http://docs.linbit.com/docs/users-guide-9.0/). The focus of this work is on DRBD9.  It includes the following sections:
* DRBD replication overview - an overview of DRBD technology for replication.
* OpenSDS host-based replication using DRBD
This design proposal was drafted [here](https://docs.google.com/document/d/1Wt_f75QXAphQGKCx9VrOHcz4Glizjl1YB-9k3wmXyQA/edit#)
## DRBD Replication Overview
### Replication Modes
DRBD supports 3 replication modes as follows:
* Protocol A - async, using DRBD proxy
* Protocol B - semi-sync
* Protocol C - sync
Protocol C is the most popular one.
### Replication Methods
DRBD has the following replication methods:
* 3 way replication with stacking, while stacking was mainly used in DRBD8, as it did not allow multiple (>2) peers. In DRBD9 stacking is becoming more and more uncommon, as it supports multiple peers natively.
![DRBD Diagram](drbd_stacking_diagram.png?raw=true "DRBD Diagram")
* Long distance with DRBD proxy
    * DRBD proxy is needed for long distance async replication and it requires a license.
    * Synchronous mode is the most used one.
* Truck based replication (involving both DRBD CLIs and physically transporting the copies to the remote peer location.)

### Basic Replication Steps

Section 5 in the DRBD guide on how to configure local and remote devices and do basic failover are referenced here.

Assume there are two nodes in the DRBD cluster.
1. Install DRBD on both cluster nodes
2. Prepare for network configuration (DRBD by convention uses TCP ports from 7788 upwards, with every resource listening on a separate port. DRBD uses two TCP connections for every resource configured. For proper DRBD functionality, it is required that these connections are allowed by your firewall configuration).
3. Prepare lower-level storage device on the cluster nodes.  They need to be roughly of the same size on both nodes.  They can be any block device found on the node, i.e., partition /dev/sda7.
4. Configure the resource: /etc/drbd.d/$yourResource.res.
5. Enable the resources on each node:
    1. Initialize metadata: # drbdadm create-md <resource>
    2. Enable the resource: # drbdadm up <resource>
    3. Check status: # drbdadm status r0
6. Initial synchronization of devices.
    1. Select sync source
    2. Start sync: # drbdadm primary --force <resource>
7. Basic manual failover
8. On the current primary node stop any applications or services using the DRBD device, unmount the DRBD device, and demote the resource to secondary.
    1. `umount /dev/drbd/by-res/<resource>`
    2. `drbdadm secondary <resource>`
    3. For DRBD9, this is not necessary anymore. If the resource was auto-promoted (i.e., a writer opened the device file RW, the device automatically gets promoted to Primary. If the last read-writer closes the device, it automatically demoted to Secondary.
9. On the node we want to make primary, promote the resource and mount the device.
    1. `drbdadm primary <resource>`
    2. `mount /dev/drbd/by-res/<resource> <mountpoint>`
    3. Again, this is not necessary any more. If all nodes are Secondary, you just open the device file (e.g., mount).

Here are some DRBD configuration file examples.

Listing 1. Simple DRBD configuration (/etc/drbd.d/global_common.conf)
```
global {
  usage-count yes;
}
common {
  net {
    protocol C;
  }
}
```

Listing 2. Simple DRBD resource configuration (/etc/drbd.d/r0.res)
```
resource r0 {
  on node1 {
    device    /dev/drbd1;
    disk      /dev/sda7;
    address   10.1.1.31:7789;
    meta-disk internal;
  }
  on node2 {
    device    /dev/drbd1;
    disk      /dev/sda7;
    address   10.1.1.32:7789;
    meta-disk internal;
  }
}
```

Note: Create replication step needs to create above resource config file. Device needs to be attached and visible to the node before that.  After resource config file is created, need to enable and sync resource.


Listing 3. Multi-volume DRBD resource configuration (/etc/drbd.d/r0.res)
```
resource r0 {
  volume 0 {
    device    /dev/drbd1;
    disk      /dev/sda7;
    meta-disk internal;
  }
  volume 1 {
    device    /dev/drbd2;
    disk      /dev/sda8;
    meta-disk internal;
  }
  on node1 {
    address   10.1.1.31:7789;
  }
  on node2 {
    address   10.1.1.32:7789;
  }
}
```

Note: Device needs to be attached to host first before it can become a DRBD resource. Devices can be of any block device type but a volume pair (one on primary and one on secondary storage) needs to have roughly equal size.  Device path such as /dev/sda8 in a volume pair can be different on two cluster nodes as long as they are configured correctly in the config file.


## OpenSDS Host-based Replication using DRBD

We want to support replication with or without using a replication group (DRBD has a grouping concept, but we should still be able to do volume level failover). We need to figure out the following:
* What are the steps without using a replication group
* What are the steps using a replication group

![Host-based Replication Diagram](host_based_replication.png?raw=true "Hosted-based Replication Diagram")

### Host-based Volume Level Replication Workflow

1. Prepare two cluster nodes A and B.
2. Configure network settings.
3. Prepare for lower-level devices. Set up storage for each node. It can be LVM or any other block storage managed by OpenSDS. Create equal size volumes on each side.
    1. Use OpenSDS to create volume A in AZ1.
    2. Attach volume A to node A in AZ1. Assume that it is available as /dev/sda1 on node A.
    3. Use OpenSDS to create volume B in AZ2.
    4. Attach volume B to node B in AZ2. Assume that it is available as /dev/sda2 on node B.
4. DR API receives request to create a replication pair with devices info and profile.
5. API server calls OpenSDS controller.
6. OpenSDS controller uses Selector to check whether the volume backends (primary and secondary) support array-based replication based on input replication devices info and the profile.
    1. If array-based replication is supported, OpenSDS controller uses DR Controller to start the create replication process.
    2. Otherwise use host-based replication.
7. For host-based replication, DR Controller goes through Volume Controller to communicate with Docks hosting volume drivers in AZ1 and AZ2 to get volume info to make sure the volumes from two sites are of the same size and are attached and visible to the hosts.
8. DR Controller creates a replication pair using the DRBD Replication Driver on each node through the Replicator and starts the replication.
    1. DRBD replication driver configures the resource on each node: /etc/drbd.conf.
        1. Configure device info (/dev/sda1 and /dev/sda2) and node info (node A and B) in resource0 on node A and B.
        2. Note: In the case of volume level replication, resource0 will only contain one volume pair.
        3. Note: In the case of group level replication, resource0 is a grouping construct that is corresponding to a replication group so resource0 can contain multiple volume pairs.
    2. DRBD replication driver enables the resources on each node:
        1. Initialize metadata: # drbdadm create-md <resource>
        2. Enable the resource: # drbdadm up <resource>
        3. Check status: # drbdadm status r0
    3. DRBD replication driver does initial synchronization of devices.
        1. Select resource on node A (primary site) as sync source
        2. Start sync on node A: # drbdadm primary --force <resource>
9. OpenSDS controller uses Policy Engine to register async policies to run later.

### Basic Manual Failover
1. API Server calls OpenSDS Controller which calls DR Controller.
2. DR Controller calls DRBD replication driver on primary and secondary node to do failover.
    1. On the current primary node stop any applications or services using the DRBD device, unmount the DRBD device, and demote the resource to secondary.
        1. `umount /dev/drbd/by-res/<resource>`
        2. `drbdadm secondary <resource>`
    2. On the current secondary node, promote the resource and mount the device.
        1. `drbdadm primary <resource>`
        2. `mount /dev/drbd/by-res/<resource> <mountpoint>`

Note: There’s a way to use DRBD in a Pacemaker cluster for more automatic failover (DRBD’s auto-promote feature).  Using the auto-promote feature, there is no need to change the Primary and Secondary roles manually; only stopping of the services and unmounting, respectively mounting, is necessary. (See section 9.2 in the DRBD user guide)

Note: The “umount” and “mount” commands are done outside of DRBD by users, assuming they have mounted the filesystem themselves earlier after establishing replication relationship between the primary and secondary sites.  In DRBD 9.2, auto-promote is supported so running “drbdadm secondary <resource>” manually is no longer necessary.  When user unmounts the filesystem on the primary site and mounts the filesystem on the secondary site, DRBD will automatically failover to the secondary site.  Therefore no additional DRBD commands need to be run to do failover.
