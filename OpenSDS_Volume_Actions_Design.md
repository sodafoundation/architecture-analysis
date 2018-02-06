# Title

Status: Pending

Version: Beta

Author(s): PengYiOfOpenSDS

## Summary

Extends the size of a volume. 

## Motivation

Extends the size of a volume to a requested size, in gibibytes (GiB). 

### Goals

What will be addressed by the proposal?
Extends the size of a volume to a requested size, in gibibytes (GiB). 

### Non-Goals

What will not be addressed by the proposal?
"Shrinks the size of a volume" will not be addressed.

## Proposal

Extends the size of a volume to a requested size, in gibibytes (GiB). 

### Data model impact

Does the proposal affect data model?
No

### REST API impact

Does the proposal affect API?
v1/{ProjectID}/ volumes/{VolumeID} /action
Method: POST
JSON schema definition:
{
"extend": {
"newSize": 3
}
}
Successful response code: 202

### Security impact

Does it affect security? 
No

### Other end user impact

Other than API, are there other changes that affect a user? 
Yes, add a cli command (osdsctl volume extend <id> <new size>).

### Performance impact

Is there any performance impact? 
No

### Other deployer impact

How does it affect the deployment? 
No

Are there config option changes? 
No

### Developer impact

Does this affect other developers? 
Please also define the extend volume driver interface here.

## Use Cases

Extends the size of a volume to a requested size, in gibibytes (GiB). 

## Implementation

Describe how the feature will be implemented. This can be a list of work items.
1. Add an api (v1/{ProjectID}/ volumes/{VolumeID} /action).
2. Add a cli command (osdsctl volume extend <id> <new size>).
3. Implement extend volume in volume controller.
4. Add logic in the selector to handle extend volume.
5. Implement extend volume in drivers.

## Alternatives considered

None
