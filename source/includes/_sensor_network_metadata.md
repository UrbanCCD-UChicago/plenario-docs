## Metadata Queries

The sensor network metadata endpoints give you all the information you need
to construct valid queries on sensor data.
Note that the metadata from these endpoints doesn't necessarily
represent the current configuration of each sensor network.
For example, if a node is up for a year and then removed,
that node will still be visible in the `/nodes` endpoint.
That way, you can still find it and query the data collected while it was up.
Similarly, if a sensor is removed from a node,
that sensor will still be visible in that node's `sensors` property.

### Responses

| **Attribute Name** | **Attribute Description**       |
| ------------------ | ------------------------------- |
| **query**          | Shows values used in the query. |
| **message**        | Reports warnings (if any).      |
| **total**          | Total number of records found.  |
| **data**           | Contains records                |
| **error**          | Contains any errors             |

Either the **data** or **error** field will be returned, but not both.

## -- Sensor Networks

`sensor-networks/<network>`

Retrieve metadata about one or many sensor networks that publish observations to Plenario.
If no network is specified, metadata for
all sensor networks will be returned.

> Network metadata for the array_of_things sensor network

```
http://plenar.io/v1/api/sensor-networks/array_of_things_chicago
```

```json
{
  "meta": {
    "query": {
      "network": "array_of_things_chicago"
    },
    "message": [ ],
    "total": 1
},
  "data": [
    {
      "sensors": [
        "TMP112"
      ],
      "info": { },
      "nodes": [
        "000",
        "011"
      ],
      "features": [
        "temperature"
      ],
      "name": "array_of_things_chicago"
    }
  ]
}
```

A sensor network is a collection of sensor nodes maintained by one organization.
Within a sensor network's metadata,
you can find the IDs of nodes within the network
and all of the features of interest those nodes report on.
With this information, you can launch queries on features of interest or drill down further into the metadata by node.

### Responses

| **Attribute Name** | **Attribute Description**                                           |
| ------------------ | ------------------------------------------------------------------- |
| **name**           | Sensor network name.                                                |
| **info**           | JSON containing any information provided by the network maintainer. |
| **nodes**          | Array of node IDs for all nodes within the network.                 |
| **sensors**        | Array of all sensor models within the network.                      |
| **features**       | Array of all features of interest that the network measures.        |

## -- Nodes

`GET /v1/api/sensor-networks/<network>/nodes/<node>`

Retrieve metadata about nodes in a sensor network.
Nodes are formatted as GeoJSON,
with non-spatial metadata stored in the each node's `properties` object.

> Node metadata for node 011 in the array_of_things network

```
http://plenar.io/v1/api/sensor-networks/array_of_things_chicago/nodes/011
```

```json
{
    "meta": {
        "query": {
            "network": "array_of_things_chicago"
        },
        "message": [],
        "total": 1
    },
    "data": [
        {
            "geometry": {
                "type": "Point",
                "coordinates": [
                    41.9084,
                    -87.6214
                ]
            },
            "type": "Feature",
            "properties": {
                "info": {},
                "sensors": [
                    "tmp112"
                ],
                "network": "array_of_things_chicago",
                "id": "011"
            }
        },
        ...
    ]
}
```
> Node metadata for all nodes in the array_of_things network within the given geometry

```
http://plenar.io/v1/api/sensor-networks/array_of_things_chicago/nodes/?geom={"type":"Polygon","coordinates”:[[[40.0, -90.0],[45.0, -90.0],[45.0, -85.0],[40.0, -85.0]]]}
```

```json
{
  "meta": {
    "query": {
      "geom": {"type": "Polygon", "coordinates": [[[40.0, -90.0], [45.0, -90.0], [45.0, -85.0], [40.0, -85.0]]]},
      "network": "array_of_things_chicago"
    },
    "message": [ ],
    "total": 5
  },
  "data": [
    {
      "geometry": {
        "type": "Point",
        "coordinates": [
          -87.623177,
          41.881832
        ]
      },
      "type": "Feature",
      "properties": {
        "info": null,
        "sensors": [
          "sensor_dev_1",
          "sensor_dev_4"
        ],
        "id": "node_dev_1",
        "network": "array_of_things_chicago"
          }
    }
  ]
}
```
A node is a collection of one or more sensors
reporting from a fixed location.
Each node belongs to exactly one network.

Get metadata about all nodes in a network,

`sensor-networks/<network>/nodes`

a specific node,

`sensor-networks/<network>/nodes/<node>`

or all nodes in a network within some geometry .

`sensor-networks/<network>/nodes?geom=<geojson>`

### Common Query Syntax

