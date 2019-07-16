# CSI-Test design

**Author(s)**: [jackhaibo](https://github.com/jackhaibo)

## Summary

This document is a design proposal to support csi-test which provides a simple way to ensure sushi CSI plugin conforms to the CSI specification.

## Motivation

Currently there is no standard way to test whether sushi csi plugin conforms to the CSI specification. Simultaneously, the CSI sanity package from csi-test can be used for unit testing sushi CSI plugin. It contains a set of basic tests that all CSI drivers should pass (for example, NodePublishVolume should fail when no volume id is provided, etc.).

### Goals

* Make OpenSDS support for running csi sanity tests, from csi-test/sanity.
* Pass all csi sanity tests cases.

### Non-Goals

## Design Details

The csi sanity test has 57 test cases built in, server as a client, connect with sushi CSI pluign server by grpc. Then csi sanity test triggers test case execution.

### Other deployer impact

Due to mount will be interfaced, sushi volume and fileshare mount and unmount feature will be affected. The following volume driver will be affected:

* nvmeof
* rbd

### Developer impact

Developers need to get the mounter object through the interface and then call mount related functions.

## Implementation

In order to be able to support csi sanity test:

* Interface the mount to mounter to fake the mount related functions.
* Fake connector and mounter
* In order to fake OpenSDS client, a faked OpenSDS client will be implemented, use list data structure as database.
* Implement sanity test file to connect with CSI pluign server by grpc and execute sanity test cases.

## References

[1] Container Storage Interface Spec: https://github.com/kubernetes-csi/csi-test/tree/master/pkg/sanity