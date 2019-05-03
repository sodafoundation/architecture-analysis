# OpenSDS metric Naming standards  

OpenSDS has different sources to generate metrics for Prometheus. Sources are in the form of exporters or adapters. Here we are defining a standard of forming metric name and labels from the metricSpec structure.

### metric spec structure 

model.MetricSpec{
			InstanceID,
			InstanceName,
			Job,
			Associator,
			Source,
			Component,
			Name,
			Unit,
			IsAggregated,
			MetricValues[]{timestamp,value},
		}
		
### metric name format

MetricSpec.Job_MetricSpec.Component_MetricSpec.Name_MetricSpec.unit
Example:OpensSDS_Volume_ResponseTime_ms

### metric label format
  
  {"instanceID":MetricSpecInstanceID.,"device":MetricSpec.InstanceName,"source":MetricSpec.Source}
Example: {device="sda",instanceID="sda",source="Node"}
