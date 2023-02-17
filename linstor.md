# LinStor

## Introduction:

LinStor comes from LinBit, the company that gave us DRBD(Distributed Replicated Block Device).It gives you a management layer on top of DRBD. It is an open source software  designed to manage block storage devices for large Linux server clusters.  When you ask LinStor to create a storage volume, it creates a volume on two or more nodes. It is a configuration management system for storage on linux systems. A Linstor setup requires at least one active controller and one or more satellites. A linstor controller manages the configuration of the linstor cluster and all its managed storage resources. All communication between linstor components use linstor’s network protocol, based on TCP/IP network connections.

Project summary:

|||
|-------------------------------|-----------------------------|
|Website|https://linbit.com/linstor|
|Organization/foundation name |LinBit(planning to move to SODA foundation)|
|Organization/foundation name|GNU General Public License v3.0|
|Open / Proprietary|Open Source|
|Source Path (if open source)|https://github.com/LINBIT/linstor-server.git|
|Brief Description|LINSTOR is a toolkit for automated cluster management that takes the complexity out of DRBD management and offers a wide range of functions, including provisioning and snapshots.

Project details

Key features:

<![if !supportLists]>● <![endif]>Open source

<![if !supportLists]>● <![endif]>Separation of data and control panel

<![if !supportLists]>● <![endif]>Multiple storage pools

<![if !supportLists]>● <![endif]>Replicate through multiple network cards

<![if !supportLists]>● <![endif]>Choose your own linux file system

<![if !supportLists]>● <![endif]>Low cpu and memory utilization

Architecture:
![architecture diagram](https://ibb.co/hFBQ21s)
![](file:///C:\Users\96653\AppData\Local\Temp\ksohtml10080\wps1.png)

Current usage:

<![if !supportLists]>● <![endif]>Open shift

<![if !supportLists]>● <![endif]>Kubernetes

<![if !supportLists]>● <![endif]>Linux server clusters

Technical details:

LINSTOR is an application that is typically integrated with highly automated systems, such as software defined storage systems or virtualization environments.

Users often interact with the management interface of some other application that uses linstor to manage the storage required for that application’s use case, which also means that the users may not have direct access to the storage systems or to the LINSTOR user interface.
