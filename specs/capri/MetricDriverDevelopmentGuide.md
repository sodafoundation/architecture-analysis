
# **Develop new metric driver for OpenSDS/Telemetry**

Creating a new metric driver is similar to creating a new storage driver described [here](https://github.com/opensds/opensds/wiki/Develop-new-storage-driver-for-OpenSDS) .


### **Scope of Metric Driver**

Metric Driver is supposed to collect data for a list of metrics  for an instance which is passed to the driver in each call. Metric driver should return an array of Metricspec structure . Each element in the array represent one metric from the metricList supplied ,

Controller takes the returned metric array and send to different targets using adapters.


### **What is Metricspec**

MericSpec structure is declared in /pkg/model/metric.go, which contains name,timestamp,value and other attributes of a metric. Below is the MetricSpec structure.

----------------------------

**type **MetricSpec **struct **{



InstanceID string **`json:"InstanceID,omitempty"`**

InstanceName string **`json:"InstanceName,omitempty"`**

Job string **`json:"Job,omitempty"`**

/* associator - Some metric would need specific fields to relate components.

  Use case could be to query volumes of a particular pool. Attaching the related_
  components as labels would help us to form promQl query efficiently.
  Example: node_disk_read_bytes_total{instance="121.244.95.60"}_
  Above query will respond with all disks associated with node 121.244.95.60_
  Since associated components vary, we will keep a map in metric struct to denote
  the associated component type as key and component name as value
  Example: associator[pool]=pool1 */

 Associator **map**[string]string **`json:"Associator,omitempty"`**

// Following fields can be used to form a unique metric name_**
// source -\> Node/Dock_
 Source string **`json:"Source,omitempty"`**
// component -\> disk/logicalVolume/VG etc_**
Component string **`json:"Component,omitempty"`**
// name -\> metric name -\> readRequests/WriteRequests/Latency etc_**
Name string **`json:"Name,omitempty"`**
// unit -\> seconds/bytes/MBs etc_**
Unit string **`json:"Unit,omitempty"`**
// is aggregated_**
IsAggregated bool **`json:"IsAggregated,omitempty"`**

MetricValues []Metric

}

type Metric struct {

  Timestamp int64

  Value float64

}

----------------------------------------------------------------


### **Who invokes Metric driver Collect?**

Dock will invoke ColelctMetrics(), which is either during a metric POST API request or a scrape request to Metric exporter by Prometheus .


### **What Methods metric driver should implement ?**

Metric driver should implement all methods defined in MetricDriver interface , which is  in drivers/drivers.go

-------------------

type MetricDriver interface {

  _//Any initialization the volume driver does while starting._

Setup() error

  _//Any operation the volume driver does while stopping._

Unset() error

  CollectMetrics(metricList []string,instanceID string) ([]model.MetricSpec, error)

  ValidateMetricsSupportList(metricList []string,resourceType string) ([]string,error)

}

------------------------


### **Code development**

All drivers should be implement in contrib/drivers/<driver type> .



*   create a <yourdriver>metrics.go file.

    example:


    touch contrib/drivers/lvm/lvmmetrics.go

*   **Develop metrics collection code like below**

    ----------------------------------------------

package lvm

import (
	log "github.com/golang/glog"

	"github.com/opensds/opensds/pkg/model"

	"gopkg.in/yaml.v2"

	"strconv"

	"time"
)

/*const (


  // yaml file which contins the supported metric list


  ConfFile  = "/etc/opensds/driver/metric-config.yaml"


  )*/

// Todo: Move this Yaml config to a file

var data = `


resources:


 - resource: volume


   metrics:


     - IOPS


     - ReadThroughput


     - WriteThroughput


     - ResponseTime


     - ServiceTime


     - UtilizationPercentage


   units:


     - tps


     - KB/s


     - KB/s


     - ms


     - ms


     - '%'


 - resource: pool


   metrics:


     - ReadRequests


     - WriteRequests


     - ReponseTime


   units:


     - tps


     - KB/s


     - KB/s


     - ms


     - ms


     - '%'


`

type Config struct {
	Resource string

	Metrics []string

	Units []string
}

type Configs struct {
	Cfgs []Config `resources`
}

type MetricDriver struct {
	cli *MetricCli
}

// CollectMetrics: Driver entry point to collect metrics. This will be invoked by the dock

// metricsList-> posted metric list

// instanceID -> posted instanceID

// metricArray    -> the array of metrics to be returned

func (lvmdriver *MetricDriver) CollectMetrics(metricsList []string, instanceID string) (metricArray []model.MetricSpec, err error) {

	// get MEtrics to unit map

	metrictounitmap := getMetricToUnitMap()

	//validate metric support list

	supportedMetrics, err := lvmdriver.ValidateMetricsSupportList(metricsList, "volume")

	if supportedMetrics == nil {

		log.Infof("No metrics found in the  supported metric list")

	}

	metricMap, err := lvmdriver.cli.CollectMetrics(supportedMetrics, instanceID)

	var tempMetricArray []model.MetricSpec

	for _, element := range metricsList {

		val, _ := strconv.ParseFloat(metricMap[element], 64)

		//Todo: See if association  is required here, resource discovery could fill this information

		associatorMap := make(map[string]string)

		metricValue := model.Metric{

			Timestamp: getCurrentUnixTimestamp(),

			Value: val,
		}

		metricValues := make([]model.Metric, 0)

		metricValues = append(metricValues, metricValue)

		metric := model.MetricSpec{

			InstanceID: instanceID,

			InstanceName: metricMap["InstanceName"],

			Job: "OpenSDS",

			Associator: associatorMap,

			Source: "Node",

			//Todo Take Componet from Post call, as of now it is only for volume

			Component: "Volume",

			Name: element,

			//Todo : Fill units according to metric type

			Unit: metrictounitmap[element],

			//Todo : Get this information dynamically ( hard coded now , as all are direct values

			IsAggregated: false,

			MetricValues: metricValues,
		}

		tempMetricArray = append(tempMetricArray, metric)

	}

	metricArray = tempMetricArray

	return

}

func metricInMetrics(metric string, metriclist []string) bool {

	for _, m := range metriclist {

		if m == metric {

			return true

		}

	}

	return false

}

func getCurrentUnixTimestamp() int64 {

	now := time.Now()

	secs := now.Unix()

	return secs

}

func getMetricToUnitMap() map[string]string {

	//construct metrics to value map

	var configs Configs

	//Read supported metric list from yaml config

	//source, err := ioutil.ReadFile(ConfFile)

	//Todo: Move this to read from file

	source := []byte(data)

	/*if err != nil {


	  log.Errorf("Not able to read file  %s  %v", ConfFile, err)


	  }*/

	error := yaml.Unmarshal(source, &configs)

	if error != nil {

		log.Fatalf("Unmarshal error: %v", error)

	}

	metrictounitmap := make(map[string]string)

	for _, resources := range configs.Cfgs {

		switch resources.Resource {

		//ToDo: Other Cases needs to be added

		case "volume":

			for index, metricName := range resources.Metrics {

				metrictounitmap[metricName] = resources.Units[index]

			}

		}

	}

	return metrictounitmap

}

//     ValidateMetricsSupportList:- is  to check whether the posted metric list is in the uspport list of this driver

//     metricList-> Posted metric list

// supportedMetrics -> list of supported metrics

func (d *MetricDriver) ValidateMetricsSupportList(metricList []string, resourceType string) (supportedMetrics []string, err error) {

	var configs Configs

	//Read supported metric list from yaml config

	//source, err := ioutil.ReadFile(ConfFile)

	//Todo: Move this to read from file

	source := []byte(data)

	/*if err != nil {


	   log.Errorf("Not able to read file  %s%v", ConfFile, err)


	}*/

	error := yaml.Unmarshal(source, &configs)

	if error != nil {

		log.Fatalf("Unmarshal error: %v", error)

	}

	for _, resources := range configs.Cfgs {

		switch resources.Resource {

		//ToDo: Other Cases needs to be added

		case "volume":

			for _, metricName := range metricList {

				if metricInMetrics(metricName, resources.Metrics) {

					supportedMetrics = append(supportedMetrics, metricName)

				} else {

					log.Infof("metric:%s is not in the supported list", metricName)

				}

			}

		}

	}

	return supportedMetrics, nil

}

func (d *MetricDriver) Setup() error {

	cli, err := NewMetricCli()

	if err != nil {

		return err

	}

	d.cli = cli

	return nil

}

func (*MetricDriver) Unset() error { return nil }


    --------------------------------------------------------------------------------------------

*   **Add some code in contrib/drivers/driver.go, so osdsdock can find your driver.**

    ------------

func InitMetricDriver(resourceType string) MetricDriver {

	var d MetricDriver

	switch resourceType {

	case config.LVMDriverType:

		d = &lvm.MetricDriver{}

		break

	default:

		d = &sample.Driver{}

		break

	}

	d.Setup()

	return d

}
    ------------------------------------------------------


###    **How to test Driver code**

*   Method1
    *   Write a test code which invokes CollectMetrics()

        Lvmmetrics_test.go


        -----------------------------------------------------


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

       	metricList := []string{"IOPS", "ReadThroughput", "WriteThroughput", "ResponseTime"}

       	var metricDriver = &MetricDriver{}

       	metricDriver.Setup()

       	metricArray, err := metricDriver.CollectMetrics(metricList, "sda")

       	if err != nil {

       		t.Errorf("CollectMetrics call to lvm driver failed: %+v\n", err)

       	}

       	fmt.Println(metricArray)

       }


*   Execute test code and make sure you get output like this for the Print statement.

        --------------------


        === RUN   TestCollectMetrics


        [{sda 153.00 OpenSDS map[] Node Volume IOPS tps false [{1555731602 824}]} {sda 153.00 OpenSDS map[] Node Volume ReadThroughput KB/s false [{1555731602 1620}]} {sda 153.00 OpenSDS map[] Node Volume WriteThroughput KB/s false [{1555731602 15.97}]} {sda 153.00 OpenSDS map[] Node Volume ResponseTime ms false [{1555731602 0.39}]}]


        --- PASS: TestCollectMetrics (1.07s)


        PASS


        --------------------------------------------------------

*   Method2
    *   If you have the POST API code integrated to your branch and prometheus and prometheus push gateway configured , you can initiate post request and see the metrics are available in prometheus
    *

<!-- Docs to Markdown version 1.0β17 -->
