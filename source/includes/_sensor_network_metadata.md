# Sensor Network Metadata Endpoints

The following sensor network metadata endpoints are provided to allow
users to construct valid queries on sensor network data. They contain
the union of all past and current configurations. For example, if node X
once contained temperature sensor A, this sensor will appear in the node’s
metadata even if it is removed from the node. Thus a user can look 
at the node metadata and see that they can make a query for temperature
sensor A on node X. If they query over a time period in which this sensor 
was not operating in node X, they will receive no results.

### Responses

| **Attribute Name** | **Attribute Description**                              |
| ------------------ | ------------------------------------------------------ |
| **query**          | Shows values used in the query.                        |
| **message**        | Reports warnings (if any).                   |
| **total**          | Total number of records found.                         |
| **data**      | Contains records        |
| **error**     | Contains any errors        |

either the **data** or **error** field will be returned, not both

## `GET /v1/api/sensor-networks/<network_name>`

> Network metadata for the array_of_things sensor network

```
http://plenar.io/v1/api/sensor-networks/array_of_things
```

```json
{ 
  "meta": {
    "query": {
      "network_name": "array_of_things"
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
      "features_of_interest": [
        "temperature"
      ],
      "name": "array_of_things"
    }
  ]
}
```

If no network_name is specified, the default is to return metadata for
all sensor networks.

### Responses

| **Attribute Name** | **Attribute Description**                              |
| ------------------ | ------------------------------------------------------ |
| **name**          | Sensor network name.                        |
| **info**        | JSON containing any information provided by the network maintainer.                   |
| **nodes**          | Array of node IDs for all nodes within the network.                         |
| **sensors**      | Array of all sensor models within the network.       |
| **features_of_interest**     | Array of all features of interest that the network measures.        |

## `GET /v1/api/sensor-networks/<network_name>/nodes/<node_id>`

> Node metadata for node 011 in the array_of_things network

```
http://plenar.io/v1/api/sensor-networks/array_of_things/nodes/011
```

```json
{ 
  "meta": {
    "query": {
      "network_name": "array_of_things"
    },
    "message": [ ],
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
      "info": { },
      "sensors": [
        "TMP112"
      ],
      "network_name": "array_of_things",
      "id": "011"
    }
  ]
}
```
> Node metadata for all nodes in the array_of_things network within the given geometry

```
http://plenar.io/v1/api/sensor-networks/array_of_things/nodes/?location_geom__within={"type":"Polygon","coordinates”:[[[40.0, -90.0],[45.0, -90.0],[45.0, -85.0],[40.0, -85.0]]]}
```

```json
{ 
  "meta": {
    "query": {
      "geom": "{"type": "Polygon", "coordinates": [[[40.0, -90.0], [45.0, -90.0], [45.0, -85.0], [40.0, -85.0]]]}",
      "network_name": "array_of_things"
    },
    "message": [ ],
    "total": 5
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
      "info": { },
      "sensors": [
        "TMP112"
      ],
      "network_name": "array_of_things",
      "id": "011"
    },
    ...
  ]
}
```

If no node_id or location_geom__within is specified, the default is to return metadata for
all nodes within the network

### Common Query Syntax

|**Parameter Name**  | **Required?** | **Default**
|--------------- | -----------------| ---
| [**location_geom__within**](#space-filtering) | no | none

### Responses

| **Attribute Name** | **Attribute Description**                              |
| ------------------ | ------------------------------------------------------ |
| **id**          | Node identifier.                        |
| **network_name**        | Name of the network that maintains the node.                   |
| **info**          | JSON containing any information provided by the network maintainer.                         |
| **geometry**      | GeoJSON object giving the node’s location.       |
| **sensors**     | Array of all sensors that the node has contained.        |

## `GET /v1/api/sensor-networks/<network_name>/features_of_interest/<feature>`

> Feature of interest metadata for temperature

```
http://plenar.io/v1/api/sensor-networks/array_of_things/features_of_interest/temperature
```

```json
{
  "meta": {
    "query": {
      "network_name": "array_of_things",
      "feature": "temperature"
    },
    "message": [ ],
    "total": 1
  },
  "data": [
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
  ]
}
```

If no feature is specified, the default is to return metadata for
all features of interest within the network

### Responses

| **Attribute Name** | **Attribute Description**                              |
| ------------------ | ------------------------------------------------------ |
| **name**          | Feature of interest name.                        |
| **observed_properties**        | Array of JSON objects containing property names, types, units, and any other necessary description.  |

## `GET /v1/api/sensor-networks/<network_name>/sensors/<sensor>`

> Sensor metadata for sensor model TMP112

```
http://plenar.io/v1/api/sensor-networks/array_of_things/sensors/TMP112
```

```json
{
  "meta": {
    "query": {
      "network_name": "array_of_things",
      "sensor": "TMP112"
    },
    "message": [ ],
    "total": 1
  },
  "data": [
    {
      "info": { },
      "name": "TMP112",
      "observed_properties": [
        "temperature.temperature"
      ]
    }
  ]
}
```

If no single sensor is specified, the default is to return metadata for
all sensors within the network

### Responses

| **Attribute Name** | **Attribute Description**                              |
| ------------------ | ------------------------------------------------------ |
| **name**          | Name of sensor model.                        |
| **info**          | JSON containing any information provided by the network maintainer.                         |
| **observed_properties**      | Array of properties measured by the sensor in the format `feature_of_interest`.`observed_property`       |

