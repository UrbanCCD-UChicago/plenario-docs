# Sensor Network Data

> A sensor node (represented as GeoJSON)

```json
{
    "geometry": {
        "type": "Point",
        "coordinates": [
            -87.6298,
            41.8781
        ]
    },
    "type": "Feature",
    "properties": {
        "info": {
			"orientation": "NE",
			"height_in_meters": "5"
		},
        "sensors": ["tmp421", "hmc5883l"],
        "network_name": "array_of_things",
        "id": "011"
    }
}
```

Sensor network data is less structured than event and shape data.
While Plenario represents event and shape data as tables with rows and columns,
it represents sensor data as key-value pairs.
To make sensor data easier to query
Plenario uses the sensor networks' hierarchies for organization.

### Sensor Network Hierarchy

> A feature of interest with a single observed property

```json
{
  "name": "temperature",
  "observed_properties": [
	{
	  "type": "float",
	  "name": "temperature",
	  "unit": "Degrees Celsius",
	  "description": "external air temperature"
	}
  ]
}
```

> A feature of interest with three observed properties, representing a three-dimensional magnetic field vector

```json
{
	"name": "magnetic_field",
	"observed_properties": [
	    {
	        "type": "float",
	        "name": "x",
			"unit": "gauss"
	    },
	    {
	        "type": "float",
	        "name": "y",
			"unit": "gauss"
	    },
	    {
	        "type": "float",
	        "name": "z",
			"unit": "gauss"
	    }
	]
}
```

![metadatamodel](images/sensor-overview.svg)

Each sensor network in Plenario has _nodes_, devices installed at some physical location.
Nodes have _sensors_ that report on physical properties like temperature.
Sensors report _observations_ that contain a timestamp and one or more values.

To make it easier to query across different sensors,
Plenario uses the concept of _features of interest_.
Features of interest define properties that different sensors can report on.
For example, if the `tempy9000` and `tempmaster_mach2` sensors both report on temperature,
you can query on the `temperature` feature of interest to find temperature observations
instead of finding and specifying every temperature sensor in the network.

### Querying Data

> A sensor observation, reporting on `magnetic_field`.

```json
{
	"feature_of_interest": "magnetic_field",
	"node_id": "029",
	"sensor": "hmc5883l",
	"meta_id": 11,
	"results": {
		"x": 0.602,
		"y": 0.613,
		"z": 0.604
	},
	"datetime": "2016-12-25T00:54:18"
}
```

You can query sensor data much like you can query tabular data,
filtering by time and space and performing aggregations.
Unlike tabular data, you can also stream incoming sensor data over Websockets.

### Querying Metadata

You can also query metadata - networks, nodes, sensors, and features of interest.
We split metadata into _high-level_ and _detailed_ metadata.
High-level metadata tells you everything you need to know to form a valid data query -
What are all the nodes that have ever been in this network?
What are all the sensors that have ever been installed on a node?
Detailed metadata lets you dig in to how an observation was derived -
What constants were used to calibrate this sensor at the time of this observation?
What sensors were installed on this node at the time of this observation?

### Common Query Syntax

> Query over January 2015

```
&start_datetime=2015-1-1&end_datetime=2015-2-1
```

For endpoints that allow time filtering, you can provide the query parameters `start_datetime` (observation date greater than or equal to) and `end_datetime` (less than or equal to). Both must be formatted in ISO 8601 format (YYYY-MM-DDThh:mm:ss.sss, to whatever precision you desire).

|parameter|description|default
|---|---|---|
|`start_datetime `|start of date range|90 days ago|
|`end_datetime `|end of date range|now|
