# OpenSDS Enumeration

OpenSDS supports the query of resources filtering by properties.

## Design Details

### Pagination
Batch queries may lead to a variety of problems when the amount of queried resource is too large, such as: server’s resources are depleted, timeout, and so forth. One way to solve this problem is paged query. Therefore, batch queries will support the capability of pagination.

* limit
The parameter indicates the total number of resources for each query. The default value of this parameter is 50. 

* offset
The parameter indicates the next beginning of resources for each query. The default value of this parameter is 0. 

See the following for example:

curl 'https://127.0.0.1:50040/v1/volumes?offset=100&limit=50'

### Sorting
To facilitate the query of resources, sometimes we need server to return resources according to one or many properties(such as name, status, createdAt, etc.) with a certain order.

* sortKey
The parameter indicates the referenced property when sorting. See the design details below for supportive properties.

* sortDir
The parameter indicates the direction to sort which supports asc(ascending) and desc(descending). The default is asc.

See the following for example:

curl 'https://127.0.0.1:50040/v1/volumes?offset=100&limit=50&sortKey=name&sorDir=desc'

## List Resources by a Filter

### List Docks
List docks by id, name, status, endpoint, driverName, description.

### List Pools
List pools by id, name, status, availabilityZone, dockId, description.

### List Profiles
List profiles by id, name, description.

### List Volumes
List volumes by id (OpenSDS volume uuid), groupId, name, status, availabilityZone, profileId, tenantId (customer), size, description, lunId (admin only), poolId, and host.

### List Snapshots
List snapshots by id, volumeId, tenantId, status, volumeSize, name, description, profileId, groupSnapshotId.

### List Attachments
List attachments by volumeId, status.

### List Groups
List groups by id, status, availabilityZone, profileId, name, description, tenantId.

### List GroupSnapshots
List group snapshots by id, name, description, groupId, profileId, status, tenantId.
