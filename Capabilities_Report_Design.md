# Capabilities Report Design

**Status**: WIP

**Version**: Alpha

**Author(s)**: Leon Wang (@leonwanghui)

## Summary

This proposal is drafted for building capabilities report mechanism for opensds storage backends, where a unified southbound interface is designed to standardize properties definition of opensds resource properties so as to provide some inputs for resource scheduling mechanism in opensds controller. 

## Motivation

As a unified storage reousrce management controller, it's indispensable to enable resource scheduling features for opensds, so the controller NEEDS to perceptive of all backend capabilities (such as capacities, configuration, advanced features and so forth). But currently what's standing in the way is that capabilities report methods in different storage systems are far different. For example, there is few property reported in LVM while some vendor sotrage systems can expose a lot of features. So we should design a standard capabilities report interface to integrate different storage drivers and establish capabilities discovery workflow to close the cycle of report mechanism.

### Goals

  * Design a unified API interface to collect capabilities.

  * Design a workflow of capabilities discovery.

### Non-Goals

  * Integration to different storage drivers.

  * Gap analysis of capabilities report feature among different backends.

## Proposal

Currently [Hotpot](https://github.com/opensds/opensds) project contains two services: ```osdslet``` service works as a controller to provide unified storage management, in the meantime ```osdsdock``` service runs on all storage nodes as a distributed agent. Since the capabilities report mechanism is a service from bottom to up, the service process should run on storage nodes and be triggered in polling mode. The concrete design would be introduced below.

### Data model impact

The proposal could imporve some properties in ```PoolSpec```, but considering the current format of ```ExtraSpec``` is map[string]interface{}, there would be no impact to the data model.

Here is a sample JSON object of updated ```PoolSpec```:
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
    "DataStorageLineOfService": {
      "RecoveryTimeObjective":0, # both thin and thick are supported in a pool
      "ProvisioningPolicy":["Thin", "Fixed"],
      "IsSpaceEfficient":true
	},
	"IOConnectivityLineOfService": {
      "AccessProtocol":"FC",
      "MaxIOPs":null
    },
    "DataProtectionLineOfService": {},
    "xxx": {}
  }
}
```

### REST API impact

None

### Security impact

This proposal brings IPC mechanism, so we should take care of message synchronization and protect the data from loss if system crashes.

***Q1***: If the main thread change the status of backend to ```error```, how can report thread accept this signal and jump out of the loop? What if the backend service comes alive again?

***Q2***: If the main thread crashes down, how to clean the report thread and notify ```osdslet``` daemon? 

### Other end user impact

None

### Performance impact

None

### Other deployer impact

None

### Developer impact

None

## Use Cases

User sets up a three-nodes opensds cluster: one controller node(running osdslet daemon) and two storage nodes(run osdsdock daemon seperately in LVM and Ceph cluster). Because user doesn't know any idea about backend storage type, so he only needs to specify a profile and opensds will handle all remaining work to create storage instance:

  * Expose what every profile contains to user with properties collected from different storage backends.

  * Update pool properties in real time and notify admin to adjust the profile definition.

  * Keep in track with report thread to make sure it alive.

## Implementation (Chinese for now)

1.服务启动，读取配置文件信息，传给系统全局配置项变量```CONF```

2.从CONF中读取db endpoint，进行数据库初始化连接

3.初始化```dock.DockHub```结构体，并赋给全局变量```dock.Brain```

4.通过调用```DockHub```的私有函数TriggerDiscovery实现后端存储能力的收集与上报

  1）初始化```DockDiscoverer```结构体，从CONF中导入所有的```model.DockSpec```信息

  2）调用一个异步进程执行DiscoverAndReport函数，创建一个loop循环并传入一个用于stop的channel

  3）loop循环内部首先调用南向的ListPools接口用于实现后端能力的收集，然后通过etcd的存储功能实现能力上报

5.初始化gRPC的server实例，针对配置的endpoint进行服务监听

## Alternatives considered

None
