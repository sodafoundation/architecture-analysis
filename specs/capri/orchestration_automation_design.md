# Orchestration and Automation Design

**Authors:** [Himanshu](https://github.com/himanshuvar), [Erik Xu](https://github.com/wisererik), [Ashit](https://github.com/kumarashit )

This document proposes a design for automation and orchestration design.
## Background
OpenSDS is aimed at addressing the storage integration challenges of both the cloud native environment and traditional IT environment. As part of this mission, Orchestration and Automation will be introduced in this document. 

It is well known that in real distributed environment, most business processes consist of several different interconnected steps that need to be performed in a specific order. Sometimes these business processes need to interact with other system. If these complex business processes are interfered by users, they are error prone. Therefore, we should provide a mechanism to reduce manual intervention  and provide ease of use.  

The goal of orchestration is to streamline and optimize frequent, repeatable processes to ensure accurate and faster execution of services. For OpenSDS, orchestration can be used to optimize the repeatable process such as Volume Provision, Bucket Migration etc. in order to streamline complex flows. Therefore, Automation for OpenSDS which can be built into a series of processes and workflows, can be orchestrated to run automatically, to provide more consistent and reliable standardized processes.

There are some use cases that’s related to orchestration and automation:

* Autoscaling 
Auto scaling is always used in the cloud native scenario. Users will define some scaling policies that increases or decreases proportionally to the number of compute instances or the capacity of volume according to a utilization metric.

* Data migration              
Data migration is the process of moving data from one location to another or moving one application to another. This is usually the result of introducing a new system or location for the data. There are some kinds of data migration, such as storage-based migration, host-based migration, application-based migration, cloud-based migration, and more. All of these data migration processes include many steps, such as selecting a data source, loading data, transforming the data and moving the data to a new location, checking the environment, and so on. Some of them are long-running steps that may cause data migration to fail if there is no autonomic mechanism.

* Big data analysis                   
Data has been a driving factor in many industries and organizations, especially in financial companies. They store financial data on private data center for the purpose of security. And they typically replicate data to the cloud and use big data analytics to capture financial data for new insights and an understanding of business, market, performance and growth.


## Objectives
### Goals
*  Define actions based on OpenSDS
*  Define generic services based on the above use cases
*  Provide flexibility for users to customize their services
*  Show orchestrations in OpenSDS dashboard
*  Provision a Volume to workload (Capri)
### Non Goals
* How to use Workflow Manager i.e. Stackstorm implement automation
* Migration (Host/Array based) (Next release)
## Architecture Diagram


* **Service Catalog Manager**: It will provide a convenient way to manage service catalog which  represents a variety of classic business process that will enable organizations improve user experience,  accelerate service delivery and reduce operational costs.  User can register service catalog based on their business requirement. A service catalog instance will be generated when user executes service catalog with specific input. In addition, the history of execution can be queried when the service catalog is being executed or has been completed.
User can write a YAML or JSON template to register a service catalog which represents a specific user’s business process. `Service`, `Workflow` and `Task` can be defined inside it. A Service contains a couple of workflows, each workflow consists of multiple tasks. Every task can have one or more than one action.

* **Connection Manager**: Connection Manager handles the workflow connector. It manages the connection between the OpenSDS Service Manager and the workflow manager. There can be different adapters which acts as workflow connector for OpenSDS workflow orchestration. Connector acts as an OpenSDS orchestration adapter for inbound and outbound integration with the Workflow Manager. In the current implementation, we use Stackstorm as the Workflow Manager and connector connects StackStorm to handle the interaction between service catalog manager and StackStorm.

**StackStorm**: It’s an `IFTTT` framework which offers advanced automation, letting users create complex workflows to automate your world. Stackstorm breaks automation down into discrete actions and ties these actions together into workflows that can perform complex operations. StackStorm also provides a sophisticated event bus that can be used to implement event-based automation. Stackstorm enables OpenSDS Orchestration and Automation for the generic workflow actions based on OpenSDS projects such as hotpot, gelato and so forth.

###Terminology

 Service Catalog Manager
It manages service catalogs and is responsible for maintaining the Service Catalog, ensuring that all information within the Service Catalog is accurate and up-to-date.

 Service
It represents user defined business process. User can write a YAML or JSON template to register a service which on validation registers a workflow. Also, it can be pre-defined workflows shipped with the product.

 Instance
It is a consumable service. Once the registration is done, user can consume the service.

 Execution
A service instance can be executed immediately or scheduled later. It helps in tracking execution progress, result of service instance.

 Connection Manager
Connection Manager handles the workflow connectors. It manages the connection between the OpenSDS Service Manager and the workflow manager.

 Connector
It is the OpenSDS orchestration adapter for inbound and outbound integration with the Workflow Manager.  

 Workflow Manager
It is the external framework to organize, centralize, and standardize workflows. 

 Workflow
It stitches actions together, define the order, transition conditions, and passing the data. 

 Actions
Actions are pieces of code that can perform arbitrary automation tasks.

## Design details
![Orchestration Design Diagram](Orchestration_and_Automation_Design.png?raw=true "Orchestration Design Diagram")

### Service 
As mentioned above, services are diversified and vary according to users’ business requirement. Therefore, we provide a flexible way to enable users to flexibly define their own services based on existing action capabilities. User can define a template written in YAML or JSON to register a service. The system verifies that the template is valid and parses the template, then creates the service and registers the workflow in Workflow Manager. 





A service template structure looks like the following file, some ideas got from OpenStack Heat template:

```yaml
---
version: 1.0
name: <string>
description: <string>
parameters: 
  <param name>:
    type: <string | number | boolean | json | array>
    description: <string>
    default: <default value>
    constraints:
      <json>
workflows:
  <workflow name>:
    description: <string>
    input: <parameters defined above>
    output: <json>
    tasks: 
      <task name>:
        action: <action reference>
          requires: <array of tasks>
          output: <json>
          on-success: 
            - <next task>
          on-fail: 
            - <next task>      
output: <json>
```
### Instance
Once the service is successfully registered, the user can consume the service. A consumed service is called an instance. After the instance is created, it can be executed immediately or delayed. Service catalog manager will read parameters from instance, then initiate and execute a specific workflow in Workflow Manager. The following is the list of attributes of Instance model.

| Attribute | Type | Required | Description |
|-------------|--------|--------------|-----------------|
| id                | string  |  yes | Unique identifier that’s generated automatically in system.           |
| name          | string  |  yes | Name of service instance that distinguishes different instances.  |
| service_id   | string  | yes  | Reference of service.                                                                    |
| parameters | object | no    | Arguments inputted by user.                                                         |
| status          | string  | yes  | Valid values are `creating`, `available`, `running`, `completed` and `error`.                      |
| output          | object | yes   | result of instance execution.                                                         |

### Execution
When executing a service instance, some journals will be generated by system. User can retrieve execution list with a specified service instance and get a detail of executing result. These will help user know the executing progress or result of service instance. The following is the list of attributes of execution model.

| Attribute | Type | Required | Description |
|-------------|--------|--------------|-----------------|
| id                | string  |  yes | Unique identifier that’s generated automatically in system.           |
| instance_id | string  |  yes | Reference of service instance.                                                      |
| workflow      | object  |  yes | Declare which workflow is referenced.                                         |
| task             | string  |  yes | Declare which task is executed.                                                    | 
| action          | string  |  yes | Declare which action is executed.                                                 |
| input            | object | no    |  Arguments inputted by user.                                                         |
| status          | string  | yes  | Valid values are `succeed` and `failed`.                                        |
| start_time    | string  | yes  | Start time of action execution.                                                       |
| end_time     | string  | no    | End time of action execution.                                                        |
| output          | object | yes   | result of task execution.                                                                |

### Actions


 Volume Creation
	Description: Create a Volume and get the status of the created volume.
 
	POST /v1/{tenant_id}/block/volumes
	GET /v1/{tenant_id}/block/volumes/{volume_id}

 Volume Deletion
	Description: Delete a Volume and get the status.

	DELETE /v1/{tenant_id}/block/volumes/{volume_id}
	GET /v1/{tenant_id}/block/volumes/{volume_id}

 Volume Attachment   
	Description: Create a Volume attachment to the host and get the volume attachment status details.

	POST /v1/{tenant_id}/block/attachments
	GET /v1/{tenant_id}/block/attachments/{attachment_id}
 Volume Detachment
	Description: Delete a volume attachment from the host and get the volume attachment status details.

	DELETE /v1/{tenant_id}/block/attachments/{attachment_id}
	GET /v1/{tenant_id}/block/attachments/{attachment_id}

 Volume Expansion
	Description: Expand a Volume and get the size details.

	POST /v1/{tenant_id}/block/volumes/{volume_id}/resize
	GET /v1/{tenant_id}/block/volumes/{volume_id}

 Volume Updation
	Description: Delete a Volume and get the status.

	PUT /v1/{tenant_id}/block/volumes/{volume_id}
	GET /v1/{tenant_id}/block/volumes/{volume_id}

 Volume Attachment Updation
	Description: Update a volume attachment for the host and get the attachment status details.

	PUT  /v1/{tenant_id}/block/attachments/{attachment_id}
	GET /v1/{tenant_id}/block/attachments/{attachment_id}

 Volume Group Creation
	Description: Create a Volume Group and get the volume group details.

	POST /v1/{tenant_id}/block/volumeGroups
	GET /v1/{tenant_id}/block/volumeGroups/{volumeGroup_id}

 Volume Group Deletion
	Description: Delete a Volume Group and get the volume group details.

	DELETE  /v1/{tenant_id}/block/volumeGroups
	GET /v1/{tenant_id}/block/volumeGroups/{volumeGroup_id}

 Volume Group Updation
	Description: Delete a Volume Group and get the volume group details.

	PUT  /v1/{tenant_id}/block/volumeGroups
	GET /v1/{tenant_id}/block/volumeGroups/{volumeGroup_id}

 Volume Snapshot Creation
	Description: Create a Volume Snapshot and get the volume snapshot details.

	POST /v1beta/{tenant_id}/block/snapshots
	GET /v1beta/{tenant_id}/block/snapshots/{snapshot_id}

 Volume Snapshot Deletion
	Description: Delete a Volume Snapshot and get the volume snapshot details.

	DELETE /v1beta/{tenant_id}/block/snapshots
	GET /v1beta/{tenant_id}/block/snapshots/{snapshot_id}

 Volume Snapshot Updation
	Description: Update a Volume Snapshot and get the volume snapshot details.

	PUT /v1beta/{tenant_id}/block/snapshots
	GET /v1beta/{tenant_id}/block/snapshots/{snapshot_id}

 Snapshot Volume Creation
	Description: Create a Volume Snapshot and get the volume snapshot details. Then Create a volume using the same snapshot_id and get the status of the created volume.

	POST /v1beta/{tenant_id}/block/snapshots
	GET /v1beta/{tenant_id}/block/snapshots/{snapshot_id}
	POST /v1/{tenant_id}/block/volumes
	GET /v1/{tenant_id}/block/volumes/{volume_id}

 Volume Replication Creation
	Description: Create a Replication relationship and get the status of the replication details.

	POST /v1/{tenant_id}/block/replications
	GET /v1/{tenant_id}/block/replications/{replication_id}

 Replication FailOver
	Description: Failover a replication and get the status of the replication details. Replication status should be failed-over.

	POST /v1/{tenant_id}/block/replications/{replication_id}/failover
	GET /v1/{tenant_id}/block/replications/{replication_id}

	Get the status of the Volume whether it is mounted or not. Volume status should be available or in-use.

	GET /v1/{tenant_id}/block/volumes/{volume_id}

 Volume Migration Plan Execution
	Description: Perform the migration and get the status of the migration status.This step includes syncing of data.

	POST v1/{tenant_id}/block/migrations/{id}/migrate
	POST v1/{tenant_id}/block/migrations/{id}/status

 Multi-Cloud Storage Backend Registration
	Description: Create a backend and get the created storage backend details. 

	POST /v1/{tenant_id}/backends
	GET /v1/{tenant_id}/backends/{backend_id}

 Multi-Cloud Storage Backend Deletion
	Description: Delete a backend and check for the deleted backend. 

	DELETE /v1/{tenant_id}/backends
	GET /v1/{tenant_id}/backends/{backend_id}

 Multi-Cloud Storage Backend Updation
	Description: Update a backend and get the updated storage backend details. 

	PUT /v1/{tenant_id}/backends
	GET /v1/{tenant_id}/backends/{backend_id}

 Multi-Cloud Bucket Creation
	Description: Create a local bucket and get the details for the created bucket.

	PUT /v1/s3/{bucketName}
	GET /v1/s3/

 Multi-Cloud Bucket Deletion
	Description: Delete a local bucket and check for the deleted bucket.

	DELETE /v1/s3/{bucketName}
	GET /v1/s3/

 Multi-Cloud Object Upload
	Description: Upload an object to the cloud storage and get the details for the uploaded object.

	PUT /v1/s3/{bucketName}/{object}
	GET /v1/s3/{bucketName}

 Multi-Cloud Object Download
	Description: Download an object from the cloud storage and get the details for the downloaded object.

	GET /v1/s3/{bucketName}/{object}
	GET /v1/s3/{bucketName}

 Multi-Cloud Object Deletion
	Description: Delete an object from the cloud storage and get the details for the downloaded object.

	DELETE /v1/s3/{bucketName}/{object}
	GET /v1/s3/{bucketName}

 Multi-Cloud Data Migration Plan Creation
	Description: Create a migration plan and get the details for the created plan.

	POST /v1/{tenant_id}/plans
	GET /v1/{tenant_id}/plans/{plan_id}


 Multi-Cloud Data Migration Plan Execution
	Description: Execute a migration plan and get the status for the migration job.

	POST /v1/{tenant_id}/plans/{plan_id}
	GET /v1/{tenant_id}/jobs/{job_id}





### Workflows

## Security impact
Keystone will serve as a identification component for multi-tenancy capabilities for Orchestration. Design principles include:
- The administrator has access to all APIs.
- For tenants, they can only query service, can have access to all the interfaces of instance and execution.
- Tenants cannot access each other's resources.


## API impact

### List Services
List all service catalog information.

### Service
- Register Service 
- Unregister Service 
- List Service 
- Get Service by id

Here’s example about register a service.

POST /v1/{tenantId}/catalog/services

Content-Type: application/json
Accept: application/json

JSON Request:
```json
{
   "version": 1,
   "name": "provision service",
   "description": "provide a volume to a host",
   "parameters": {
      "volume_name": {
         "type": "string",
         "constraints": {
            "allowed_pattern": "[A-Z]+[a-zA-Z0-9]*"
         }
      },
      "profile": {
         "type": "string",
         "default": "default"
      },
      "volume_size": {
         "type": "integer",
         "default": 1
      },
      "host": {
         "type": "string"
      }
   },
   "output": "<% $.volume_id %>"
   "workflows": {
      "provision-workflow": {
         "input": ["volume_name", "profile", "volume_size", "host"],
         "tasks": {
            "create-volume": {
               "action": "opensds.create-volume",
               "publish": {
                  "volume_id": "<% task(create-volume).result.volume_id %>"
               },
               "on-success": ["mapping-volume-to-host"]
            },
            "mapping-volume-to-host": {
                "action": "opensds.attach-volume",
                "on-success": ["notify"]
            },
            "notify":
                "action": "core.local",
                "input": {
                  "cmd: "printf 'volume <% $.volue_id %> was attached to host successfully'"
                }
         }
      }
   }
}
```
### Instance
- Create service instance??will run immediately
- Run a service instance
- List service instances
- Get service instance
- Delete service instance

With Capri release, there will be pre-defined workflows shipped with the product. User having appropriate privileges can define Service defintions based upon the workflows. Once the service definition is created, user with appropriate role and intent can create a Service instance. Once a service instance is created, user can track the status.
API details can be found at:
http://petstore.swagger.io/?url=https://raw.githubusercontent.com/opensds/orchestration/9869f5cb5fb740e91700c6200e67327b3dae0c6a/openapi-spec/swagger.yaml

Here’s example about creating an instance.

POST /v1/{tenantId}/catalog/instances

Content-Type: application/json
Accept: application/json

JSON Request:
```json
{
   "name": "provision service instance",
   "service_id": "a66976e0-9fbf-4cf3-912a-e891dd41b1a5",
   "description": "provide a volume to a host",
   "parameters": {
      "volume_name": "example_vol1",
      "profile": "default",
      "volume_size": 10,
      "host": {
         "id": "string",
         "ip": "10.0.0.2",
         "host_name": "ubuntu001"
      }
   }
 }
```

### Execution ?? move execution api to instance part

- List executions of service instance
- Get execution by id
- Get execution topology by service instance??
- Re-run a failed or suspended execution??


Here’s example of getting an instance execution.

GET /v1/{tenantId}/catalog/executions/a66976e0-9fbf-4cf3-912a-e891dd41b1a5

Content-Type: application/json
Accept: application/json

JSON Response:
```json
{
   "id": "a66976e0-9fbf-4cf3-912a-e891dd41b1a5",
   "instance_id": "a66976e0-9fbf-4cf3-912a-e891dd41b1a5",
   "workflow":"provision-workflow",
   "task": "create-volume",
   "action": "opensds.create-volume",
   "input": {
      "volume_name": "string",
      "profile": "string",
      "volume_size": 11,
   },
   "status": "running",
   "start_time": "Thu, 03 Apr 2019 23:34:02 UTC"
 }
```

## Open discussion

## References
[1] Stackstorm
https://docs.stackstorm.com/

## Addendum
Sample provisioning yaml file:
---
Description: "Volume provisioning"
Name: provision
Technology: st2
enabled: true
entry_point: workflows/createvol
parameter:
  -
    volsize:
      description: "Size of the volume to be created"
      required: true
      type: integer
  -
    volname:
      description: "Name of the volume to be created"
      required: true
      type: string
  -
    hostname:
      description: "Name of the node to which the volume will beattached"
      required: true
      type: string
runner_type: action-chain
workflows:
  -
    createvol:
      Actions:
        createvolume:
          parameters:
            -
              volsize:
                type: integer
            -
              volname:
                type: string
          ref: create_vol
        on-failure: ~
        on-success: attachvol
      description: "Create a volume of given size"
  -
    attachvol:
      Actions:
        attachvolume:
          parameters:
            -
              hostname:
                type: integer
            -
              volname:
                actionName: createvol
                isParamPassed: true
                type: string
          ref: attach_vol
        on-success: ""
      description: "Attach a given volume"
