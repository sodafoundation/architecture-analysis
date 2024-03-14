# **TARS - A Microservice Platform for Efficient Service Management**

## Introduction
**TARS is an open-source microservice platform designed for high reliability, high performance, and efficient service management. Based on TARS, developers can efficiently develop a reliable microservice system, significantly reducing system operation work and enabling them to focus on business logic and meet fast changes of user requirements.**

TARS is an open-source version of TAF, which has been used in Tencent for years. The name "TARS" comes from the friendly and efficient robot in the movie Interstellar.

### TARS Features
- Protocol: TARS protocol is a binary, extensible, and cross-platform implementation for IDL language. This allows servant objects implemented in different languages to communicate with each other through RPC calls.
- 
- Trace: The framework can trace a specific request of a service and report logs for traced requests to a log server for users to analyze and diagnose problems.
- Monitor: The framework supports data report functions to monitor the quality of service process and business running status.
- DEV-MODE: Developers define the service interface via IDL, and TARS generates codes for communication between client and server. Service only needs to implement service logic, and the client uses the generated code to call the service.
- Balance: The framework uses name service for service register and discovery, and a specified load balance policy to call servers.
- GROUP: TARS groups servers into sets according to their locations to reduce response time of calls among servers and minimize the influence of network failure.
- CONFIG: Server configurations are managed by tarsweb, allowing developers to change configurations safely in a webpage and check the history of configuration changes and rollback to previous versions.
- SHIELD: TARS implements name service excluded and client shielding to exclude fail server from the address list and remove inactive server from the server list for client shielding.
- PROTECT: TARS handles scenarios of burst requests or machine fault to avoid overloading the system and improve system throughput.
TARS Supported Platforms and Languages
TARS supports Linux, Windows, and MAC platforms, and C++, Java, Nodejs, PHP, and Go languages.

### TARS Projects
- **TARS has several projects, including:**

- TARS LAB: JMeter, Benchmark, TarsJavaStart, TarsTools
- Service Governance: Tseer, Gateway
- Storage: DCache
- Infrastructure: K8Stars
- Technical Analysis Add technical analysis here.

### Supported platforms
- For now it supports OS as below:

 - Linux
 - Windows
 - Mac

### Supported languages
- For now it supports following languages:

 - C++
 - Java
 - Nodejs
 - PHP
 - Go

### License

- **Overview**
BSD 3-Clause License is a permissive open-source software license that allows users to freely use, modify, and distribute the software under certain conditions. It is also known as the "New BSD License" or "Modified BSD License".

- **Key Points**
  - BSD 3-Clause License is a permissive license that allows for free use, modification, and distribution of the software under certain conditions.
  - The license requires attribution to the original author and distribution of the license terms with any distribution of the software.
  - BSD 3-Clause License also includes a disclaimer of liability, stating that the software is provided "as is" and without warranty of any kind.
  - The license does not require any modifications or derivatives of the software to be released under the same license.
  - BSD 3-Clause License is compatible with other open-source licenses, including the GNU General Public License (GPL) and the Apache License.
 
- **Conditions**
  - Redistribution of the software must include the license terms and copyright notice.
  - Redistribution of the software must not use the names of the original authors to promote or endorse the software without prior written permission.
  - Redistribution of the software must not use the names of the original authors to imply endorsement of any derivative works without prior written permission.
 
- **When to Use**
  - BSD 3-Clause License is a good choice for software projects that require a permissive license that allows for free use and modification of the software, while still protecting the rights of the original authors. It is also a good choice for projects that may want to incorporate code from other open-source projects that use a compatible license.


### Other Links

| Column 1 | TARS |
| -------- | -------- |
| Website | [Link](https://tarscloud.org/) |
| TARS Projects | [Project](https://tarscloud.org/foundation/projects) |
| Email | [Mail](tars@tarscloud.org) |
| GitHub | [Link](https://github.com/TarsCloud) |
| Twitter | [Tweet](https://twitter.com/TarsCloud) |


