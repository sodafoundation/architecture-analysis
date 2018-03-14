# OpenSDS state machine design

Status: WIP

Version: Alpha

Author(s): jack li (@lasshpanzer)

## Summary

The State machines of volume, snapshot and attachment to show the real-time status and state transitions between actions.

## Motivation

When creating a volume, there is a need to know if it has been created or if something went wrong.

### Goals

Design volume statuses

Design snapshot statuses

Design attachment statuses

### Non-Goals

Statuses for Group, GroupSnapshot and Replication will be designed later.

## Proposal

There is no mechanism to show the real-time status and state transitions in the current opensds version. It needs to be implemented.

### Data model impact

Status will be added to volume, snapshot, and attachment resources.

### REST API impact

Does the proposal affect API?
No

### Security impact

Does it affect security?
No

### Other end user impact

When getting a resource such as a volume, end user can now see the changed status of the resource.

### Performance impact

Is there any performance impact?
No

### Other deployer impact

None

### Developer impact

Driver developers will need to return status after an operation is completed on a resource.

## Use Cases

None

## Implementation

Volume statuses

Status            Description
Creating	      The volume is being created. 
Available	      The volume is ready to use.
Attaching	      The volume is attaching to an instance.
Detaching	      The volume is detaching from an instance.
InUse             The volume is attached to an instance.
Deleting	      The volume is being deleted.
Error             A volume creation error occurred.
ErrorDeleting     A volume deletion error occurred.
ErrorExtending    An error occurred while attempting to extend a volume.
Extending         The volume is being extended.


Snapshot statuses

Status	          Description
Creating	      The snapshot is being created.
Available	      The snapshot is ready to use.
Deleting	      The snapshot is being deleted
Error	          A snapshot creation error occurred.
Deleted	          The snapshot has been deleted
ErrorDeleting	  A snapshot deletion error occurred.


Attachment statuses

Status	          Description
Attached	      The volume has been attached to an instance.
Attaching	      The volume is attaching to an instance.
Detached	      The volume has been detached from an instance.
ErrorAttaching    An error occurred while the volume is attaching to an instance.
ErrorDetaching	  An error occurred while the volume is detaching from an instance.


## Alternatives considered

None
