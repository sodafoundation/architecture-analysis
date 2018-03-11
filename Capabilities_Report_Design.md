# Capabilities Report Design

**Status**: Completed

**Version**: Alpha

**Author(s)**: Leon Wang (@leonwanghui)

## Summary

This proposal is drafted for building capabilities report mechanism for opensds storage backends, where a unified southbound interface is designed to standardize properties definition of opensds resource properties so as to provide some inputs for resource scheduling mechanism in opensds controller. 

## Motivation

As a unified storage resource management controller, it's indispensable to enable resource scheduling features for opensds, so the controller NEEDS to perceptive of all backend capabilities (such as capacities, configuration, advanced features and so forth). But currently what's standing in the way is that capabilities report methods in different storage systems are far different. For example, there is few property reported in LVM while some vendor storage systems can expose a lot of features. So we should design a standard capabilities report interface to integrate different storage drivers and establish capabilities discovery workflow to close the gap of report mechanism.

### Goals

  * Design a unified API interface to collect capabilities.

  * Design a workflow of capabilities discovery.

### Non-Goals

  * Integration to different storage drivers.

  * Gap analysis of capabilities report feature among different backends.

## Proposal

Currently [Hotpot](https://github.com/opensds/opensds) project contains two services: ```osdslet``` service works as a controller to provide unified storage management, in the meantime ```osdsdock``` service runs on all storage nodes as a distributed agent. Since the capabilities report mechanism is a service from bottom to up, the service process should run on storage nodes and be triggered in polling mode. The concrete design would be introduced below.

### Data model impact

The proposal could improve some properties in ```PoolSpec```, but considering the current format of ```ExtraSpec``` is map[string]interface{}, there would be no impact to the data model.

Currently vendors need to define some basic properties (diskType, accessProtocol, thinProvisioned and compressed) in their drivers, besides they can also configure some advaced features optionally to build their own technical competitiveness. Here is a sample JSON object of updated ```PoolSpec```:

```json
{
  "id": "f4486139-78d5-462d-a7b9-fdaf6c797e1b",
  "createdAt": "2018-01-15T12:19:45.505Z",
  "updatedAt": "2018-01-15T12:19:45.505Z",
  "name": "pool01",
  "storageType": "block",
  "description": "",
  "status": "available",
  "availabilityZone": "az01",
  "totalCapacity": 1000,
  "freeCapacity": 699,
  "dockId": "ccac4f33-e603-425a-8813-371bbe10566e",
  "extras": {
    "diskType": "SSD",
    "accessProtocol":"FC",
    "thinProvisioned": true,
    "compressed": true,
    "advanced": {
      "recoveryTimeObjective": 0,
      "maxIOPS": 1000,
      "deduped": false,
      "xxx":{}
	}
  }
}
```

### REST API impact

None

### Security impact

This proposal brings IPC mechanism, so we should take care of message synchronization and protect the data from loss if system crashes.

### Other end user impact

None

### Performance impact

None

### Other deployer impact

None

### Developer impact

For storage drivers, developers NEED to fix the gap between capabilities exposed by storage systems and capabilities report interface. Specifically, they should implement ```ListStoragePools``` interface by themselves to make sure all required fields (see sample JSON object above) returned.

## Use Cases

User A sets up a three-nodes opensds cluster: one controller node(running osdslet daemon) and two storage nodes(run osdsdock daemon seperately in LVM and Ceph cluster). Because user doesn't know any idea about backend storage type, so A only needs to specify a profile and opensds will handle all remaining work to create storage instance:

  * Expose what every profile contains to user with properties collected from different storage backends.

  * Update pool properties in real time and notify admin to adjust the profile definition.

  * Keep in track with report thread to make sure it alive.

## Implementation

Here is the whole process of ```osdsdock``` service after implementing capabilities report features:

1. After the service initialized, the system will read all information from ```/etc/opensds/opensds.conf```, and pass the parameters into the global configuration variable ```CONF```

2. Get ```dbEndpoint``` field from ```CONF``` and connect to etcd server using this variable

3. Initialize ```DockHub``` structure defined in dock package, and pass the pointer of this instance to global variable ```Brain```

4. Enable capabilities collecting and reporting feature by calling ```TriggerDiscovery``` method which is a private method of ```DockHub```


  * Initialize ```DockDiscoverer``` structure defined in discovery package, import all properties about ```DockSpec``` from ```CONF``` and     pass them to the instance

  * Execute DiscoverAndReport method by calling a golang thread, and this thread will run a loop circle (see sample definition below):

```go
type Context struct {
	stopChan chan bool
	errChan chan error
	metaChan chan string	
}

func (dd *DockDiscoverer) DiscoverAndReport(d drivers.VolumeDriver, ctx *Context)
```

  * Inside the loop circle, the system will call ```ListStoragePools``` which is southbound interface to collect capabilities from storage backends, and then the report feature will be accomplished by storing the data in etcd

5. Initialize a ```Server``` instance of gRPC, and start listening specified endpoint from ```CONF```

## Alternatives considered

None

## Open issues

***Q1***: If the main thread change the status of backend to ```error```, how can report thread accept this signal and jump out of the loop? What if the backend service comes alive again?

***Q2***: If the main thread crashes down, how to clean the report thread and notify ```osdslet``` daemon? 
