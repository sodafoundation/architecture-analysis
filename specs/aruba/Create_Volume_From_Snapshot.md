Create Volumes from Snapshots

**Status**: Pending

**Version**: Alpha

**Author(s)**: Xiaoyan Li (@lixiaoy1)

## Summary

This proposal is drafted for creating a volume from snapshot supported backend storages. It defines the API interfaces to OpenSDS customers and meanwhile the interfaces for kinds of storage drivers.

## Motivation

Under some scenarios users may want to reuse the data on snapshots, with this function they can easily do it.

### Goals

* Design a unified API interface to create volumes from snapshots supported by backend storages.
* Complete the common logic to create volumes from snapshots, and implementations in LVM, Ceph and Cinder.

### Non-Goals

* The implementations inside storage drivers except LVM, Ceph and Cinder.
* It does not provide the function to create volumes from snapshots which is not supported by backend storages. For example, creating volumes from snapshots in different storages is not supported.

## Proposal

The proposal is drafted to allow users to create volumes from snapshots.

### Data model impact

A new item snapshot_id will be added to VolumeSpec.

type VolumeSpec struct {
        ... // Keep the original entries.
        SnapshotId string `json:"snapshotId,omitempty"`
}

### Logic change
* Only alllow to create a volume whose size is bigger or equal than the snapshot. When size is not specified, use the size of snapshot.
* When profile is not specified, use profile of the snapshot.
* When profile is specified, it needs to check whether the pool of the snapshot satisfies the profile. If yes, it moves on. Or it fails.


### REST API impact

The snapshot_id parameter needs to be added to the create-volume API URL. Meanwhile, size can be optional.

### Security impact

None.

### Other end user impact

A field of snapshot_id will be added to the output of displaying volumes.

### Performance impact

None.

### Other deployer impact

None.

### Developer impact

Driver developers need to implement creating volumes from snapshots in their drives.

## Use Cases

If the original volume is corrupted for some reason, the user can create another volume from a snapshot to prevent data loss.

## Implementation

1. Update the create-volume API.
2. Update the create-volume CLI.
3. Update the display-volume output.
4. Implement the function in volume controller.
5. Add the logic in the selector.
6. Implement this function in LVM, Ceph and Cinder drivers.

## Alternatives considered

None.

## Open issues

None.
