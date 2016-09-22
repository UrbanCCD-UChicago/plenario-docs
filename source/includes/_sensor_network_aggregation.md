## -- Sensor Network Aggregation

This endpoint lets you see historcal trends by aggregating individual
node observations up to larger units of time. This is done by applying 
one of the provided aggregate functions on all observations found within
a specified window of time. 

### Endpoint-Specific Parameters

| **Parameter Name** | **Required?** | **Parameter Default** | **Parameter Description**                       |
| ------------------ | ------------- | --------------------- | ----------------------------------------------- |
| **node_id**        | Yes           | None                  | Target node                                     |
| **function**       | Yes           | None                  | Aggregate function to apply                     |
| **feature**        | Yes           | None                  | Node feature to aggregate                       |
| **sensors**        | No            | All sensors           | Narrows features to only those on these sensors |
| **start_datetime** | No            | Yesterday's datetime  | Beginning of observation window                 |
| **end_datetime**   | No            | Current datetime      | End of observation window                       |

### Responses

| **Attribute Name** | **Attribute Description**                             |
| ------------------ | ----------------------------------------------------- |
| **meta**           | Holds meta information about the query and its result |
| **meta.query**     | Query parameters used                                 |
| **meta.message**   | Server issued information such as warnings            |
| **meta.total**     | Total number of records retrieved                     |
| **data**           | Holds result information from a successful query      |
| **error**          | Holds error information from a failed query           |

### Available Aggregates

| **key** | **Description**    |
| ------- | ------------------ |
| **avg** | average	       | 
| **std** | standard deviation |
| **var** | variance	       |

## GET `/v1/api/sensor-networks/<network-name>/aggregate?<args>`

> Standard deviations of magnetic field in the y direction over the last day

```
http://plenario-app-venusaur.us-east-1.elasticbeanstalk.com/v1/api/
sensor-networks/plenario_development/aggregate?feature=magnetic_field.y
&node_id=node_dev_1&function=std
```

```json
{
  "meta": {
    "query": {
      "function": "std",
      "start_datetime": "2016-09-21T16:22:25.987058",
      "network_name": "plenario_development",
      "feature": "gas_concentration",
      "node_id": "node_dev_2",
      "end_datetime": "2016-09-22T16:22:25.987107",
      "agg_unit": "hour"
    },
    "message": [
      
    ],
    "total": 24
  },
  "data": [
    {
      "n2": {
        "std": 0.284548489817335,
        "count": 310
      },
      "co2": {
        "std": 0.282142285621254,
        "count": 310
      },
      "time_bucket": "2016-09-21T16:00:00"
    },
    {
      "n2": {
        "std": 0.292254493510091,
        "count": 441
      },
      "co2": {
        "std": 0.285662120705567,
        "count": 441
      },
      "time_bucket": "2016-09-21T17:00:00"
    },
    {
      "n2": {
        "std": 0.292257961896799,
        "count": 479
      },
      "co2": {
        "std": 0.285328724420976,
        "count": 479
      },
      "time_bucket": "2016-09-21T18:00:00"
    },
    {
      "n2": {
        "std": 0.275324727522044,
        "count": 427
      },
      "co2": {
        "std": 0.289401004433159,
        "count": 427
      },
      "time_bucket": "2016-09-21T19:00:00"
    },
    {
      "n2": {
        "std": 0.294165323573799,
        "count": 438
      },
      "co2": {
        "std": 0.295827494148314,
        "count": 438
      },
      "time_bucket": "2016-09-21T20:00:00"
    },
    {
      "n2": {
        "std": 0.279975321417624,
        "count": 42
      },
      "co2": {
        "std": 0.293083388277757,
        "count": 42
      },
      "time_bucket": "2016-09-21T21:00:00"
    },
    {
      "count": 0,
      "time_bucket": "2016-09-21T22:00:00"
    },
    {
	...
    },
    {
      "count": 0,
      "time_bucket": "2016-09-22T15:00:00"
    }
  ]
}
```

