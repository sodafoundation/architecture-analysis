# Title

Status: WIP

Version: Alpha

Author(s): Jerry (@xxwjj)

## Summary
A tenant is a group of users who share a common access with specific privileges to the software instance. With a multitenant architecture, a software application is designed to provide every tenant a dedicated share of the instance - including its data, configuration, user management, tenant individual functionality and non-functional properties.

## Motivation

Multitenant is used to sharing the same system or program components in a multiuser environment and still ensuring the isolation of data between users. No matter in private cloud and public cloud the multitenant feature is significant.

### Goals

* Design multitanat in northbound APIs.
* Design multitanat data models.

### Non-Goals

Some northbound applications should be modified to adpat this feature.

## Proposal

To implement the multitanant feature,there is many parts need to modifed. The two of most important parts are the api router and the data storage.The api router need to add the a extra paramter `project_id`. Usually, there is three sulotions for data storage: independent database,shared database with independent schecma and shared database shared schecma with tenant_id. Compared to the three schemes, the third schemes have the lowest cost of maintenance and purchase, allowing the largest number of tenants to be supported by each database.So, we better for open source community.

### Data model impact

Nearly all the data model need to add project_id item.


### REST API impact

Nearly all the API url need to add project_id paramter.An example would be like this:<br>
`http://ip:50040/v1beta/{proectId}/profile/{profileId}`

### Security impact

None

### Other end user impact

All the northbound projects need to modifed to adat it. 

### Performance impact

None

### Other deployer impact

None

### Developer impact

None

## Use Cases

A user creates an object and only the user which has admin permission or bolongs to the same proejct can has perssion to query, modify or delete it. 

## Implementation

1. Modify the API router.
2. Modify the data model.
3. Add a paramter which contain the proejct_id and authentication parmaters to all relative functions.
4. Modify unit testing.
5. Modify opensds client package and northbound project.

## Alternatives considered

None
