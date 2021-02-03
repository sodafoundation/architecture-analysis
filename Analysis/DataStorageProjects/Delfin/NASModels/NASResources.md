# Delfin NAS Resource Model Analysis

**Authors:** [SODA Team](https://github.com/sodafounation)

This is NAS resource model analysis for SODA Infrastructure Management project Delfin.

## Goal

This documents purpose is to define NAS data models of Delfin resources including Filesystem, Qtree, Quota etc.

## Non-Goals

NA

## Assumptions and Constraints

NA

## Requirement Analysis

### 1. Delfin project needs to support NAS resource when managing heterogenous storage backends

In order to manage storage resources in a heterogenous environment, we need to derive a NAS model for resources from different storage vendors.

### 2. Delfin project needs to support NAS resource models of Filesystem, Qtree and Quota

Users of the Delfin project should be able to get details of NAS resources of Filesystem, Qtree and Quota.

## NAS Resource Model Analysis

Following are the resource models derived from analyzing some the leading storage vendors including DELL, Hitachi, HP, Huawei, IBM etc.

### NAS Resource Model implementation diagram

![NAS Resource Model Diagram](NASResourceModel.jpg)

### Filesystem

Attributes | Type | Description
-- | -- | --
id | string | UUID of the Filesystem
name | string | Name of the Filesystem
storage_id | string | Delfin id of the associated storage
native_filesystem_id | string | Original Filesystem id in the device
total_capacity | long | Total capacity in bytes of the Filesystem
used_capacity | long | Used capacity in bytes of the Filesystem
free_capacity | long | Free capacity in bytes of the Filesystem
status | enum | Running status of the Filesystem (normal, offline, unknown)
type | string | Filesystem type (thick, thin)
deduplication | bool | Filesystem deduplication (true, false)
compression | bool | Filesystem compression (true, false)
security_mode | string | Security Mode of Filesystem (mixed, native, windows, unix)
nfs_exports | long | Number of NFS exports
cifs_exports | long | Number of CIFS exports

### Qtree

Attributes | Type | Description/enum
-- | -- | --
id | string | UUID of the Qtree
name | string | Name of the Qtree
storage_id | string | Delfin id of the associated storage
native_qtree_id | string | Original Qtree id in the device
filesystem_id | string | Delfin id of the parent filesystem
quota_id | string | Delfin id of the quota applied to Qtree
path | string | Path of the Qtree
used_capacity | long | Capacity used
nfs_exports | long | Number of NFS exports
cifs_exports | long | Number of CIFS exports
security_mode | string | Security Mode of Qtree (mixed, native, windows, unix)
state | string | State of Qtree (normal, soft_limit, hard_limit, abnormal)

### Quota

Attributes | Type | Description
-- | -- | --
id | string | UUID of the quota
native_qtree_id | string | Original Quota id in the device
storage_id | string | Delfin id of the associated storage
type | string | Quota type (Qtree, Volume, User, Group)
capacity_hard_limit | long | Hard limit for the space
capacity_soft_limit | long | Soft limit for the space
file_hard_limit | long | Hard limit on the number of files
file_soft_limit | long | Soft limit on the number of files
