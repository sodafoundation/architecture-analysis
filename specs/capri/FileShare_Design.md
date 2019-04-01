# Title

**Author(s)**: [Pravin Ranjan](https://github.com/PravinRanjan10)

## Summary

A share is a remote, mountable file system. You can mount a share to and access a share from several hosts by several users at a time. we can create a share, list shares, and show information for, update, and delete a share.To create a share, specify one of these supported protocols:
* NFS
* CIFS

## Motivation

Multiple users can access the same file share mount point.
Data that would otherwise be duplicated on each client can be kept in a single location and accessed by client on the network.

### Goals

* To create and allow multiple access of a share using only one protocol(eg. NFS/CIFS)
* To create and allow multiple access of a snapshot

## Design Details

1.  A user can request for action (let’s say: create a share) through
     Northbound Plugins(NBP). We have already few plugins, but need to
     modify for file share request.
2. Now, NBP can communicate to OpenSDS API server.
3. Based on user request File Share API will be selected.
4. Internally, OpenSDS File Share API talk to controllers.
5. Controllers will also maintain the File Share metadata in database.
6. Controller communicates to Dock, specific driver to create a share.

![FileShare Architecture Diagram](FileShare_Design.png?raw=true "File_Share_Design")

#### Flow diagram:
![FileShare Architecture Diagram](FileShare_Flow_diagram.png?raw=true "File_Share_Design")

### REST API impact

YES
## Use Cases

Multiple users can access the same file share mount point.
Data that would otherwise be duplicated on each client can be kept in a single location and accessed by client on the network.

## Implementation
This is the list of proposal for API

##### 1. GET /v1beta/{tenantId}/file/shares
* Lists all shares.
 ##### Request
"tenantId": "string"

  ##### Response parameters
```json
  “Id” : “string”

  “createdAt” : “2019-03-20T12:49:00.497Z”

  “updatedAt” : “2019-03-20T12:49:00.497Z”

  “tenantId” : “string”

  “name” : “string”

  “protocol”: “string”

  “description” : “string”

  “size” : “int64”

  “availabilityZone”: “string”

  “status”: “string”

  “profileId”: “string”

  “poolId”: “string”

  “snapshotId”: “string”

  “exportLocations” : “array”
```
##### 2. GET /v1beta/{tenantId}/file/shares/{shareId}
* Shows details for a share.
##### Request
```json
  “tenatId” : “string”

  “shareId” : “string”

  “exportLocations” : “array”
```

  ##### Response parameters
  ```json
  “Id” : “string”

  “protocol” : “string”

  “createdAt” : “2019-03-20T12:49:00.497Z”

  “updatedAt” : “2019-03-20T12:49:00.497Z”

  “tenatId” : “string”

  “userId” : “string“

  “name” : “string”

  “description” : “string”

  “size” : “int64”

  “availabilityZone”: “string”

  “status”: “string”

  “profileId”: “string”

  “poolId”: “string”

  “snapshotId”: “string”

  “exportLocations” : “array”
```

##### 3.   POST /v1beta/{tenantId}/file/shares
* Creates a share.
##### Request
```json
  “description” : “string”

  “protocol” : “array”

  “name” : “string”

  “size” : “int 64”

  “userId” : “string“

  “availabilityZone”: “string”

  “exportLocations” : “array”   // path[ ] //optional

  “profileId”: “string”

  “snapshotId” : “string”
  ```
##### Response parameters
```json
  “Id” : “string”

 “protocol” : “array”

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

 “exportLocations” : “array”   // path[ ]
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
 “Id” : “string”

 “protocol” : “string”

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
    “Id” : “string”
```
##### 6.	GET /v1beta/{tenantId}/file/snapshots
* Lists all share snapshots
#### Request
```json
  “tenantId”: “string”
```
 ##### Response parameters
```json
 “snapshotId”: “string”

 “protocol” : “string”

 “createdAt” : “2019-03-20T12:49:00.497Z”

 “updatedAt” : “2019-03-20T12:49:00.497Z”

 “name” : “string”

 “description” : “string”

 “shareSize” : ”int64”

 “snapshotSize” : “int64”

 “status”: “string”
```
##### 7.	GET /v1beta/{tenantId}/file/snapshots/{snapshotId}
* Show details of snapshot
##### Request
```json
  “id”: “string”
```  
##### Response parameters
```json
  “snapshotId”: “string”

  “protocol” : “string”

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
  “description” : “string”

  “name” : “string”

  “shareId” : “string“
```
  ##### Response parameters
```json  
  “Id”: “string”

  “shareId” : “string”

  “protocol” : “string”

  “createdAt” : “2019-03-20T12:49:00.497Z”

  “updatedAt” : “2019-03-20T12:49:00.497Z”

  “name” : “string”

  “description” : “string”

  “shareSize” : “int64”

  “snapshotSize” : “int64”

  “status”: “string”
```
##### 9.	PUT /v1beta/{tenantId}/file/snapshot/{snapshotId}
* Updates a share snapshot.
##### Request
```json
  “description” : “string”

  “name” : “string”
```  
##### Response parameters
```json
  “snapshotId”: “string”

  “shareId” : “string”

  “protocol” : “string”

  “createdAt” : “2019-03-20T12:49:00.497Z”

  “updatedAt” : “2019-03-20T12:49:00.497Z”

  “name” : “string”

  “description” : “string”

  “shareSize” : “int64”

  “snapshotSize” : “int64”

  “status”: “string”
```
##### 10.	DELETE /v1beta/{tenantId}/file/shares/{snapshotId}
* Deletes a share
##### Request
```json
  “snapshotId” : “sting”
```
##### 11.	POST /v1beta/{tenantId}/file/accesses/
* Add access control for file share.
##### Request
```json
    “sharetId” : “sting”

    “type” : “string”    // user/ip

    “accessCapability” : read/write/execute

    “accessTo” : “user_list”
```
##### 12.	PUT /v1beta/{tenantId}/file/accesses/{accessId}
* Add access control for file share.
##### Request
```json
   “accessId” : “string”
```
## Alternatives considered
NO

## Open issues

NO
