## Sensor Data Queries

## -- `GET /v1/api/sensor-networks/<network_name>/query`

> All temperature readings from HTU21D sensors on nodes 000 and 011 in the array_of_things network

```
http://plenar.io/v1/api/sensor-networks/array_of_things/query?features_of_interest=temperature&sensors=HTU21D&nodes=000,011
```

```json
{
  "meta": {
    "query": {
      "start_datetime": "2016-06-08T17:55:32.503905",
      "features_of_interest": [
        "temperature"
      ],
      "limit": 1000,
      "end_datetime": "2016-09-06T17:55:32.503935",
      "offset": 0,
      "sensors": [
        "htu21d"
      ],
      "network_name": "array_of_things",
      "nodes": [
        "000",
        "011"
      ]
    },
    "message": [],
    "total": 1000
  },
  "data": [
    {
      "feature_of_interest": "temperature",
      "node_id": "011",
      "sensor": "htu21d",
      "meta_id": 54,
      "results": {
        "temperature": 30.12
      },
      "datetime": "2016-08-25T00:54:18"
    },
    ...
  ]
}
```
> All readings from the array_of_things network in December 2016 within the given geometry

```
http://plenar.io/v1/api/sensor-networks/array_of_things/query?location_geom__within={"type":"Polygon","coordinates":[[[40.0, -90.0],[45.0,-90.0],[45.0, -85.0],[40.0, -85.0]]]}&start_datetime=2016-12-1T00:00:00.000&end_datetime=2017-1-1T00:00:00.000

```

```json
{
  "meta": {
    "query": {
      "start_datetime": "2016-12-1T00:00:00.000",
      "limit": 1000,
      "geom": {"type":"Polygon","coordinates":[[[40.0, -90.0],[45.0,-90.0],[45.0, -85.0],[40.0, -85.0]]]},
      "end_datetime": "2017-1-1T00:00:00.000",
      "offset": 0,
      "network_name": "array_of_things"
    },
    "message": [],
    "total": 1000
  },
  "data": [
    {
      "feature_of_interest": "temperature",
      "node_id": "029",
      "sensor": "TMP112",
      "meta_id": 11,
      "results": {
        "temperature": 17.22
      },
      "datetime": "2016-12-25T00:54:18"
    },
    ...
  ]
}
```

### Common Query Syntax

|**Parameter Name**  | **Required?** | **Default**
|--------------- | -----------------| ---
| [**nodes**](#nodes) | no | all nodes in network
| [**sensors**](#sensors) | no | all sensors in network
| [**features_of_interest**](#features-of-interest) | no | all features reported on by network
| [**location_geom__within**](#space-filtering) | no | none
| [**start_datetime** & **end_datetime**](#sensor-network-time-filtering) | no | 90 days ago - now

### Responses

| **Attribute Name** | **Attribute Description**                              |
| ------------------ | ------------------------------------------------------ |
| **query**          | Shows values used in the query.                        |
| **message**        | Reports warnings (if any).                   |
| **total**          | Total number of records found.                         |
| **data**      | Contains records        |
| **error**     | Contains any errors        |

either the **data** or **error** field will be returned, not both

### Data Format

| **Attribute Name** | **Attribute Description**                              |
| ------------------ | ------------------------------------------------------ |
| **node_id**          | ID of the node that generated the result.                        |
| **sensor**        | Sensor model that generated the result.                   |
| **feature_of_interest**          | Feature of interest that the result measures.                         |
| **datetime**      | Time at which the reading was taken        |
| **results**      | JSON of the feature of interest’s properties and measured values.       |
| **meta_id**     | Integer identifier for the node configuration and calibration information that generated the reading.        |
