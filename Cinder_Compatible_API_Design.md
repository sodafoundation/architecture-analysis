# Cinder-compatible api design

Status: Pending

Version: Alpha

Author(s): PengYi

## Summary

Cinder-compatible api receives cinder api request and convert it to hotpot api. 

## Motivation

To eliminate the differences between cinder api and hotpot api, an adapter(cinder-compatible api) should receive cinder api request and convert it to hotpot api. 

### Goals

* The adapter(cinder-compatible api) shouldn’t have an impact on the current openstack or hotpot environment. It also provides rest api to counterfeit the cinder api.

### Non-Goals

The proposal only satisfies the data model in the current OpenSDS. 

## Proposal
The working steps of Cinder-compatible api are as follows:
1.Users use a python-cinderclient to issue an api request.
2.Cinder-compatible api receive the request, parse and convert the request to hotpot. 
3.Hotpot receive the request and performs related operations and returns the result to the Cinder-compatible api.
4.Cinder-compatible api receive the result, parse and convert the result to python-cinderclient. 

### Data model impact

None

### REST API impact

None

### Security impact

None

### Other end user impact

None

### Performance impact

None

### Other deployer impact

None

### Developer impact

None

## Use Cases

Create volume type
1.	User create a volume type with the following request:
	Request Example
{
    "volume_type": {
        "name": "vol-type-001",
        "description": "volume type 0001",
        "os-volume-type-access:is_public": true,
        "extra_specs": {
            "capabilities": "gpu"
        }
    }
}
2.	Cinder-compatible api receive the request, parse and convert the request to hotpot api request, then send the request to hotpot:
	{ “profile”: {
      "name": "vol-type-001",
      "storageType": "block",
      "description": "volume type 0001",
      "extras": {
        "capabilities": "gpu"
      }
   }
}

Note:
●	Currently hotpot doesn’t support the volume type access
●	The storageType can be block, file, object, default is block

Create volume 
1.	There are many ways to create a volume, such as create volume from volume type, create volume from snapshot, create volume from volume, etc. Now the hotpot only support creating volume from volume type. Creating volume from snapshot/volume will be supported in the near future. The following example shows how to create a volume from a volume type.
	{
    "volume": {
        "size": 10,
        "availability_zone": "az1",
        "description": null,
        "name": "vol001",
        "volume_type": "7edbc2f4-1507-44f8-ac0d-eed1d2608d29",
    }
}
2.	For cinder-compatible api, the request of creating volume will be represented like the following json string.
	{
  "name": "vol001",
  "description": "",
  "size": 10,
  "availabilityZone": "az1",
  "profileId": "7edbc2f4-1507-44f8-ac0d-eed1d2608d29",
}

Create attachment
1.	Cinder has exposed new attachment api since ocata version which supports attaching volume to nova instances or non-nova instances. 
{
    "attachment": {
        "instance_uuid": "462dcc2d-130d-4654-8db1-da0df2da6a0d",
        "connector": {
            "initiator": "iqn.1993-08.org.debian: 01: cad181614cec",
            "ip": "192.168.1.20",
            "platform": "x86_64",
            "host": "tempest-1",
            "os_type": "linux2",
            "multipath": false,
            "mountpoint": "/dev/vdb",
            "mode": "ro"
        },
        "volume_uuid": "462dcc2d-130d-4654-8db1-da0df2da6a0d"
    }
}
2.	For cinder-compatible api, the request of creating attachment will be represented like the following json string.
{
  "hostInfo": {
    "platform": "x86_64",
    "osType": "linux2",
    "ip": "192.168.1.20",
    "host": "tempest-1",
    "initiator": "iqn.1993-08.org.debian: 01: cad181614cec"
  },
  "metadata": {
    "instance_uuid": "462dcc2d-130d-4654-8db1-da0df2da6a0d"
  },
  "mountpoint": "/dev/vdb",
  "volumeId": "462dcc2d-130d-4654-8db1-da0df2da6a0d"
}

Note:
●	Multipath and mode aren’t supported in hotpot, these feature should be implemented in future.
●	Instance_uuid: In Nova terms server == instance, the server_id parameter referenced below is the UUID of the Instance, for non-nova consumers this can be a server UUID or some other arbitrary unique identifier. 
	For hotpot, this parameter can be stored in metadata.

## Implementation

Work Items
1. Implement basic framework about the cinder-compatible api
2. Implement volume type and extra specs api(Get/List/Create/Delete/Update)
3. Implement volume api(Get/List/Create/Delete/Update)
4. Implement volume attachment(Get/List/Create/Delete/Update)
5. Implement snapshot api(Get/List/Create/Delete/Update)

## Alternatives considered

None
