# [Project Stork](https://joinup.ec.europa.eu/collection/spain-center-technology-transfer/solution/project-stork)

  
  

# INTRODUCTION

Stork is a set of command line helpers for Databricks. Some commands are for managing libraries in Databricks in an automated fashion. This allows you to move away from the point-and-click interface for your development work and for deploying production-level libraries for use in scheduled Databricks jobs. Another command allows you to create an interactive cluster that replicates the settings used on a job cluster.

  

**PROJECT SUMMARY**
| Website | [hi@portworx.com](mailto:hi@portworx.com) |
|--|--|
| Organization/ Foundation Name | # Portworx |
| License | BSD 2-Clause License |
|-Open/Proprietary-|-Open Source-|
| Source Code | Source Code |
| Brief description | Stork is an open source monitoring application and dashboard for ISC’s Kea DHCP, and eventually for ISC’s BIND 9. It is the spiritual successor of the earlier projects Kittiwake and Anterius. |
|  |  







  
  

**Website**

[hi@portworx.com](mailto:hi@portworx.com)

  

Organization/ Foundation Name

# Portworx

****License****

BSD 2-Clause License

Open/Proprietary

Open Source

Source Code

https://github.com/ShopRunner/stork.git

Brief description

Stork is an open source monitoring application and dashboard for ISC’s Kea DHCP, and eventually for ISC’s BIND 9. It is the spiritual successor of the earlier projects Kittiwake and Anterius.

  
  
  
  
  
  

