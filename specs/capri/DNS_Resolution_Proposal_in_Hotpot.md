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

* Add the new term of `DnsEndpoint` to decouple bind IP address with external
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
In Hotpot source code, we need to add the new term of `DnsEndpoint` to decouple
bind IP address with external IP address.

Here are some terminology to help developers understand this design:
* ApiEndpoint: indicates the bind endpoint which the server would listen to. The
format of this field should be exactly like `127.0.0.1:8088`. All Hotpot internal
services should include it and also it should be configured properly.
* DnsEndpoint: indicates the external IP and endpoint which the server would
expose to other services. Normally the `DnsEdnpoint` should be the same as
`ApiEndpoint`, but in K8S it should be the format like `FQDN` (such as
`apiserver.default.svc.cluster.local`).

### K8S related
In this design, some manifests (see [here](https://github.com/opensds/opensds/tree/18c9088053bd99aa363182cbc582e2e232361a74/install/kubernetes)) are added under `install/kubernetes` folder, and `README.md`
provides a tutorial on how to install OpenSDS on an existing Kubernetes cluster.

### Istio related
In this design, some manifests (see [here](https://github.com/opensds/opensds/tree/18c9088053bd99aa363182cbc582e2e232361a74/install/kubernetes/istio-networking)) are added under `install/kubernetes/istio-networking`
folder.

### Data model impact
This design would have some impact on Hotpt global configure definition:
```go
type OsdsApiServer struct {
	ApiEndpoint        string        `conf:"api_endpoint,localhost:50040"`
+	DnsEndpoint        string        `conf:"dns_endpoint,localhost:50040"`
	AuthStrategy       string        `conf:"auth_strategy,noauth"`
	Daemon             bool          `conf:"daemon,false"`
	PolicyPath         string        `conf:"policy_path,/etc/opensds/policy.json"`
	LogFlushFrequency  time.Duration `conf:"log_flush_frequency,5s"` // Default value is 5s
	HTTPSEnabled       bool          `conf:"https_enabled,false"`
	BeegoHTTPSCertFile string        `conf:"beego_https_cert_file,/opt/opensds-security/opensds/opensds-cert.pem"`
	BeegoHTTPSKeyFile  string        `conf:"beego_https_key_file,/opt/opensds-security/opensds/opensds-key.pem"`
}

type OsdsLet struct {
	ApiEndpoint       string        `conf:"api_endpoint,localhost:50049"`
+	DnsEndpoint       string        `conf:"dns_endpoint,localhost:50049"`
	Daemon            bool          `conf:"daemon,false"`
	LogFlushFrequency time.Duration `conf:"log_flush_frequency,5s"` // Default value is 5s
}

type OsdsDock struct {
	ApiEndpoint                string        `conf:"api_endpoint,localhost:50050"`
+	DnsEndpoint                string        `conf:"dns_endpoint,localhost:50050"`
	DockType                   string        `conf:"dock_type,provisioner"`
	EnabledBackends            []string      `conf:"enabled_backends,lvm"`
	Daemon                     bool          `conf:"daemon,false"`
	BindIp                     string        `conf:"bind_ip"` // Just used for attacher dock
	HostBasedReplicationDriver string        `conf:"host_based_replication_driver,drbd"`
	LogFlushFrequency          time.Duration `conf:"log_flush_frequency,5s"` // Default value is 5s
	Backends
}

type Database struct {
-	Credential string `conf:"credential,username:password@tcp(ip:port)/dbname"`
-	Driver     string `conf:"driver,etcd"`
-	Endpoint   string `conf:"endpoint,localhost:2379,localhost:2380"`
+	Credential  string `conf:"credential,username:password@tcp(ip:port)/dbname"`
+	Driver      string `conf:"driver,etcd"`
+	DnsEndpoint string `conf:"dns_endpoint,localhost:2379,localhost:2380"`
}
```

### REST API impact

None

### Security impact

None

### Other end user impact

None

### Performance impact

None

### Other deployer impact

To enable DNS resolution, users need to change the `opensds.conf` like below:
```conf
[osdsapiserver]
api_endpoint = 0.0.0.0:50040
+dns_endpoint = localhost:50040
auth_strategy = keystone
# If https is enabled, the default value of cert file
# is /opt/opensds-security/opensds/opensds-cert.pem,
# and key file is /opt/opensds-security/opensds/opensds-key.pem
https_enabled = False
beego_https_cert_file =
beego_https_key_file =
# Encryption and decryption tool. Default value is aes.
password_decrypt_tool = aes

[osdslet]
api_endpoint = 0.0.0.0:50049
+dns_endpoint = localhost:50049

[osdsdock]
api_endpoint = 0.0.0.0:50050
+dns_endpoint = localhost:50050
# Choose the type of dock resource, only support 'provisioner' and 'attacher'.
dock_type = provisioner
# Specify which backends should be enabled, sample,ceph,cinder,lvm and so on.
enabled_backends = sample

[database]
-credential = opensds:password@127.0.0.1:3306/dbname
-endpoint = localhost:2379,localhost:2380
+dns_endpoint = localhost:2379,localhost:2380
driver = etcd
```

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
dns_endpoint = apiserver.opensds.svc.cluster.local:50040
auth_strategy = noauth
# If https is enabled, the default value of cert file
# is /opt/opensds-security/opensds/opensds-cert.pem,
# and key file is /opt/opensds-security/opensds/opensds-key.pem
https_enabled = False
beego_https_cert_file =
beego_https_key_file =
# Encryption and decryption tool. Default value is aes.
password_decrypt_tool = aes

[osdslet]
api_endpoint = 0.0.0.0:50049
dns_endpoint = controller.opensds.svc.cluster.local:50049

[osdsdock]
api_endpoint = 0.0.0.0:50050
dns_endpoint = dock.opensds.svc.cluster.local:50050
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
dns_endpoint = db.opensds.svc.cluster.local:2379,db.opensds.svc.cluster.local:2380
driver = etcd
```

### Istio traffic management
Usually for large scale system, traffic management (traffic shifting, A/B test,
canary ugrade, etc) is very important. And Istio system is built to handle these
situations without any impact to business code.

## Implementation

* Add `DnsEndpoint` field in global `Config` structure.
* Add some K8S deployment and service manifests.
* Add some Istio gateway, virtual service and destination rule manifests.

## Alternatives considered

None

## Open issues

These terms described [above](#design-details) could be ambiguous for developers,
we could replace them with some better names.
