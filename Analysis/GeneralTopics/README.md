# All the uncategorized / general technical analysis go here.

## Basic Layout Suggestion for your Analysis:
 - Topic Introduction
	 - [Project/Topic basic information, links, etc] 
 - Purpose
 - Technical Analysis
 - Recommendation/Suggestions

 ## TARS - Focus on Microservice Ecosystem

 ## Introduction
 Tars is a open-source microservice platform. It contains a high-performance RPC framework and a service management platform. Based on Tars, you can develop a reliable microservice system efficiently.

Tars is an open-source version of TAF which has being used in Tencent for years. The name, Tars, comes from the robot in the movie Interstellar. In the movie, Tars has a friendly interactive mode - everyone who first meets him can interact with him easily. The robot Tars can accomplish missions efficiently, even under hard conditions.

The open-source project Tars carries the same mission as robot Tars does. It is designed for high reliability, high performance, and efficient service management. By significantly reducing system operation work, developers can focus on business logic and meet fast changes of user requirements.

Right now, thousands of services in Tencent have been developed with TAF, running on tens of thousands of machines
 ##
 Tars comes from the robot in Interstellar movie. Tars is a high-performance RPC framework based on name service and Tars protocol, also integrated administration platform, and implemented hosting-service via flexible schedule.

Tars, aka TAF(Total Application Framework), has been used in Tencent since 2008. It supports C++,Java,Nodejs and php for now. This framework offers a set of solution for development, maintenance and testing, which making develop, deploy and testing service efficiently. It integrated extensible protocol for encoding/decoding, high-performance RPC communication framework, name service, monitor, statistics and configuration. You can use it to develop your reliable distributed application based on microservice fast, and reach fully efficient service management.

Nowadays it’s used by hundreds of bussiness in Tencent, services that developed base on TAF run on 16 thousands of machines.

## TARS Features:
- Protocol
	- TARS protocol is an implementation for IDL language. It is binary, extensible and cross-platform. <br>Thus, servant objects implemented in different languages can communicate with each other through RPC calls.
- Trace
	- The framework can trace a specific request of a service. the traced request is labeled and forwarded to server. <br>Then, servers report logs for traced request to log server. Users can analysis logs for traced request and diagnose problems.
- Monitor
	- The framework supports following data report function in order to monitor the quality of service process and business running status.
- DEV-MODE
	- Developers define service interface via IDL. Tars generates codes for communication between client and server. <br>Service only needs to implement service logic and client uses the generated code to call the service. The invoke modes split to 3 kinds: sync, async and one-way request.
- Balance
	- The framework uses name service for service register and discovery. Client gets server address list via name service, then it uses a specified load balance policy to call servers.<br>The supported load balance policies are round-robin, hash, weighted policy.
- GROUP
	- In order to reduce response time of calls among servers and minimize influence of network failure, tars supports IDC grouping to groups server according to their locations.<br> To facilitate service management, tars groups servers into Sets.
- CONFIG
	- Server configurations are managed by tarsweb. Developer can change configurations in a webpage making the change easily and the change is safer. <br> Developer can check history of configuration changes and rollback to previous version.
- SHIELD
	- It's implemented with two ways: name service excluded and client shielding. <br>Name service will exclude fail server from address list. And Client removes inactive server from the server list for client shielding.
- PROTECT
	- To avoid overloading the system because of burst requests or machine fault, tars handles this scenario in the framework. In order to improve system throughput, server uses request queue to process request asynchronously.




## Supported Platforms :
- Linux
- Windows
- MAC

## Supported Languages :
- C++
- Java
- Nodejs
- PHP
- Go

## TARS Projects :
- TARS LAB
	- JMeter
	- Benchmark
	- TarsJavaStart
	- TarsTools
- Service Governance
	- Tseer
	- Gateway
- Storage
	- DCache
- Infrastructure
	- K8Stars

### Link for TARS Landscape: https://tarscloud.org/foundation/projects

# Technical Analysis:
Add Technical Analysis here.

## Other Links:
| -- | TARS |
|--|--|
| **Website** | https://tarscloud.org/ |
| **TARS Projects** |  https://tarscloud.org/foundation/projects|
| **License** | [BSD-3-Clause](License.md) |
| **TARS Projects** | https://github.com/TarsCloud |
| **Twitter** | https://twitter.com/TarsCloud |

## Contributing:
Contributing
If you meet any problems never found in historical issues, please submit one!

If you find any bugs or get any new ideas, please submit PR!

**NOTE: Before you submit PR, please check whether you signed CLA!!**