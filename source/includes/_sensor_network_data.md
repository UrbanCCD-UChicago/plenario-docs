## HTTP Data Queries

These endpoints let you quickly fetch sensor observations
or generate aggregates over a node's history.

## -- Raw Observations

`GET /v1/api/sensor-networks/<network>/query`

> Temperature observations from HTU21D sensors on nodes 000 and 011 in the array_of_things network

Get observations.

```
http://plenar.io/v1/api/sensor-networks/array_of_things_chicago/query?feature=temperature&sensors=HTU21D&nodes=000,011
```

```json
{
  "meta": {
    "query": {
      "start_datetime": "2016-06-08T17:55:32.503905",
      "feature": "temperature",
      "limit": 1000,
      "end_datetime": "2016-09-06T17:55:32.503935",
      "offset": 0,
      "sensors": [
        "htu21d"
      ],
      "network": "array_of_things",
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
      "feature": "temperature",
      "node": "011",
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
> Temperature observations from the array_of_things_chicago network in December 2016 within the given geometry

```
http://plenar.io/v1/api/sensor-networks/array_of_things_chicago/query?feature=temperature&geom={"type":"Polygon","coordinates":[[[40.0, -90.0],[45.0,-90.0],[45.0, -85.0],[40.0, -85.0]]]}&start_datetime=2016-12-1T00:00:00.000&end_datetime=2017-1-1T00:00:00.000
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
      "network": "array_of_things"
    },
    "message": [],
    "total": 1000
  },
  "data": [
    {
      "feature": "temperature",
      "node": "029",
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

> All below freezing temperature readings from array_of_things_chicago network in the last 1 day

```
http://plenar.io/v1/api/sensor-networks/array_of_things_chicago/query?feature=temperature&filter={"col": "temperature", "val": "0", "op": "le"}
```

```json
{
  "meta": {
    "query": {
      "start_datetime": "2016-12-1T00:00:00.000",
      "limit": 1000,
      "end_datetime": "2017-1-1T00:00:00.000",
      "offset": 0,
      "network": "array_of_things"
    },
    "message": [],
    "total": 1000
  },
  "data": [
    {
      "feature": "temperature",
      "node": "029",
      "sensor": "TMP112",
      "meta_id": 11,
      "results": {
        "temperature": -4.01
      },
      "datetime": "2016-12-25T00:54:18"
    },
    ...
  ]
}
```

You must specify one feature of interest in the query parameters.
All other parameters are optional.
Response limit is 1000 by default, but can be set to a maximum of 10000.

### Common Query Syntax

|**Parameter Name**                                    | **Required?** | **Default**            |
|----------------------------------------------------- | ------------- | ---------------------- |
| [**feature**](#features-of-interest)                 | yes           | none                   |
| [**nodes**](#nodes)                                  | no            | all nodes in network   |
| [**sensors**](#sensors)                              | no            | all sensors in network |
| [**geom**](#space-filtering)                         | no            | none                   |
| [**start_datetime**](#sensor-network-time-filtering) | no            | 1 day ago              |
| [**end_datetime**](#sensor-network-time-filtering)   | no            | now                    |
| [**filter**](#attribute-filtering)                   | no            | none                   |
| [**limit**](#nodes)                                  | no            | 1000                   |

### Filter parameter

A single condition that applies to one property can be expressed in the following syntax: `{"op":"<operator>", "prop":"<property>", "val":"<target_value>"}`, where `<property>` must be a property of the feature specified by
the `feature` parameter.

You can use the following binary operators:

> LIKE examples:
>
> "%rat%" matches "rat poisoning" and "crater".
> "jo_" matches "joe" and "job" but not "josephine"

|operator|description|
|---|---|
|`eq`|equal to (==)|
|`gt`|greater than (>)|
|`ge`|greater than or equal to (>=)|
|`lt`|less than (<)|
|`le`|less than or equal to (<=)|
|`ne`|not equal (!=)|
|`in`|within a list of provided values like `0560,0110`|
|`like`|Match string pattern|
|`ilike`|Match string pattern, case insensitive|


<aside class="notice">
    <code>like</code> and <code>ilike</code> match patterns using the SQL LIKE operator. That lets you use <code>%</code> and <code>_</code> as wildcard characters. <code>%</code> matches a string of any length, while <code>_</code> matches exactly one character.
</aside>

### Responses

| **Attribute Name** | **Attribute Description**       |
| ------------------ | ------------------------------- |
| **query**          | Shows values used in the query. |
| **message**        | Reports warnings (if any).      |
| **total**          | Total number of records found.  |
| **data**           | Contains records                |
| **error**          | Contains any errors             |

either the **data** or **error** field will be returned, not both

### Data Format

| **Attribute Name** | **Attribute Description**                                                                             |
| ------------------ | ----------------------------------------------------------------------------------------------------- |
| **node**           | ID of the node that generated the result.                                                             |
| **sensor**         | Sensor model that generated the result.                                                               |
| **features**       | Feature of interest that the result measures.                                                         |
| **datetime**       | Time at which the reading was taken                                                                   |
| **results**        | JSON of the feature of interest’s properties and measured values.                                     |
| **meta_id**        | Integer identifier for the node configuration and calibration information that generated the reading. |

## -- Timeseries

`/v1/api/sensor-networks/<network>/aggregate?<args>`

> Standard deviations of gas concentrations from sensor_dev_3 on node_dev_2 for the past day

```
http://plenar.io/v1/api/sensor-networks/plenario_development/
aggregate?feature=gas_concentration&node=node_dev_2
&function=std&sensors=sensor_dev_3
```

```json
{
    "meta": {
        "query": {
            "node": "node_dev_2",
            "function": "std",
            "start_datetime": "2016-09-22T15:42:33.147232",
            "agg": "hour",
            "feature": [
                "gas_concentration"
            ],
            "end_datetime": "2016-09-23T15:42:33.147302",
            "sensors": [
                "sensor_dev_3"
            ],
            "network": "plenario_development"
        },
        "message": [ ],
        "total": 24
    },
    "data": [
        {
          "n2": {
              "std": 0.285770076888335,
              "count": 448
          },
          "co2": {
              "std": 0.289637279773448,
              "count": 448
          },
            "time_bucket": "2016-09-22T15:00:00"
        },
        ...
        {
          "n2": {
              "std": 0.281392701124023,
              "count": 287
          },
          "co2": {
              "std": 0.299980627541392,
              "count": 287
          },
            "time_bucket": "2016-09-23T14:00:00"
        }
    ]
}
```

This endpoint lets you see historical trends by aggregating individual
node observations up to larger units of time. This is done by applying
one of the provided aggregate functions on all observations found within
a specified window of time.

### Endpoint-Specific Parameters

| **Parameter Name**       | **Required?** | **Parameter Default** | **Parameter Description**                                        |
| ------------------------ | ------------- | --------------------- | ---------------------------------------------------------------- |
| **node**                 | Yes           | None                  | Target node                                                      |
| **function**             | Yes           | None                  | Aggregate function to apply: see table below                     |
| **feature**              | Yes           | None                  | Node feature to aggregate                                        |
| **sensors**              | No            | All sensors           | Narrows features to only those on these sensors                  |
| **start_datetime**       | No            | Yesterday's datetime  | Beginning of observation window                                  |
| **end_datetime**         | No            | Current datetime      | End of observation window                                        |
| **agg**                  | No            | hour                  | Size of time slices: one of minute, hour, day, week, month, year |

### Responses

| **Attribute Name** | **Attribute Description**                             |
| ------------------ | ----------------------------------------------------- |
| **meta**           | Holds meta information about the query and its result |
| **meta.query**     | Query parameters used                                 |
| **meta.message**   | Server issued information such as warnings            |
| **meta.total**     | Total number of aggregate records retrieved           |
| **data**           | Holds result information from a successful query      |
| **error**          | Holds error information from a failed query           |

### Available Aggregate Functions

| **key** | **Description**    |
| ------- | ------------------ |
| **avg** | average	           |
| **std** | standard deviation |
| **var** | variance	         |
| **min** | minimum	           |
| **max** | maximum	           |
| **med** | median	           |
