# Thin OpenSDS Design

**Authors:** [Xing Yang](https://github.com/xing-yang)

This document serves as a design proposal for Thin OpenSDS. This proposal was drafted [here](https://docs.google.com/document/d/16zLCE1X8bNjkGoKvBpqDZaxEHZapWluQVgdvlxuR_tE/edit?usp=sharing).

## Background

In containerized environment, users may not need the full-blown orchestration capabilities of OpenSDS.  Instead, they just want a CSI plugin that can provision storage for Container Orchestration Systems (COs).  That’s why we are proposing to provide a light-weight version of OpenSDS that can serve this need.

## Objectives

### Goals

* Provide a light-weight version of OpenSDS that includes a CSI plugin to provision storage for COs.
* Only one storage backend is supported at a given time based on configure setting.
* Only functionalities supported by CSI spec [1] are supported.  These include create/delete volume, attach/detach volume, mount/unmount volume, create/delete snapshot, resize volume, etc.
* More functionalities will be added to Thin OpenSDS as they become available in the CSI spec.

### Non Goals

* While Thin OpenSDS can support different storage backends, multiple backends cannot be used simultaneously because the scheduler and volume controller are not included.
* Replication is not supported because CSI does not support replication.
* Make database configurable and optional. This may be desirable for some use cases, however, it is not easy to achieve as database is needed to persist the states of resources. 

## Design Details

The Thin OpenSDS design is illustrated in the following diagram.

![Thin_OpenSDS Architecture Diagram](resources/thin_opensds.png?raw=true "Thin OpenSDS Architecture Diagram")

This design is dependent upon [API Framework Refactoring](https://github.com/opensds/design-specs/blob/master/specs/capri/API_Framework_Refactoring.md) which has already been implemented in Capri.

It includes the OpenSDS CSI plugin, API server, the dock, and volume drivers.  Communication between API server and dock will be through gRPC.  Because the scheduler and volume controller are not included, only one storage backend can be supported at any given time depending on configure settings.  Only CSI supported functions are available.  For example, replication won’t be available because it is not supported by CSI and there is no DR controller in Thin OpenSDS to provide that feature.

* Database is needed for the first phase and is used to persist states for resources.
* A config file for the backend will be used for CSI plugin.
* When OpenSDS is started, Dock info is written in the database. API will read this entry in the database.

## References

[1] Container Storage Interface Spec: https://github.com/container-storage-interface/spec
