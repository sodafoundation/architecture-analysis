# DNS Resolution Proposal in Hotpot

**Author(s)**: [Leon Wang](https://github.com/leonwanghui)

## Summary

This design is to enable DNS resolution for all Hotpot related services, which
not only simplifies network configuration for OpenSDS setup but also solves
the access problem in some conditions.

## Motivation

As we know, DNS resolution is widely used to service-to-service communication.
For example, when users want to access Google Search, they would type some
DNS addresses such like `www.google.com` rather than some static IP address.

Currently there are several services deployed in OpenSDS cluster: api-server,
controller, dock, dashboard, authchecker and database. And the current design is
that system admins have to remember all their IP addresses and configure them
in [opensds.conf](https://github.com/opensds/opensds/blob/master/examples/opensds.conf#L16).
This could lead to several problems:
* Quite difficult to scale-out. Multi-dock deployment could drive users crazy
because they have to maintain the network topology manually.
* With more and more service designed (metrics controller, file share controller,
scheduler, policy controller), Hotpot becomes more like a microservie system,
and network management is always a disaster for microservice.
* In some conditions, service A can't directly access service B because the bind
IP listened by B is not same with the external IP exposed to A.

To solve the problem above, we can enable DNS resolution for Hotpot services and
leverage some tools (Kubernetes, consul, istio, etc) to manage all network
configuration.

### Goals

* Redesign the term of `ApiEndpoint` to decouple bind IP address with external
IP address.
* Provide an elegant deployment with Kubernetes based on DNS resolution feature.
* Provide some deployment manifests of Istio to demonstrate how OpenSDS leverage
Istio to perform traffic management, fault injection, circuit breaking, etc.

### Non-Goals

* How to integrate with other tools (such like consul) is not included in this
design.
* In non-K8S conditions, user still have to perform traffic management manually.

## Design Details

This design mainly includes three aspects of update work.

### Hotpot related
In Hotpot source code, we need to redesign the term of `ApiEndpoint` defined in
OsdsApiServer, OsdsLet and OsdsDock structures to decouple bind IP address with
external IP address:
* ApiEndpoint in OsdsApiServer: indicates the bind endpoint which the API server
would listen to. User can configure it when starting `osdsapiserver` service, and
the format of this field should be exactly like `127.0.0.1:50040`.
* ApiEndpoint in OsdsLet and OsdsDock: indicates the external IP and endpoint
which the controller and dock gRPC server would expose to other services. Normally
the format should be like `127.0.0.1:50049`, but in K8S it should be the format
like `FQDN` (such as `controller.default.svc.cluster.local`).

And another change is for osdslet and osdsdock service, the bind endpoint should
be set statically so that these two services would run without affected by outside
of world. So we need to create two constants below:
```go
// OpensdsCtrBindEndpoint indicates the bind endpoint which the opensds
// controller grpc server would listen to.
OpensdsCtrBindEndpoint = "0.0.0.0:50049"
// OpensdsDockBindEndpoint indicates the bind endpoint which the opensds
// dock grpc server would listen to.
OpensdsDockBindEndpoint = "0.0.0.0:50050"
```

### K8S related
In this design, some manifests (see [here](https://github.com/opensds/opensds/tree/development/install/kubernetes))
are added under `install/kubernetes` folder, and `README.md` provides a tutorial
on how to install OpenSDS on an existing Kubernetes cluster.

### Istio related
In this design, some manifests (see [here](https://github.com/opensds/opensds/tree/development/install/kubernetes/istio-networking)) are added under `install/kubernetes/istio-networking`
folder.

### Data model impact

None

### REST API impact

None

### Security impact

None

### Other end user impact

None

### Performance impact

None

### Other deployer impact

None

### Developer impact

Owners of these designs ([#28](https://github.com/opensds/design-specs/pull/28)
and [#27](https://github.com/opensds/design-specs/pull/27)) need to learn how
this design works, and then update their current implementation. 

## Use Cases

### OpenSDS deployment on Kubernetes
In Kubernetes, all applications would be deployed and managed in `Pod`, and user
can access to that application through `Service`. So if OpenSDS wants to take
the advantages of K8S orchestration, it has to distinguish the concept of `PodIP`
from `ClusterIP`. For detailed introduction of K8S Service, please refer to
[here](https://kubernetes.io/docs/concepts/services-networking/service/).

In this condition, users need to configure `opensds.conf` like below:
```conf
[osdsapiserver]
api_endpoint = 0.0.0.0:50040
auth_strategy = keystone
# If https is enabled, the default value of cert file
# is /opt/opensds-security/opensds/opensds-cert.pem,
# and key file is /opt/opensds-security/opensds/opensds-key.pem
https_enabled = False
beego_https_cert_file =
beego_https_key_file =
# Encryption and decryption tool. Default value is aes.
password_decrypt_tool = aes

[keystone_authtoken]
memcached_servers = authchecker.opensds.svc.cluster.local:11211
signing_dir = /var/cache/opensds
cafile = /opt/stack/data/ca-bundle.pem
auth_uri = http://authchecker.opensds.svc.cluster.local/identity
project_domain_name = Default
project_name = service
user_domain_name = Default
password = opensds@123
# Whether to encrypt the password. If enabled, the value of the password must be ciphertext.
enable_encrypted = False
# Encryption and decryption tool. Default value is aes. The decryption tool can only decrypt the corresponding ciphertext.
pwd_encrypter = aes
username = opensds
auth_url = http://authchecker.opensds.svc.cluster.local/identity
auth_type = password

[osdslet]
api_endpoint = controller.opensds.svc.cluster.local:50049

[osdsdock]
api_endpoint = dock.opensds.svc.cluster.local:50050
# Choose the type of dock resource, only support 'provisioner' and 'attacher'.
dock_type = provisioner
# Specify which backends should be enabled, sample,ceph,cinder,lvm and so on.
enabled_backends = lvm

[lvm]
name = lvm
description = LVM Test
driver_name = lvm
config_path = /etc/opensds/driver/lvm.yaml
host_based_replication_driver = DRBD

[database]
endpoint = db.opensds.svc.cluster.local:2379,db.opensds.svc.cluster.local:2380
driver = etcd
```

### Istio traffic management
Usually for large scale system, traffic management (traffic shifting, A/B test,
canary ugrade, etc) is very important. And Istio system is built to handle these
situations without any impact to business code.

## Implementation

* Add `OpensdsCtrBindEndpoint` and `OpensdsDockEndpoint` field in constants package.
* Set the bind endpoint of osdslet and osdsdock service to static constants above.
* Add some K8S deployment and service manifests.
* Add some Istio gateway, virtual service and destination rule manifests.

## Alternatives considered

None

## Open issues

These terms described [above](#design-details) could be ambiguous for developers,
we could replace them with some better names.