![](https://lh5.googleusercontent.com/G5hEQKkgAVFJV9z24qQbclNIsbhMayurOf059_2x1yet6UnykD3g7B86aPqekGq2PtQu3VnF6cW5dZ54Zo1sbpNp4jsupcpnTift2R-WDIdvQTGXIZJ8LJw0_QEfYVjfKVXUzGu-nXMkj0Dd "horizontal line")

  

# PROJECT DETAILS

  

## Key Features

-   Local authentication with admin and super-admin privilege levels
    
-   Stork agent deployed on the Kea or BIND server reports CPU and OS data, including speed, memory, memory utilization, operating system and version.
    
-   Auto-discovery of Kea and BIND services on monitored machines with application software version and uptime since last reload
    
-   [Prometheus](https://prometheus.io/) and [Grafana](https://grafana.com/) integration
    
-   DHCP pool utilization
    
-   DHCP Shared network utilization
    
-   DHCP High Availability status monitoring
    
-   DHCP Host reservations
    
-   BIND9 Cache Hit Ratio monitoring
    

  
  

![](https://lh5.googleusercontent.com/G5hEQKkgAVFJV9z24qQbclNIsbhMayurOf059_2x1yet6UnykD3g7B86aPqekGq2PtQu3VnF6cW5dZ54Zo1sbpNp4jsupcpnTift2R-WDIdvQTGXIZJ8LJw0_QEfYVjfKVXUzGu-nXMkj0Dd "horizontal line")

  
  

## Architecture

The Stork server is installed on a stand-alone machine. It connects to any indicated agents and indirectly (via those agents) interacts with the Kea DHCP services. It provides an integrated, centralized front end for these services. Only one Stork server is deployed in a network.

  

The Stork agent is installed along with Kea DHCP and interacts directly with those services. There may be many agents deployed in a network, one per machine.

  
  

![](https://lh6.googleusercontent.com/x1SEt64B2_4w1KD6Hm5GCG_2bOJ_gIX-y_dYBzRPgPU04UMmX2J3tmxlAF6WU533h1xkMzluccMj9xh0I_kycem_EwSfYDUXS16hPmvafjFLBqsKATFpFhl6OIDHyQ7fSHrYtPuBujxwnm-X)

  
  
  
  

![](https://lh5.googleusercontent.com/G5hEQKkgAVFJV9z24qQbclNIsbhMayurOf059_2x1yet6UnykD3g7B86aPqekGq2PtQu3VnF6cW5dZ54Zo1sbpNp4jsupcpnTift2R-WDIdvQTGXIZJ8LJw0_QEfYVjfKVXUzGu-nXMkj0Dd "horizontal line")

  

## Current Usage

![Amazon Web Services](https://lh4.googleusercontent.com/V1YXIdLgJjK6j2uIZInPgSSBV3OwgjbJjYd-n1FC2c16u4u0-41u-EMBU5bH_keb-TBTq0Ch3cZ4AZy_ce4LPHE4eld3hEUlNDyLUqGlUzoHJTMt3R8gQXZlGu893PL7SKkJTxKmK5fde3bF)

[AWS](https://aws.amazon.com/)

[Cloud Computing](https://aws.amazon.com/)

![CloudFlare](https://lh4.googleusercontent.com/vUOhHqNfL9up2ZSP5WXzFoa9rEH9KqNbd2GF7uXMjuIl9k6hy3DqCVcDzjKnJLShOBQTFCKLUbWv3RkJKUBIU7cQu9ezX5IjYxTCOlNsbH3PMhYlUXTiaoGEc4ykWiSMQjTTgxTFBIvdm8DH)

[Cloudflare](https://cloudflare.com/)

[DNS & SSL](https://cloudflare.com/)

![Sentry](https://lh4.googleusercontent.com/htCk50LopQtfkBERcKpzHAMediRJjyw6ck1T4oc-YHK3NqoSASif_uvSS2jDDWib24WfxgCYtJoQl9pXD3cN5QudElDzMklj7dNWs2WvN-CPOxLvW_xL2JKHaRKXnsxTYcVHqvMHySrmO3ZS)

[Sentry](https://sentry.io/)

[Monitoring](https://sentry.io/)

![Elastic](https://lh6.googleusercontent.com/iCoyDsyr5_8F8Ikp3GPFuKX3MYlxOAPoa5y6y0duXXNUS2_Cl0YD4G7UIARGtIal8pj_v4M-K1T47jjS30Hq9W7WvXStspCSn3xQFkjMmyVmk7USw9YBCLJfQHB_RIbAiVY8aLsRZL-YTm3o)

[Elastic](https://www.elastic.co/)

[Search](https://www.elastic.co/)

![New Relic](https://lh5.googleusercontent.com/deiL9MHV5m3qk11_JxJs0_y4yAe6qawcU3h08s-bi8GSe8YZwF8IpN7YzmrREYboXuQpI1pksHlb9m6gJmwLSyNNoLL7P5zxfVaRUi8DfNFYv2yRJiqrz3lZS_nbPzKeACKCg-m1Fo_MPFHe)

[New Relic](https://newrelic.com/)

[Performance](https://newrelic.com/)

![PagerDuty](https://lh4.googleusercontent.com/8OLXRgxOtH7oeSb3eZhrjl27OkhJhChwwjHY8Sx1jLMCdPzxyvCJKLwbzZZ3178UWvZjxjFbYgIaTbxMeUSsQaZVNQc4ykOSMRr9EbgRQhpmyKj5aEEKiEAdOIxQICqILFyYbXIDYsgpSsdz)

[PagerDuty](https://www.pagerduty.com/)

[Monitoring](https://www.pagerduty.com/)

![](https://lh5.googleusercontent.com/gBemvekH6cCkqqp7h8fgRNDnPniLlL8_7W5EvkiiDDHWNqT7qGJTxXfN_T5IKSbmu5dXxFQ4KXkxL-_uDNUo8rr3WhAkv-akjoXti8EtgRNATN7AlKvDrT8Nd0NuJ6zzBiXhIPd3e2uQKbRM)

![](https://lh6.googleusercontent.com/ebwF-6bK1zzhM0E9JUsfMmfxI_f926SQhorFa_wSgPLraTIvf7bF2PreQBlr0W_dI35fvuYltYCrwGXa1ygr6-Dl9Q5Mm8wefssVHjQcuvH6_mzqRF9q8kXO-_tZDI0x_lMhnKTAMlYqlsbj)

![](https://lh3.googleusercontent.com/0e9XaqHVmw654-5zWDQw3yZJcZZKFeV4KGgcT1S-KmWUJ7kNCT2yU1o-H6dYmhy5dumT6poq-wsQugSmWwuvTvk6MxIb_q0cdRYjI3vcB-pIdYBgSRn4IBpjLJsKlWQMtFirO1dXTK769hMc)
**Other Information**

There are public mailing lists available for the Stork project. stork-users (stork-users at lists.isc.org) is intended for Stork users. stork-dev (stork-dev at lists.isc.org) is intended for Stork developers, prospective contributors, and other advanced users. The lists are available at [https://www.isc.org/mailinglists/](https://www.isc.org/mailinglists/). The community provides best-effort support on both of those lists.
