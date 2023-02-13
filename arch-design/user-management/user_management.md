# SODA User Management Design

**Authors:** [Rajat Verma](https://github.com/rajat-soda)

This document proposes a design for User Management (using Openstack Keystone). This proposal was drafted [here](https://docs.google.com/document/d/1AStnHyht46BsISaxeIBsLiV7IBQYLL1tNyLskzOKNqM/edit#heading=h.8uz1ebyc43xn).

# Background
## Motivation and background
User management, in its simplest form, is the method by which you create, remove and maintain users in an Enterprise System. Any solution designed to serve multiple users must have some type of a user management system, be it a proprietary tool built into the product or a tie into an existing system such as Active Directory/LDAP, or another identity provider.
User management not only establishes a user’s authorization to access secure resources, it also serves as a repository of identities and, if done efficiently, can be the source of all identities for an organization. There are many such open source tools that have been designed to fulfill the requirements of User Management. One of such open source tools which aptly fulfills the needs of SODA User Management is Keystone. And SODA intends to adopt Keystone as the User Management tool. 
 
## Services
Keystone is organized as a group of internal services exposed on one or many endpoints. Many of these services are used in a combined fashion by the frontend. For example, an authenticate call will validate user/project credentials with the Identity service and, upon success, create and return a token with the Token service.

## Identity
The Identity service provides auth credential validation and data about users and groups. In the basic case, this data is managed by the Identity service, allowing it to also handle all CRUD operations associated with this data. In more complex cases, the data is instead managed by an authoritative backend service. An example of this would be when the Identity service acts as a frontend for LDAP. In that case the LDAP server is the source of truth and the role of the Identity service is to relay that information accurately.

## Users
Users represent an individual API consumer. A user itself must be owned by a specific domain, and hence all user names are not globally unique, but only unique to their domain.

## Groups
Groups are a container representing a collection of users. A group itself must be owned by a specific domain, and hence all group names are not globally unique, but only unique to their domain.

## Resource
The Resource service provides data about projects and domains.

## Projects (Tenants)
Projects (also known as Tenants) represent the base unit of ownership in SODA, in that all resources in SODA should be owned by a specific project. A project itself must be owned by a specific domain, and hence all project names are not globally unique, but unique to their domain. If the domain for a project is not specified, then it is added to the default domain.
 
## Domains
Domains are a high-level container for projects, users and groups. Each is owned by exactly one domain. Each domain defines a namespace where an API-visible name attribute exists. Keystone provides a default domain, aptly named ‘Default’.
In the Identity v3 API, the uniqueness of attributes is as follows:
Domain Name. Globally unique across all domains.
Role Name. Unique within the owning domain.
User Name. Unique within the owning domain.
Project Name. Unique within the owning domain.
Group Name. Unique within the owning domain.
Due to their container architecture, domains may be used as a way to delegate management of SODA resources. A user in a domain may still access resources in another domain, if an appropriate assignment is granted.

## Assignment
The Assignment service provides data about roles and role assignments.

## Roles
Roles dictate the level of authorization the end user can obtain. Roles can be granted at either the domain or project level. A role can be assigned at the individual user or group level. Role names are unique within the owning domain.

## Role Assignments
A 3-tuple that has a Role, a Resource and an Identity.

## Token
The Token service validates and manages tokens used for authenticating requests once a user’s credentials have already been verified.

## Catalog
The Catalog service provides an endpoint registry used for endpoint discovery.

## Policy
The Policy service provides a rule-based authorization engine and the associated rule management interface.

## Authorization (Policy)
Various components in the system require that different actions are allowed based on whether the user is authorized to perform that action.
For the purposes of keystone there are only a couple levels of authorization being checked for:
Require that the performing user is considered an admin.
Require that the performing user matches the user being referenced.
Other systems wishing to use the policy engine will require additional styles of checks and will possibly write completely custom backends. By default, keystone leverages policy enforcement that is maintained in oslo.policy.

## Rules
Given a list of matches to check for, simply verify that the credentials contain the matches. For example:
credentials = {'user_id': 'foo', 'is_admin': 1, 'roles': ['nova:netadmin']}

**An admin only call:**
policy_api.enforce(('is_admin:1',), credentials)

**An admin or owner call:**
policy_api.enforce(('is_admin:1', 'user_id:foo'), credentials)

**A netadmin call:**
policy_api.enforce(('roles:nova:netadmin',), credentials)
 
Credentials are generally built from the user metadata in the ‘extras’ part of the Identity API. So, adding a ‘role’ to the user just means adding the role to the user metadata.

## Authentication :
Keystone provides several authentication plugins that inherit from keystone.auth.plugins.base. The following is a list of available plugins.
keystone.auth.plugins.external.Base
keystone.auth.plugins.mapped.Mapped
keystone.auth.plugins.oauth1.OAuth
keystone.auth.plugins.password.Password
keystone.auth.plugins.token.Token
keystone.auth.plugins.totp.TOTP
In the most basic plugin password, two pieces of information are required to authenticate with keystone, a bit of Resource information and a bit of Identity.
Take the following call POST data for instance:
```    {
        "auth": {
            "identity": {
                "methods": [
                    "password"
                ],
                "password": {
                    "user": {
                        "id": "0ca8f6",
                        "password": "secretsecret"
                    }
                }
            },
            "scope": {
                "project": {
                    "id": "263fd9"
                }
            }
        }
    }
```
 
The user (ID of 0ca8f6) is attempting to retrieve a token that is scoped to project (ID of 263fd9).
To perform the same call with names instead of IDs, we now need to supply information about the domain. This is because usernames are only unique within a given domain, but user IDs are supposed to be unique across the deployment. Thus, the auth request looks like the following:
 ``` 
   {
        "auth": {
            "identity": {
                "methods": [
                    "password"
                ],
                "password": {
                    "user": {
                        "domain": {
                            "name": "acme"
                        }
                        "name": "userA",
                        "password": "secretsecret"
                    }
                }
            },
            "scope": {
                "project": {
                    "domain": {
                        "id": "1789d1"
                    },
                    "name": "project-x"
                }
            }
        }
    }
```
 
For both the user and the project portion, we must supply either a domain ID or a domain name, in order to properly determine the correct user and project.
Alternatively, if we wanted to represent this as environment variables for a command line, it would be:
$ export OS_PROJECT_DOMAIN_ID=1789d1
$ export OS_USER_DOMAIN_NAME=acme
$ export OS_USERNAME=userA
$ export OS_PASSWORD=secretsecret
$ export OS_PROJECT_NAME=project-x
 
Note that the project the user is attempting to access must be in the same domain as the user.
Scope
Scope is an overloaded term.
In reference to authenticating, as seen above, scope refers to the portion of the POST data that dictates what Resource (project or domain) the user wants to access.
In reference to tokens, scope refers to the effectiveness of a token, i.e.: a project-scoped token is only useful on the project it was initially granted for. A domain-scoped token may be used to perform domain-related function.
In reference to users, groups, and projects, scope often refers to the domain that the entity is owned by. i.e.: a user in domain X is scoped to domain X.
 
**TODO : Requirement Analysis**

-  List of requirements

    - Users should be managed in Groups. 
    - Admins / Group Admins should be able to see resources allocated for each groups
    - Admins should be able to drill down Groups -> User details / consumptions / Performance etc..
 

 ![User Management Hierarchy](resources/usermgmt-figure-1.png?raw=true "User Management Hierarchy")


The Figure (Fig-1) depicts a high level hierarchy of actors in SODA. 
**SODA Admin :** These are the persona with the overall administrator CRUD rights to all the resources in SODA. This admin can create, update, delete users, groups and projects in SODA. This persona can also house the un-assigned users and projects. Once the users are assigned to projects, the user would move to the appropriate project.
 

|Actor Details|                        Functionality                                        |
|-------------|--------------------------------------------------------------------------------|
| <br>Actions     | Can Create / Modify/ Delete users, Group Admins <br> Can onboard Tenants <br> Can onboard CXOs. <br> Can assign Group Admin / Site   Admin for a Group / Site. <br> Can play the role of SODA   Deployment                                        |
| Hierarchy   | Top most user in   SODA Users Hierarchy.                                       |
| Access      | Has access to   all the Infrastructure / Tenentas / Users / Groups in SODA.    |
-------------

**CXOs :** These are the personas that have the READONLY access to all the resources being monitored and managed by SODA. These are the personas that are interested in charts and reports of SODA. They may not perform any management function in SODA. 

| Actor Details  |                           Functionality                                |
|----------------|------------------------------------------------------------------------|
|    <br>Actions | Read only access to the SODA Dashboard and   Reports <br> Can generate reports <br> Can access Audit reports <br>                                                                 |
| Hierarchy      | Top level user ,   peer of SODA Admin.                                 |
| Access         | Has access to   the whole SODA resources and users created in SODA.    |
|
                                                                         
**Projects (Tenants) :** The term Project refers to a set of Infrastructure resources that is assigned to an organization eg. HR , Finance etc. 
 
**Groups :** A group has a set of projects and a set of users that work on the projects. The Group may contain a Group Admin to perform the administrative tasks on the resources and users in the group. 
**Group Admins :** These are the admins that are housed under SODA Admin. These admins may be responsible for managing small projects, its resources and users working on the project. 

| Actor Details |                           Functionality                                                                                                                                                                                                                                                                         |
|---------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Actions       | Create a group<br>Delete a group<br>Update a group (change its name or description)<br>Add a user to a group<br>Remove a user from a group<br>List group members<br>List groups for a user<br>Assign a role on a project to a group<br>Assign a role on a domain to a group<br>Query role assignments to groups |
| Hierarchy     | Under SODA Admin                                                                                                                                                                                                                                                                                                |
| Access        | Has access to <br>all the Users in the Group<br>All the resources /projects/tenants in the Group<br>No Group Admin can exist without any Tenants or Users.                                                                                                                                                      |
|


**Role:** a first-class piece of metadata associated with many user-project pairs.
TBD : Inheritance of roles. [Delfin / Multi Cloud]

**Token:** identifying credential associated with a user or user and project

**Extras:** bucket of key-value metadata associated with a user-project pair.

**Rule:** describes a set of requirements for performing an action.

**Users:** Lastly, the user is the persona that works on the resources. There could be a few types of users in SODA. 


**CXO User :** This kind of persona works on behalf of the CXO and has only READONLY rights on the resources in SODA. 

**Resources :** These are the infrastructure resources being monitored and managed by SODA. These could include Storage Arrays, Volumes, Objects Hosts etc. that are being managed by SODA.


 ![User Management Entity Relationship Diagram](resources/usermgmt-figure-2.png?raw=true "User Management Entity Relationship Diagram")
 
**Non - Functional Goals**

**Multi-tenancy :** Providing multi-tenancy to users is very critical for maintaining data safety and privacy. This can be provided in multiple ways some of them are   
**Database per Tenant: **Each Tenant has its own database and is isolated from other tenants.
Shared Database, Shared Schema: All Tenants share a database and tables. Every table has a Column with the Tenant Identifier, that shows the owner of the row.
Shared Database, Separate Schema: All Tenants share a database, but have their own database schemas and tables.
 
 
 
SODA Component Interactions with Keystone. 
![Keystone - Service Interaction ](resources/usermgmt-figure-3.png?raw=true "Keystone - Service Interaction Diagram")

The **Figure-3** presents the interactions of various SODA modules with the Identity module , Keystone. As can be seen Keystone is at the heart of interactions between the components of SODA and the user. At a high level any request / response interaction of a user with SODA has the following steps

User sends credentials (user/password)  to get Authenticated from Keystone. 
Once the User is authenticated successfully, it is assigned a token. 
This token is sent along with any requests to any of the components that the user wishes to interact with. The request (Request + Token) is sent, and the component validates the token with the Identity service .
Once the token is validated, the response is returned to the user.

Now, not all users can perform the same function on SODA, i.e each user has a set of roles and responsibilities assigned to them with respect to each module. 

Here is a breakdown of all the functions that can be performed from each module. 
At present, only Admin and non-Admin roles are considered. Other roles can be added as per inputs from users. 

The primary components are 

**Delfin :**
Delfin Requirement analysis brainstorming notes :-  https://docs.google.com/presentation/d/1Kmhfy4iLQpMtol9whjIkN7R--NYaTbaKY7gYBytjkts/edit#slide=id.p


**Backend Credential Service.** 

In SODA , we monitor and manage heterogeneous storages both in the Cloud and On-premises. These Storage Backends need to be accessed using the Credentials for each of these backends, which are secrets and need to be preserved in a secure store like a Vault, which provides encryption. 

The Backend Credentials are a secret resource accessible only to the Administrator of the SODA Infrastructure. Only the Administrator can perform CREATE, READ , UPDATE, DELETE operations on them. The other users have only READONLY rights on the encrypted Credentials from the Vault. However, these credentials are accessible to the users only by the virtue of the profiles of the Users. 
 
For eg. When a User has a profile created to access Storage Tiers e.g Diamond , Gold , Silver etc OR any particular storage category. Users will be able to access only those Storage entities listed in the Diamond, Gold , Silver Tiers respectively. In cases where users need access to multiple backends, the user will require access to both the backends in their profiles, to enable the users to perform operations on the multiple backends listed. 

SODA Component Interactions with Keystone. 
![Vault - SODA User Interaction ](resources/usermgmt-figure-4.png?raw=true "Vault - SODA User Interaction Diagram")

As depicted in the Fig-4 ,  the Backend Credentials are added in the Vault by the Admin. 
The Admin has the rights to perform CREATE , READ, UPDATE, DELETE Operations of the Backend Credentials. 

Once the Credentials are updated in the Vault, they can be read (in encrypted format) by the users. The Users can make use of the Credentials to authenticate themselves against the Backends. Thereafter, they can execute any operations on the Backend. 

The permission to the Users is granted through the profiles. These profiles are created and assigned to the User by the Admins. The User is assigned the appropriate profiles to access the Backends assigned to the Groups to which the Users belong. 



