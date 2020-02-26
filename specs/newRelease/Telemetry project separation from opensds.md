# Telemetry project separation from opensds

**Author(s)**: [Najmudheen](https://github.com/NajmudheenCT)

## Summary

Separate telemetry modules from opensds repository and move to a new repository.
 

## Motivation

Telemetry should be able to run as an independent solution with all telemetry related functionalities. User need not install  opensds to avail telemetry services.

### Goals

* telemetry as a separate project
* new end point to connect telemetry services.
* no dependency with opensds services
* separate installer for telemetry
* separate CI for telemetry

### Non-Goals

* usage of new tools
* replacement of existing tools
* feature addition
* decoupling of modules

## Design Details
telemetry project will have 4 modules
* API server -> separated from opensds API server with only telemetry APIs
* Controller -> separated from opensds controller with only telemetry controller
* Dock       -> separated from opensds dock with only telemtry driver
* lvm_exporter -> moved as it is to telemetry repo


### Data model impact

NA

### REST API impact

NA

### Security impact

NA

### Other end user impact

API end point change would impact the consumers of telemetry like dashboard and anomaly detection. 

### Performance impact

NA

### Other deployer impact

NA

### Developer impact

NA

## Use Cases

Telemetry should be able to run as a independent solution with all telemetry related functionalities. 

## Implementation

* Separate only telemetry related code and dependent utilities
* build and test all modules separately
* CI for the project
* installer for the project

## Alternatives considered

NA

## Open issues

* dependency with keystone, etcd (which are shared by opensds)
* dock initialization
