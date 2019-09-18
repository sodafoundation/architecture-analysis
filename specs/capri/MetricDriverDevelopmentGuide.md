
# **Develop new metric driver for OpenSDS/Telemetry**

Creating a new metric driver is similar to creating a new storage driver described [here](https://github.com/opensds/opensds/wiki/Develop-new-storage-driver-for-OpenSDS) .
### **Scope of Metric Driver**
Metric driver is supposed to collect data for a storage/platform type. Metric driver should return an array of Metricspec structure . Each element in the array represents one metric .

Opensds controller receives  the returned metric array and send to different targets as configured in the controller.


### **What is Metricspec**

MericSpec structure is declared in /pkg/model/metric.go, which contains name,timestamp,value and other attributes of a metric. Below is the MetricSpec structure.

```go
type MetricSpec struct {
	/* Following are the fields used to form name and labels associated with a Metric, same as Prometheus guage name and labels
	Example: node_disk_read_bytes_total{device="dm-0",instance="121.244.95.60:12419",job="prometheus"}
	guage name can be formed by appending Job_Component_Name_Unit_AggrType */

	// Instance ID -\> volumeID/NodeID
	InstanceID string `json:"instanceID,omitempty"`

	// instance name -\> volume name / node name etc.
	InstanceName string `json:"instanceName,omitempty"`

	// job -\> Prometheus/openSDS
	Job string `json:"job,omitempty"`

	/*Labels - There can be multiple componets/properties  associated with a metric , these are catured using this map
	  Example: Labels[pool]="pool1";Labels[device]="dm-0" */
	Labels map[string]string `json:"labels,omitempty"`

	// component -\> disk/logicalVolume/VG etc
	Component string `json:"component,omitempty"`

	// name -\> metric name -\> readRequests/WriteRequests/Latency etc
	Name string `json:"name,omitempty"`

	// unit -\> seconds/bytes/MBs etc
	Unit string `json:"unit,omitempty"`

	// Can be used to determine Total/Avg etc
	AggrType string `json:"aggrType,omitempty"`

	/*If isAggregated ='True' then type of aggregation can be set in this field
	  ie:- if collector is aggregating some metrics and producing a new metric of
	  higher level constructs, then this field can be set as 'Total' to indicate it is
	  aggregated/derived from other metrics.*/

	MetricValues []*Metric `json:"metricValues,omitempty"`
}

type Metric struct {
	Timestamp int64   `json:"timestamp,omitempty"`
	Value     float64 `json:"value"`
}

```


### **Who invokes Metric driver collect method?**

1.opensds dock process will invoke ColelctMetrics(), which is result of  POST API invocation.

2.A scrape request to Metric exporter by prometheus/third party .

### **What Methods metric driver should implement ?**

Metric driver should implement all methods defined in MetricDriver interface , which is  in drivers/drivers.go

```go
type MetricDriver interface {
	//Any initialization the metric driver does while starting.
	Setup() error
	//Any operation the metric driver does while stopping.
	Teardown() error
	// Collect metrics for all supported resources
	CollectMetrics() ([]*model.MetricSpec, error)
}

```

### **Code development**

All drivers should be implement in contrib/drivers/<driver type> .

*   create a <yourdriver>metrics.go file.

    example:
    ```
        touch contrib/drivers/lvm/lvmmetrics.go
    ```
#### **Develop metrics collection code as  below**

```go
func (d *MetricDriver) CollectMetrics() ([]*model.MetricSpec, error) {

   // get Metrics to unit map
   var metricToUnitMap map[string]string = map[string]string{"iops": "tps", "throughput": "kbs"}

   //validate metric support list
   supportedMetrics := [] string {"throughput","iops"}
   var volumeList []string = []string{"vol1", "vol2"}

   var labelMap map[string]map[string]string = map[string]map[string]string{"vol1": map[string]string{"device": "vol1_name"}, "vol2": map[string]string{"device": "vol2_name"}}
   var metricMap map[string]map[string]string = map[string]map[string]string{"vol1": map[string]string{"InstanceName": "vol1", "iops": "8.77", "read_throughput": "12.22", "response_time": "0.01", "service_time": "0.01", "write_throughput": "32.56", "utilization": "12.21"},
      "vol2": map[string]string{"InstanceName": "vol2", "iops": "6.26", "throughput": "8.27"},
   }
      
   var tempMetricArray []*model.MetricSpec
   // fill volume metrics
   for _, volume := range volumeList {
      convrtedVolID := convert(volume)
      aMetricMap := metricMap[convrtedVolID]
      aLabelMap := labelMap[convrtedVolID]
      for _, element := range supportedMetrics {
         val, _ := strconv.ParseFloat(aMetricMap[element], 64)
         metricValue := &model.Metric{
            Timestamp: getCurrentUnixTimestamp(),
            Value:     val,
         }
         metricValues := make([]*model.Metric, 0)
         metricValues = append(metricValues, metricValue)
         metric := &model.MetricSpec{
            InstanceID:   volume,
            InstanceName: aMetricMap["InstanceName"],
            Job:          "lvm",
            Labels:       aLabelMap,
            Component:    "volume",
            Name:         element,
            Unit:         metricToUnitMap[element],
            AggrType:     "",
            MetricValues: metricValues,
         }
         tempMetricArray = append(tempMetricArray, metric)
      }
   }
   
   metricArray := tempMetricArray
   return metricArray, nil
}
func (d *MetricDriver) Setup() error {

	return nil
}
func (*MetricDriver) Teardown() error { return nil }

```
### Make changes in InitMetricDriver function in drivers.go  to pick your driver 
```go
func InitMetricDriver(resourceType string) MetricDriver {
	var d MetricDriver
	switch resourceType {
	case config.LVMDriverType:
		d = &lvm.MetricDriver{}
		break
	case config.CephDriverType:
		d = &ceph.MetricDriver{}
		break
	case config.HuaweiDoradoDriverType:
		d = &dorado.MetricDriver{}
		break
	default:
		//d = &sample.Driver{}
		break
	}
	d.Setup()
	return d
}
```

###    **How to test driver code**

*   Method1:
     Write a test code which invokes CollectMetrics()
 Lvmmetrics_test.go
```go
       package lvm
       import (
       	"fmt"
       	"testing"
       )
       func TestMetricDriverSetup(t *testing.T) {
       	var d = &MetricDriver{}
       	if err := d.Setup(); err != nil {
       		t.Errorf("Setup lvm metric  driver failed: %+v\n", err)
       	}
       }
       func TestCollectMetrics(t *testing.T) {
       	metricArray, err := metricDriver.CollectMetrics()
       	fmt.Println(metricArray)
       }
```

*   Method2:
     Use  POST API to trigger collectMetrics()
    
    example: 
    ```
    "curl -d '{""driverType"":""lvm""}' -H ""Content-Type: application/json"" -X POST http://127.0.0.1:50040/v1beta/e93b4c0934da416eb9c8d120c5d04d96/metrics 
    ```
    


