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

> Network metadata for the array_of_things sensor network

```
http://plenar.io/v1/api/sensor-networks/array_of_things
```

```json
{
  "meta": {
    "query": {
      "network": "array_of_things"
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
      "name": "array_of_things"
    }
  ]
}
```

If no network is specified, (just `/sensor-networks`) metadata for
all sensor networks will be returned.

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

> Node metadata for node 011 in the array_of_things network

```
http://plenar.io/v1/api/sensor-networks/array_of_things/nodes/011
```

```json
{
    "meta": {
        "query": {
            "network": "array_of_things"
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
                "network": "array_of_things",
                "id": "011"
            }
        },
        ...
    ]
}
```
> Node metadata for all nodes in the array_of_things network within the given geometry

```
http://plenar.io/v1/api/sensor-networks/array_of_things/nodes/?geom={"type":"Polygon","coordinates”:[[[40.0, -90.0],[45.0, -90.0],[45.0, -85.0],[40.0, -85.0]]]}
```

```json
{
  "meta": {
    "query": {
      "geom": {"type": "Polygon", "coordinates": [[[40.0, -90.0], [45.0, -90.0], [45.0, -85.0], [40.0, -85.0]]]},
      "network": "array_of_things"
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
        "network": "plenario_development"
          }
    }
  ]
}
```

Retrieve metadata about nodes in a sensor network.
Nodes are formatted as GeoJSON,
with non-spatial metadata stored in the each node's `properties` object.

Get metadata about all nodes in a network,

`sensor-networks/<network>/nodes`

a specific node,

`sensor-networks/<network>/nodes/<node>`

or all nodes in a network within some geometry .

`sensor-networks/<network>/nodes?geom=<geojson>`

### Common Query Syntax

|**Parameter Name**  | **Required?** | **Default**
|--------------- | -----------------| ---
| [**geom**](#space-filtering) | no | none

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

> Feature of interest metadata for magnetic_field

```
http://plenar.io/v1/api/sensor-networks/array_of_things/features/magnetic_field
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
            "properties": [
                {
                    "type": "FLOAT",
                    "name": "x"
                },
                {
                    "type": "FLOAT",
                    "name": "y"
                },
                {
                    "type": "FLOAT",
                    "name": "z"
                }
            ]
        }
    ]
}
```

Retrieve features of interest defined within a network.

If no feature is specified, the default is to return metadata for
all features of interest within the network

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

```
http://plenar.io/v1/api/sensor-networks/array_of_things/sensors/TMP112
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

If no single sensor is specified, the default is to return metadata for
all sensors within the network

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
