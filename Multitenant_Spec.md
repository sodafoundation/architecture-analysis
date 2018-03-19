# Title

Status: WIP

Version: Alpha

Author(s): Jerry (@xxwjj)

## Summary
A tenant is a group of users who share a common access with specific privileges to the software instance. With a multitenant architecture, a software application is designed to provide every tenant a dedicated share of the instance - including its data, configuration, user management, tenant individual functionality and non-functional properties.

## Motivation

Multitenant is used to share the same system or program components in a multiuser environment and still ensuring the isolation of data between users. No matter in private cloud and public cloud the multitenant feature is significant.

### Goals

* Design multitenant in northbound APIs.
* Design multitenant data models.

### Non-Goals

Some northbound applications should be modified to adapt this feature.

## Proposal

To implement the multitenant feature,there are many parts need to modified. The two of most important parts are the API router and the data storage.The API router need to add the a extra parameter `tenantId`. Usually, there are three solutions for data storage: independent database,shared database with independent schema and shared database shared schema with tenant_id. Compared to the three schemas, the third schemas have the lowest cost of maintenance and purchase, allowing the largest number of tenants to be supported by each database. So it is better for open source community.

### Data model impact

Nearly all the data model need to add tenantId item.


### REST API impact

Nearly all the API URL need to add tenantId parameter.An example would be like this:<br>
`http://ip:50040/v1beta/{tenantId}/profile/{profileId}`

### Security impact

None

### Other end user impact

All the northbound projects need to modified to adapt it. 

### Performance impact

None

### Other deployer impact

None

### Developer impact

None

## Use Cases

A user creates an object and only the user who has administrator permission or belongs to the same tenant can have permission to query, modify or delete it. 

## Implementation

1. Modify the API router.
2. Modify the data model.
3. Add a parameter which contains the tenantId and authentication parameters to all relative functions.
4. Modify unit testing.
5. Modify OpenSDS client package and northbound project.

## Alternatives considered

None
