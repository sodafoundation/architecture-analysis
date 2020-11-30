# Upload Snapshot

**Authors:** [Xing Yang](https://github.com/xing-yang)

This is the design spec for the upload snapshot feature.

The upload snapshot design was initially drafted [here](https://docs.google.com/document/d/1pkAocf4oZ_QF3F_p5YEIlQL9XkDulOr5kMVaZYgyAlI/edit?usp=sharing) as a seperate API design. Since then we have decided to focus on phase one of the design which was drafted [here](https://docs.google.com/document/d/1YoxqZuprbL2C79HoNvYuTh8Yonz9JE42xluTGPdwI3U/edit?usp=sharing).

## Upload snapshot when creating snapshot

### CSI

* Create a `profile` in OpenSDS, specifying snapshot policy including topology which contains info to connect to a S3 backend in the cloud, i.e., AWS.
* Create VolumeSnapshotClass. Under `parameters` of VolumeSnapshotClass, specify profile id. 
* Create snapshot using CSI plugin.
* CSI plugin calls CreateSnapshot in Hotpot.

### Hotpot

* CreateSnapshot should have a profile id.  This profile should only contain snapshot policy including topology.
* If specified, CreateSnapshot should call initialize_connection_snapshot to attach snapshot and then upload snapshot to S3, after snapshot is created.  
* Information about where the snapshot is uploaded should be saved in the metadata of snapshot so we can retrieve it at CreateVolume from snapshot time.
* While still in CreateSnapshot, after snapshot is attached, open the devicePath to read the data and upload to S3.  The uploading part should call API from multi-cloud data control. Ideally data should be read chunk by chunk.
* After uploading is complete, call terminate_connection_snapshot to detach snapshot.
* In addition to data from devicePath, we also need to upload any metadata necessary for the S3 object store and any snapshot metadata.

## Creating volume from snapshot stored in S3

### CSI

* Create volume from snapshot using CSI plugin.

### Hotpot

* In CreateVolume method, check snapshot id if provided.  From snapshot, check metadata to see if the snapshot is uploaded.  Also we should add a flag in CreateVolume to indicate the source snapshot should be the one uploaded in the cloud.  This is because we actually have two snapshots, one on the block storage itself, and the other one in a S3 object store.

* We need to attach the volume, download data from S3 and copy to the volume.

* After it is done, we need to detach the volume.

## Hotpot driver interfaces

Add the following driver interfaces:

* Initialize_connection_snapshot()
* Terminate_connection_snapshot()

For connector and target, use existing interfaces if possible and separate the logic about snapshot from the logic about volume if needed for clarity.

For LVM driver, we can use local path as LVM is local.  The real attach work will be implemented post-Bali to support multi-node.
