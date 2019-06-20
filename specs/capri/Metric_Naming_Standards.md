# OpenSDS metric Naming standards  

OpenSDS has different sources to generate metrics for Prometheus. Sources are in the form of exporters or adapters. Here we are defining a standard of forming metric name and labels from the metricSpec structure.

### metric spec structure 

model.MetricSpec{
			InstanceID,
			InstanceName,
			Job,
			Labels,
			Component,
			Name,
			Unit,
			Aggr_type,
			MetricValues[]{timestamp,value},
		}
		
### metric name format

MetricSpec.Job_MetricSpec.Component_MetricSpec.Name_MetricSpec.unit_<Aggr_type>
Example:OpensSDS_Volume_ResponseTime_ms

### metric label format
  
  Labels will go as map[string]string in the Labels map.
Example: {device="sda"}
