# NVMeoF support

**Authors:** [Intel](https://github.com/qwren)

This is the design spec for the NVMeoF feature.

## Motivation

The NVMe over Fabrics (NVMeOF) is an emerging technology, and it gives data centers unprecedented access to NVMe SSD storage. Like iSCSI, NVMeОF is a network protocol used to communicate between a host and a storage system over a network (aka fabric). Compared with iSCSI, NVMeOF has much lower latency, in practice adding just a few microseconds to cross the network. This makes the difference between local storage and remote storage very small.

NVMeoF depends on and requires the use of RDMA, and it can use any of the RDMA technologies, including InfiniBand, RoCE and iWARP. Current implementation of NVMeoF in OpenSDS will only test the scenario that Intel iWARP RDMA technology. In future, we can do more testing in the case of other RDMA technology, including InfiniBand and RoCE.

### Goal

* Add NVMeoF connector
* Add NVMeoF support for Native OpenSDS Southbound LVM driver
* Installation using Ansible needs to be modified so that new protocol is configured

## Details 

### NVMeoF connector

* constant definition, like iSCSI, in contrib/connector/connector.go
```go
const (
        FcDriver = "fibre_channel"
        PortName = "port_name"
        NodeName = "node_name"
        Wwpn     = "wwpn"
        Wwnn     = "wwnn"

        IscsiDriver = "iscsi"
        Iqn         = "iqn"

        RbdDriver = "rbd"
+
+       NvmeofDriver = "nvmeof"
+       Nqn          = " nqn"
)
```

* NVMeoF connector implementation in contrib/connector/nvmeof/nvmeof.go and contrib/connector/nvmeof/nvmeof_helper.go
```go
+type ConnectorInfo struct {
+	TgtNqn		string `mapstructure:"targetNqn"`
+	TgtPortal 	string `mapstructure:"targetPortal"`
+	TranType  	string `mapstructure:"transporType"`
+	TgtDisco   	bool   `mapstructure:"targetDiscovered"`
+	VolumeID   	string `mapstructure:"volumeId"`
+}
```

Attach() and Detach() are the two methods that an OpenSDS driver with a different protocol needs to implement.
```go
+func (nof *Nvmeof) Attach(conn map[string]interface{}) (string, error) {
+        return Connect(conn)
+}
+
+func (nof *Nvmeof) Detach(conn map[string]interface{}) error {
+        NvmeofCon := ParseNvmeofConnectInfo(conn)
+
+        return DisConnect(NvmeofCon.Nqn)
+}
```

### NVMeoF support for Native OpenSDS Southbound LVM driver

* constant definition  
```go
const (
-	iscsiTgtPrefix = "iqn.2017-10.io.opensds:"
+	iscsiTgtPrefix  = "iqn.2017-10.io.opensds:"
+	nvmeofTgtPrefix = "nqn.2019-1.opensds"
+	iscsiAccess     = "iscsi"
+	nvmeofAccess    = "nvmeof"
)
```

* func NewTarget
```go
-// NewTarget method creates a new iscsi target.
-func NewTarget(bip string, tgtConfDir string) Target {
-	return &iscsiTarget{
-		ISCSITarget: NewISCSITarget(bip, tgtConfDir),
+// NewTarget method creates a new target based on its type.
+func NewTarget(bip string, tgtConfDir string, access string) Target {
+	if access == iscsiAccess {
+		return &iscsiTarget{
+			ISCSITarget: NewISCSITarget(bip, tgtConfDir),
+		}
+	} else if access == nvmeofAccess {
+		return &nvmeofTarget{
+			NvmeofTarget: NewNvmeofTarget(bip, tgtConfDir),
+		}
+	} else {
+		return nil
	}
}
```

* add nvmeofTarget struct for NVMeoF 
```go
+type nvmeofTarget struct {
+	NvmeofTarget
+}
+
+func (t *nvmeofTarget) CreateExport(volId, path, hostIp, initiator string, chapAuth []string) (map[string]interface{}, error)
+func (t *nvmeofTarget) RemoveExport(volId string) error
```

* implement NVMeoF target in contrib/drivers/lvm/targets/nvmeof.go
```go
type NvmeofTarget interface {
        CreateNvmeofTarget(volId, tgtNqn, path, hostIp, initiator string, chapAuth []string) error
        GetNvmeofTarget(nqn string) int
        RemoveNvmeofTarget(volId, nqn string) error
}
```

### Ansible impact

RDMA Ethernet card is necessary for real NVMeoF Initiator/Target environment. Hardware environment has to be configured in Ansible, and it is necessary to load the following modules to make use of RDMA feature in userspace:

* nvmet
* nvmet-rdma
* nvme-rdma
