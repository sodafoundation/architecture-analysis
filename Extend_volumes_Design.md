# Design to enable ability to extend volumes

Status: Pending

Version: Beta

Author(s): PengYiOfOpenSDS

## Summary

This proposal is drafted to allow users to extend the size of a volume that was created.

## Motivation

In the current version of openSDS, the user cannot extend the size of a volume. However, in fact, drivers in the background support volume be extended. Therefore, by implementing this proposal, users can be allowed to extend the size of a volume that was created.

### Goals

* Design a unified API interface to extend the size of a volume.

* Integration to different storage drivers.

### Non-Goals

* Shrinks the size of a volume.

## Proposal

This proposal is drafted to allow users to extend the size of a volume that was created.

### Data model impact

None

### REST API impact

The following API will be added to support this capability.
'''v1/{ProjectID}/volumes/{VolumeID}/resize

Method: POST

JSON schema definition:
{
	"newSize": 3
}

Successful response code: 202'''

### Security impact

None

### Other end user impact

Add a cli command (osdsctl volume extend <id> <new size>).

### Performance impact

None

### Other deployer impact

None

### Developer impact

None

## Use Cases

The user created a volume with a size of 1G. Then the user extends the size of the volume to 2G.

## Implementation

1. Add an api (v1/{ProjectID}/ volumes/{VolumeID} /action).
2. Add a cli command (osdsctl volume extend <id> <new size>).
3. Implement extend volume in volume controller.
4. Add logic in the selector to handle extend volume.
5. Implement extend volume in LVM, Ceph, and Cinder drivers.

## Alternatives considered

None
