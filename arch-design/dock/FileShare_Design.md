# Title

**Author(s)**:  [Shruthi MN](https://github.com/Shruthi-1MN), [Pravin Ranjan](https://github.com/PravinRanjan10)

## Summary

A share is a remote, mountable file system. You can mount a share to and access a share from several hosts by several users at a time. we can create a share, list shares, and show information for, update, and delete a share.To create a share, specify one of these supported protocols:
* NFS
* CIFS

## Motivation

Multiple users can access the same file share mount point.
Data that would otherwise be duplicated on each client can be kept in a single location and accessed by client on the network.

### Goals

* Facilitating File Share Service by providing Standard API to manage multiple vendors, simplify File Share API definition  
* File share across the users based on access capability
* File share facilitates with profiling
* Scope for now is to support only NFS, SMB file share protocols
* write a Manila driver and a default NFS/SMB driver
* Provide CLI support for File Share service

### Non-Goals

* Provide support for multiple other file share protocols ex: GlusterFS, HDFS etc..
* Supporting Replication and Data Protection for file share profile

## Design Details

1.  A user can request for action (let’s say: create a share) through
     Northbound Plugins(NBP). We have already few plugins, but need to
     modify for file share request. A User can also use CLI or dashboard.
     So, all the actions(create, list, update, delete) should be supported
     by CLI and dashboard.
2. Now, NBP can communicate to OpenSDS API server.
3. Based on user request File Share API will be selected.
4. Internally, OpenSDS File Share API talk to controllers.
5. Controllers will also maintain the File Share metadata in database.
6. Scheduler which identifies the matching pool with the selected profile and schedules the request to the specific dock.
7. Dock discovers the requested driver and pass the values to the southbound driver.
8. Southbound drivers such as native nfs, ocenstor, manila performs the requested operations.  

![FileShare Architecture Diagram](resources/FileShare_Design.png?raw=true "File_Share_Design")

#### Flow diagram:
![FileShare Architecture Diagram](resources/FileShare_Flow_diagram.png?raw=true "File_Share_Design")

### REST API impact

YES
Fileshare API's needs to write and Existing Profile API's needs to be changed.

### Data model impact

YES

### Security impact

No

### Other end user impact

No

## Use Cases

Multiple users can access the same file share mount point.
Data that would otherwise be duplicated on each client can be kept in a single location and accessed by client on the network.

## Implementation
This is the list of proposal for API

##### 1. GET /v1beta/{tenantId}/file/shares
* Lists all shares.
 ##### Request
 ```json
"*tenantId": "string"
```
  ##### Response parameters
```json
  “id” : “string”

  “createdAt” : “2019-03-20T12:49:00.497Z”

  “updatedAt” : “2019-03-20T12:49:00.497Z”

  “tenantId” : “string”

  “name” : “string”

  “protocols”: "[]string"

  “description” : “string”

  “size” : “int64”

  “availabilityZone”: “string”

  “status”: “string”

  “profileId”: “string”

  “poolId”: “string”

  “snapshotId”: “string”

  “exportLocations” : "[]string"
```
##### 2. GET /v1beta/{tenantId}/file/shares/{shareId}
* Shows details for a share.
##### Request
```json
  “*tenantId” : “string”

  “*shareId” : “string”

  “exportLocations” : "[]string"
```

  ##### Response parameters
  ```json
  “id” : “string”

  “protocols” : "[]string"

  “createdAt” : “2019-03-20T12:49:00.497Z”

  “updatedAt” : “2019-03-20T12:49:00.497Z”

  “tenantId” : “string”

  “userId” : “string“

  “name” : “string”

  “description” : “string”

  “size” : “int64”

  “availabilityZone”: “string”

  “status”: “string”

  “profileId”: “string”

  “poolId”: “string”

  “snapshotId”: “string”

  “exportLocations” : “[]string”
```

##### 3.   POST /v1beta/{tenantId}/file/shares
* Creates a share.
##### Request
```json
  “*tenantId” : “string“

  “*name” : “string”

  “*size” : “int64”

  “*profileId”: “string”

  “description” : “string”

  “userId” : “string“

  “availabilityZone”: “string”

  “exportLocations” : “[]string”

  “snapshotId” : “string”
  ```
##### Response parameters
```json
 “id” : “string”

 “createdAt” : “2019-03-20T12:49:00.497Z”

 “updatedAt” : “2019-03-20T12:49:00.497Z”

 “tenantId” : “string”

 “userId” : “string“

 “name” : “string”

 “description” : “string”

 “size” : “int64”

 “availabilityZone”: “string”

 “status”: “string”

 “profileId”: “string”

 “poolId”: “string”

 “snapshotId”: “string”

 “exportLocations” : “[]string”
```
##### 4. PUT /v1beta/{tenantId}/file/shares/{shareId}
* Updates a share.
##### Request
```json
  “name” : “string”

  “description” : “string”
```  
 ##### Response parameters
 ```json
 “id” : “string”

 “protocols” : “[]string”

 “createdAt” : “2019-03-20T12:49:00.497Z”

 “updatedAt” : “2019-03-20T12:49:00.497Z”

 “tenantId” : “string”

 “userId” : “string“

 “name” : “string”

 “description” : “string”

 “size” : “int64”

 “availabilityZone”: “string”

 “status”: “string”

 “profileId”: “string”

 “poolId”: “string”

 “snapshotId”: “string”
```
##### 5. 	DELETE /v1beta/{tenantId}/file/shares/{shareId}
* Deletes a share
##### Request
```json
    “*id” : “string”
```
##### 6.	GET /v1beta/{tenantId}/file/snapshots
* Lists all share snapshots
#### Request
```json
  “*tenantId”: “string”
```
 ##### Response parameters
```json
 “id”: “string”

 “protocols” : "[]string"

 “createdAt” : “2019-03-20T12:49:00.497Z”

 “updatedAt” : “2019-03-20T12:49:00.497Z”

 “name” : “string”

 “description” : “string”

 "shareId" : "string"

 “shareSize” : ”int64”

 “snapshotSize” : “int64”

 “status”: “string”
```
##### 7.	GET /v1beta/{tenantId}/file/snapshots/{snapshotId}
* Show details of snapshot
##### Request
```json
  “*id”: “string”
```  
##### Response parameters
```json
  “id”: “string”

  “shareId” : “string“

  “protocols” : "[]string"

  “createdAt” : “2019-03-20T12:49:00.497Z”

  “updatedAt” : “2019-03-20T12:49:00.497Z”

  “name” : “string”

  “description” : “string”

  “shareSize” : “int64”

  “snapshotSize” : “int64”

  “status”: “string”
```
##### 8.   POST /v1beta/{tenantId}/file/snapshot
* Creates a snapshot from a share.
##### Request
```json
  “*shareId” : “string“

  “description” : “string”

  “name” : “string”

```
  ##### Response parameters
```json  
  “id”: “string”

  “shareId” : “string”

  “protocols” : "[]string"

  “createdAt” : “2019-03-20T12:49:00.497Z”

  “updatedAt” : “2019-03-20T12:49:00.497Z”

  “name” : “string”

  “description” : “string”

  “shareSize” : “int64”

  “snapshotSize” : “int64”

  “status”: “string”
```
##### 9.	PUT /v1beta/{tenantId}/file/snapshots/{snapshotId}
* Updates a share snapshot.
##### Request
```json
  “description” : “string”

  “name” : “string”
```  
##### Response parameters
```json
  “id”: “string”

  “shareId” : “string”

  “protocols” : "[]string"

  “createdAt” : “2019-03-20T12:49:00.497Z”

  “updatedAt” : “2019-03-20T12:49:00.497Z”

  “name” : “string”

  “description” : “string”

  “shareSize” : “int64”

  “snapshotSize” : “int64”

  “status”: “string”
```
##### 10.	DELETE /v1beta/{tenantId}/file/snapshots/{snapshotId}
* Deletes a snapshot
##### Request
```json
  “*id” : “string”
```
##### 11.	POST /v1beta/{tenantId}/file/acls/
* Add access control list for file share.
##### Request
```json
    “shareId” : “string”

    “type” : “string”    // user/ip

    “accessCapability” : "[]string"read/write

    “accessTo” : "string"   //“The IP's”
```
##### 12.	DELETE /v1beta/{tenantId}/file/acls/{aclId}
* DELETE access control list for file share.
##### Request
```json
   “*id” : “string”
```
## Implementation of file share APIs for Profiles consolidated with block storage
Note : Highlighted with background color texts are specific to file share and those are only supported in file share. Rest all parameters are belongs to block storage

##### 1.	POST /v1beta/{tenantId}/profiles
* Create profiles

##### Request
```json
body

   [
     "name"         : "string",
     "storage type" : "string",
     "description"  : "string",
     "provisioningProperties" : {
                "datastore"  : {
                    "characterCodeSet"        : "string",
                    "maxFileNameLengthBytes"  : "int64",
                    "storageAccessCapability" : "string",
                    "recoveryTimeObjective"   : "int64",
                    "provisioningPolicy"      : "string",
                    "isSpaceEfficient"        : "bool"
                },
                "ioconnectivity" : {
                  "accessProtocol" : "string",
                  "maxIOPS"        : "int64",
                  "maxBWS"         : "int64"
                }
      },
      "replicationProperties" : {
          "dataProtection" : {
              "isIsolated"                  : "bool",
              "minLifeTime"                 : "string",
              "recoveryGeographicObjective" : "string",
              "recoveryPointObjectiveTime"  : "string",
              "recoveryTimeObjective"       : "string",
              "replicaType"                 : "string"
          },
          "replicaInfos" : {
              "replicaUpdateMode"    : "string",
              "consistencyEnabled"   : "bool",
              "replicationPeriod"    : "string",
              "replicationBandwidth" : "int64"
          }
      },
    "snapshotProperties" : {
            "name"        : "string",
            "description" : "string",
            "schedule" : {
                  "datetime"  : "date",
                  "occurence" : "string"
            },
            "replicaInfos":{
                  "number"   : "int64",
                  "duration" : "int64"
            },
            "topology" : {
                  "location" : "string"
            }
      },
      "dataProtectionProperties" : {
          "dataProtection" : {
              "isIsolated"                  : "bool",
              "minLifetime"                 : "string",
              "recoveryGeographicObjective" : "string",
              "recoveryPointObjectiveTime"  : "string",
              "recoveryTimeObjective"       : "string",
              "replicaType"                 : "string"
          },
          "consistencyEnalbed"              : "bool"
      },
     "customProperties" : {
          "additionalProp1" : {},
          "additionalProp2" : {},
          "additionalProp3" : {}
      }
  ]
  tenantId* : "string"  //The project UUID in a multi-tenancy environment
```

##### Response

```json
200 OK
[
  {
      "id": "5d8c3732-a248-50ed-bebc-539a6ffd25c1",
      "name": "Gold",
      "description": "provide gold storage service",
      "customProperties": {
            "key1": "value1",
            "key2": {
                  "subKey1": "subValue1",
                  "subKey2": "subValue2"
            },
            "key3": "value3"
      }
    }
]
401   Unauthorized
403   Forbidden
500   An unexpected error occurred
{
  "code": 0,
  "message": "string"
}
```
##### 2.	GET /v1beta/{tenantId}/profiles
* Lists information for all profiles.

##### Request

```json
tenantId* : "string"  //The project UUID in a multi-tenancy environment
```
##### Response

```json
200 OK
[
  {
      "id": "5d8c3732-a248-50ed-bebc-539a6ffd25c1",
      "name": "Gold",
      "description": "provide gold storage service",
      "customProperties": {
            "key1": "value1",
            "key2": {
                  "subKey1": "subValue1",
                  "subKey2": "subValue2"
            },
            "key3": "value3"
      }
    },
    {
    "id": "5d8c3732-a248-50ed-bebc-539a6ffd25c2",
    "name": "Silver",
    "description": "provide silver storage service",
    "customProperties": {
          "key1": "value1",
          "key2": "value2"
     }
   }
]
401   Unauthorized
403   Forbidden
404   Resource doesn't exist
500   An unexpected error occurred
{
  "code": 0,
  "message": "string"
}

```
##### 3.	GET /v1beta/{tenantId}/profiles/{profileId}
* Gets profile detail by profile ID.

##### Request

```json
tenantId* : "string"  //The project UUID in a multi-tenancy environment
profileId* : "string" //The UUID of the Profile
```
##### Response

```json

[  
  {
    "id": "5d8c3732-a248-50ed-bebc-539a6ffd25c1",
    "name": "Gold",
    "description": "provide gold storage service",
    "customProperties": {
          "key1": "value1",
          "key2": {
                "subKey1": "subValue1",
                "subKey2": "subValue2"
          },
          "key3": "value3"
    }
 }
]
401   Unauthorized
403   Forbidden
404   Resource doesn't exist
500   An unexpected error occurred
{
  "code": 0,
  "message": "string"
}
```
##### 4.	PUT /v1beta/{tenantId}/profiles/{profileId}
* Updates a profile by profile ID.

Note : Updates only name and description rest all parameters in profiles are not allowed to update/change
##### Request
```json
body*
[
  {
    "name": "string",
    "description": "string",
   }
]
tenantId* : "string"  //The project UUID in a multi-tenancy environment
profileId* : "string" //The UUID of the Profile
```
##### Response
```json
200   OK
{
    "id": "5d8c3732-a248-50ed-bebc-539a6ffd25c1",
    "name": "Gold",
    "description": "provide gold storage service",
    "customProperties": {
          "key1": "value1",
          "key2": {
                  "subKey1": "subValue1",
                  "subKey2": "subValue2"
          },
          "key3": "value3"
    }
}

401   Unauthorized
403   Forbidden
404   Resource doesn't exist
500   An unexpected error occurred
{
  "code": 0,
  "message": "string"
}
```
##### 5.	DELETE /v1beta/{tenantId}/profiles/{profileId}
* Deletes a profile.

##### Request
```json
tenantId* : "string"  //The project UUID in a multi-tenancy environment
profileId* : "string" //The UUID of the Profile  
```
##### Response
```json
200   OK
401   Unauthorized
403   Forbidden
404   Resource doesn't exist
500   An unexpected error occurred
{
  "code": 0,
  "message": "string"
}
```
##### 6.	POST /v1beta/{tenantId}/profiles/{profileId}/customeProperties
* Adds customized property to the profile.

##### Request
```json
body *
{
      "additionalProp1": {},
      "additionalProp2": {},
      "additionalProp3": {}
}
tenantId* : "string"  //The project UUID in a multi-tenancy environment
profileId* : "string" //The UUID of the Profile

```
##### Response
```json
200   OK
{
    "key1": "value1",
    "key2": {
            "subKey1": "subValue1",
            "subKey2": "subValue2"
    },
    "key3": "value3"
}

401   Unauthorized
403   Forbidden
404   Resource doesn't exist
500   An unexpected error occurred
{
  "code": 0,
  "message": "string"
}
```
##### 7.	GET /v1beta/{tenantId}/profiles/{profileId}/customeProperties
* Lists all customized properties for the profile.

##### Request
```json

tenantId* : "string"  //The project UUID in a multi-tenancy environment
profileId* : "string" //The UUID of the Profile

```
##### Response
```json
200   OK
{
    "key1": "value1",
    "key2": {
            "subKey1": "subValue1",
            "subKey2": "subValue2"
    },
    "key3": "value3"
}

401   Unauthorized
403   Forbidden
404   Resource doesn't exist
500   An unexpected error occurred
{
  "code": 0,
  "message": "string"
}
```
##### 8.	DELETE /v1beta/{tenantId}/profiles/{profileId}/customProperties/{customKey}
* Remove a customized property from profile

##### Request
```json

tenantId* : "string"  //The project UUID in a multi-tenancy environment
profileId* : "string" //The UUID of the Profile
customKey* : "string" //The key of the customized properties

```
##### Response
```json
200   OK
401   Unauthorized
403   Forbidden
404   Resource doesn't exist
500   An unexpected error occurred
{
  "code": 0,
  "message": "string"
}
```


## Alternatives considered
NO

## Open issues

NO
