# Unique Durable Identifier for Volumes

**Author(s)**: [Najmudheen](https://github.com/NajmudheenCT)

## Summary

This proposal brings out the design details to implement  an additional identifier for the volumes in OpenSDS, which can be used by any  management platforms, applications, plugins  etc  to uniquely identify the Volume which is backing the  virtual disk.

## Motivation
Host platforms like VMware uses generic  algorithm  to generate a unique id for Virtual Disks. Management modules who manage end to end path (plugins or SRMs) can use this identifier to associate the relation . A common method used by Host Platforms is a  SCSI Inquiry command to the VPD page to retrieve   some "metadata" of  target LUN. This id will  persist between restarts , attaching to a different machine or a different SCSI port of the same machine. There are different standards followed in industry for unique identifier like NAA, EUI FC_WWN etc.

Having this identifier details in volume spec would help these platforms to associate vDisk to Volume in OpenSDS.


### Goals

* Define an Identifier structure as used standard storage APIs like  swordfish.
* Define modified  Volumespec with Identifier.
* Include  Identifier in  API response.

### Non-Goals

* How driver assign identifier is not covered in this design doc.

## Design Details

### Identifier 
This type describes any additional identifiers for a volume as descibed in [Redfish schema](https://redfish.dmtf.org/schemas/DSP2046_2019.1.html#common-properties-Identifier_v1.8.1)


| Property Name     | Type         | Description                              |
|-------------------|--------------|------------------------------------------|
| DurableName       | string       | This indicates the world wide, persistent name of the resource. |
| DurableNameFormat | string(enum) | This represents the format of the DurableName property.<br>See DurableNameFormat in Property Details, below, for the possible values of this property. |

#### Property details

| string | Description                              |
|--------|------------------------------------------|
| EUI    | IEEE-defined 64-bit Extended Unique Identifier. |
| FC_WWN | Fibre Channel World Wide Name.           |
| iQN    | iSCSI Qualified Name.                    |
| NAA    | Name Address Authority Format.           |
| NQN    | NVMe Qualified Name.                     |
| NSID   | NVM Namespace Identifier.                |
| UUID   | Universally Unique Identifier.           |

### Proposed VolumeSpec after adding Identfier

```sh
[
  {
    "id": "084bf71e-a102-11e7-88a8-e31fe6d52248",
    "createdAt": {},
    "updatedAt": {},
    "tenantId": "string",
    "userId": "string",
    "name": "string",
    "multiAttach": true,
    "description": "string",
    "size": 2,
    "availabilityZone": "string",
    "status": "string",
    "profileId": "string",
    "poolId": "string",
    "snapshotId": "string",
    "groupId": "string",
    "snapshotFromCloud": true,
    "replicationId": "string",
    "replicationDriverData": {
      "key1": "value1",
      "key2": "value2"
    },
    "AttachStatus": "",
    "identifier": {
         "durableName": "value",
        "durableNameFormat": "valueFormat"
        }
    "metadata": {
      "key1": "value1",
      "key2": "value2"
    }
  }
]
```





### Data model impact

Volume spec is modified by adding 

       "identifier": {
           "durableName": "testName",
           "durableNameFormat": "TestFormat"
           }

### REST API impact

* Volume GET will contains new fields
```

       "identifier": {
           "durableName": "testName",
           "durableNameFormat": "TestFormat"
           }
```

### Security impact

NO

### Other end user impact

CLI output table for volume details would change.

### Performance impact

No

### Other deployer impact

NA
### Developer impact

Southbound drivers need to populate identifier field for volume spec.

## Use Cases

* OpenSDS nbp plugin for Vmware can use this identifier to uniquely identify openSDS volume for each virtual disks in Vmware ESX attached by OpenSDS. 

## Implementation

* Volume spec model need to be enhanced
* Need to add new fields in database schema
* CLI report needs to be enhanced.
* Driver codes should be enhanced to supply identifier information

## Alternatives considered

NA

## Open issues

NA