|**Parameter Name**            | **Required?** | **Default** |
|----------------------------- | ------------- | ----------- |
| [**geom**](#space-filtering) | no            | none        |

### Responses

| **Attribute Name** | **Attribute Description**                                           |
| ------------------ | ------------------------------------------------------------------- |
| **id**             | Node identifier.                                                    |
| **network_name**   | Name of the network that maintains the node.                        |
| **info**           | JSON containing any information provided by the network maintainer. |
| **geometry**       | GeoJSON object giving the node’s location.                          |
| **sensors**        | Array of all sensors that the node has contained.                   |

## -- Features of Interest

`GET /v1/api/sensor-networks/<network>/features/<feature>`

Retrieve features of interest defined within a network.

If no feature is specified, the default is to return metadata for
all features of interest within the network

> Feature of interest metadata for magnetic_field

```
http://plenar.io/v1/api/sensor-networks/array_of_things_chicago/features/magnetic_field
```

```json
{
    "meta": {
        "query": {
            "network": "array_of_things",
            "features": [
                "magnetic_field"
            ]
        },
        "message": [],
        "total": 1
    },
    "data": [
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
    ]
}
```

> An observation of magnetic_field

```json
{
	"feature": "magnetic_field",
	"node": "029",
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

A feature of interest defines properties of the world that sensors observe,
like temperature or traffic.
Every observation is tagged with a feature of interest,
which determines how the observation is formatted.
A feature called `traffic_count` might be composed of _observed properties_
called `pedestrians`, `cars`, and `bicycles`.
Then an observation of `traffic_count` can only report values labeled
`pedestrians`, `cars`, and `bicycles`.

A feature of interest must have one or more observed properties.
Observed properties contain metadata like
data type (float, int, boolean, or string) and unit of measurement that can help you interpret observations.
Properties only define scalar values, so for features that are vector-valued,
you will find each dimension defined as a separate feature.
For example, the definition of `magnetic_field` to the right
has separate properties for the x, y, and z dimensions.


### Common Query Syntax

|**Parameter Name**            | **Required?** | **Default** |
|----------------------------- | --------------| ----------- |
| [**geom**](#space-filtering) | no            | none        |

### Responses

| **Attribute Name** | **Attribute Description**                              |
| ------------------ | ------------------------------------------------------ |
| **name**           | Feature of interest name.                              |
| **properties**     | Array of JSON objects containing property names, types, units, and any other necessary description.  |

## -- Sensors

`GET /v1/api/sensor-networks/<network>/sensors/<sensor>`

> Sensor metadata for sensor model TMP112

Retrieve metadata about sensors.
If no sensor is specified, returns all sensors from the specified network.

```
http://plenar.io/v1/api/sensor-networks/array_of_things_chicago/sensors/TMP112
```

```json
{
  "meta": {
    "query": {
      "network": "array_of_things",
      "sensor": "tmp112"
    },
    "message": [ ],
    "total": 1
  },
  "data": [
    {
      "info": { },
      "name": "tmp112",
      "properties": [
        "temperature.temperature"
      ]
    }
  ]
}
```

A sensor is a piece of equipment that generates observations.
Every observation is tagged with the sensor that generated it.
The `info` object in sensor metadata contains general metadata
like a link to the sensor's datasheet.
The `properties` list contains all of the _observed_properties_
that the sensor reports on, written as <feature>.<observed_property>.
It is not enough to specify just the feature,
because some sensors may report on only a subset of a feature's properties.

### Common Query Syntax

| **Parameter Name**           | **Required?** | **Default** |
| ---------------------------- | ------------- | ----------- |
| [**geom**](#space-filtering) | no            | none        |

### Responses

| **Attribute Name** | **Attribute Description**                                                     |
| ------------------ | ----------------------------------------------------------------------------- |
| **name**           | Name of sensor model.                                                         |
| **info**           | JSON containing any information provided by the network maintainer.           |
| **properties**     | Array of properties measured by the sensor in the format `feature`.`property` |

## -- Map

> Map the array_of_things_chicago network

```
http://plenar.io/v1/api/sensor-networks/array_of_things_chicago/map
```
```json
{
  "0000001e0610b9e7": {
    "apds-9006-020": {
      "light_intensity.500nm": "intensity"
    }, 
    "bmi160": {
      "acceleration.x": "accel_x", 
      "acceleration.y": "accel_y", 
      "acceleration.z": "accel_z", 
      "orientation.x": "orient_x", 
      "orientation.y": "orient_y", 
      "orientation.z": "orient_z"
    }
  },
  "0000001e0610ba72": {
    "apds-9006-020": {
      "light_intensity.500nm": "intensity"
    }, 
    "bmi160": {
      "acceleration.x": "accel_x", 
      "acceleration.y": "accel_y", 
      "acceleration.z": "accel_z", 
      "orientation.x": "orient_x", 
      "orientation.y": "orient_y", 
      "orientation.z": "orient_z"
    }
  }
}
```

`GET /v1/api/sensor-networks/<network>/map`

Represents the specified network as a tree. This can be helpful when it is
necessary to programmatically explore the contents of a network or to see
the relationships of its subcomponents.


## -- Validation

> Valid parameters

```
http://plenar.io/v1/api/sensor-networks/array_of_things_chicago/check?
node=0000001e0610b9e7&sensor=bmp180&feature=atmospheric_pressure
```
```json
{
  "message": "Your query params are good to go."
}
```

> Invalid parameters

```
http://plenar.io/v1/api/sensor-networks/array_of_things_chicago/check?
node=0000&sensor=bmp181&feature=atmospherik_pressure
```
```json
{
  "error": {
    "feature": [
      "atmospherik_pressure does not exist"
     ],
    "node": [
      "0000 does not exist"
    ],
    "sensor": [
      "bmp181 does not exist"
    ]
  },
  "meta": {
    "query": {
      "node": "0000",
      "feature": "atmospherik_pressure",
      "sensor": "bmp181"
    }
  }
}
```

`GET /v1/api/sensor-networks/<network>/check`

While all API endpoints will inform you if your query parameters
are invalid - it can be helpful to check any combination of parameters
for a given network.

### Common Query Syntax

| **Paramater Name** | **Required?** | **Description** |
| ------------------ | ------------- | --------------- |
| **node(s)**        | no            | Node ids        |
| **sensor(s)**      | no            | Sensor names    |
| **feature(s)**     | no            | Feature names   |