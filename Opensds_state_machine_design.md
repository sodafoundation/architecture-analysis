# Opensds state machine design

Status: WIP

Version: Alpha

Author(s): jack li (@lasshpanzer)

## Summary

The State machines of volume,snapshot and attachment show the real-time status and state transitions between actions.

## Motivation

For example, When creating a volume, there is needed to know if it has been created or if something went wrong.

### Goals

Design volume statuses

Design snapshot statuses

Design attachment statuses

### Non-Goals

Some architectures needs to be changed.

## Proposal

There is no mechanism to show the real-time status and state transitions in the current opensds version.It needs to be implemented.

### Data model impact

The state attributes of the data have been added to many new states

### REST API impact

Does the proposal affect API?
No

### Security impact

Does it affect security?
No

### Other end user impact

Other than API, are there other changes that affect a user? For example the CLI changes.
No

### Performance impact

Is there any performance impact?
No

### Other deployer impact

None

### Developer impact

Other developers may use these state attributes to design other properties and methods.

## Use Cases

None

## Implementation

Volume statuses

Status	          Description
creating	     |The volume is being created. 
available	     |The volume is ready to attach to an instance.
attaching	      The volume is attaching to an instance.
detaching	      The volume is detaching from an instance.
in-use	          The volume is attached to an instance.
deleting	      The volume is being deleted.
error	          A volume creation error occurred.
error_deleting	  A volume deletion error occurred.
error_extending	  An error occurred while attempting to extend a volume.
extending	      The volume is being extended.


Snapshot statuses

Status	          Description
creating	      The snapshot is being created.
available	      The snapshot is ready to use.
deleting	      The snapshot is being deleted
error	          A snapshot creation error occurred.
deleted	          The snapshot has been deleted
error_deleting	  A snapshot deletion error occurred.


Attachment statuses

Status	          Description
attached	      The volume has been attached to an instance.
attaching	      The volume is attaching to an instance.
detached	      The volume has been detached from an instance.
error_attaching	  An error occurred while the volume is attaching to an instance.
error_detaching	  An error occurred while the volume is detaching from an instance.


## Alternatives considered

None
