<![endif]-->

**TRILIO**

PROJECT ANLYSIS

**INTRODUCTION:**

Trillio is an AI-Driven application platform for developing enterprise-class applications. It's Natural Language Processing capabilities vastly simplify and accelerate application delivery. Trillo provides integration with an extensive ecosystem of cloud services from GCP, AWS, & Azure

At Trilio, we believe that data protection should be effortless, automated, and predictable. Our platform, TrilioVault delivers a modern data protection experience that gives customers more power and control over their cloud and container environments. Organizations of all sizes, from well-known brands to ambitious start-ups, rely on our cloud-native data protection software to manage and backup their applications so they can meet their Service Level Agreements (SLAs), compliance requirements and recover from planned or unplanned outages.

We are a growing team of experts who are deeply passionate about the work we do and the difference we make for our customers. We have adapted the best practices we learned from our roots in the world of enterprise software, data protection, security and cloud infrastructure.

**PROJECT SUMMARY:**

**Website**

**www.trilio.io**

**Organization/foundation Name**

**Trillio**

**Open/ Proprietary**

**Proprietary**

**Source path**

**Brief Description**

**Trillio is an AI-Driven application platform for developing enterprise-class applications. Its Natural Language Processing capabilities vastly simplify and accelerate application delivery .Trillo provides integration with an extensive ecosystem of cloud services from GCP, AWS, & Azure. It is a low-code offering and eliminates DevOps.**

**License**

**KEY FEATURES:**

<![if !supportLists]>Ø <![endif]>**_Agentless_**

<![if !supportLists]>Ø <![endif]>**_Non-Disruptive_**

<![if !supportLists]>Ø <![endif]>**_Multi-Tenant/Cluster_**

<![if !supportLists]>Ø <![endif]>**_Infinite scalability_**

<![if !supportLists]>Ø <![endif]>**_Self-service /Integrated Management_**

<![if !supportLists]>Ø <![endif]>**_Open Universal Backup Schema_**

**ARCHITECTURE:**

<![if !vml]>![](file:///C:/Users/acer/AppData/Local/Temp/msohtmlclip1/01/clip_image002.png)<![endif]>

TrilioVault is an add on service to OpenStack cloud infrastructure and provides backup and disaster recovery functions for tenant workloads. TrilioVault is very similar to other OpenStack services including nova, cinder, glance, etc and adheres to all tenets of OpenStack. It is a stateless service that scales with your cloud.

**MAIN COMPONENTS:**

<![if !vml]>![Graphical user interface, diagram

Description automatically generated](file:///C:/Users/acer/AppData/Local/Temp/msohtmlclip1/01/clip_image004.png)<![endif]>

TrilioVault has four main software components:

1.  TrilioVault ships as a QCOW2 image. User can instantiate one or more VMs from the QCOW2 image on a standalone KVM boxes.

<![if !supportLists]>2. <![endif]>TrilioVault API is a python module that is installed on all OpenStack controller nodes where the nova-api service is running.

<![if !supportLists]>3. <![endif]>TrilioVault Datamover is a python module that is installed on every OpenStack compute nodes.

<![if !supportLists]>4. <![endif]>TrilioVault horizon plugin is installed as an add on to horizon servers. This module is installed on every server that runs horizon service.

**SERVICE ENDPOINT:**

<![if !vml]>![](file:///C:/Users/acer/AppData/Local/Temp/msohtmlclip1/01/clip_image006.png)<![endif]>

TrilioVault is both a provider and consumer into OpenStack ecosystem. It uses other OpenStack services such as nova, cinder, glance, neutron, and keystone and provides its own service to OpenStack tenants. To accomodate all possible OpenStack deployments, TrilioVault can be configured to use either public or internal URLs of services. Likewise TrilioVault provides its own public, internal and admin URLs.

**NETWORK TECHNOLOGY:**

<![if !vml]>![](file:///C:/Users/acer/AppData/Local/Temp/msohtmlclip1/01/clip_image008.png)<![endif]>

This figure represents a typical network topology. TrilioVault exposes its public URL endpoint on public network and TrilioVault virtual appliances and data movers typically use either internal network or dedicated backup network for storing and retrieving backup images from backup store.

CURRENT USAGE:

With Organizations, customers can now create an umbrella layer that allof their users and accounts can belong to. Additionally, customers can register their company domain(s) so that _new_ users and accounts get automatically added to the Organization. This means spending less time switching through multiple accounts to find the right one.

They provide career growth potential for our people to expand their roles and solve new problems as we address the most important challenges in enterprise infrastructure.

TECHNICAL USAGE:

Trilio’s Continuous Restore capability enables migration and replication of stateful applications in seconds or minutes so that all companies can protect and use their data anywhere regardless of what the application runs on or where the data is stored,” continued Balcha. This provides organizations the ability to meet leading levels of application uptime, achieve Service Level Agreements (SLAs) expected of production-grade applications all at an affordable cost. Continuous Restore is a game-changer that will make today’s modern businesses even more competitive and resilient.
