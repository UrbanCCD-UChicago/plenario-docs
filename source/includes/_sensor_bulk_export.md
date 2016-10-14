## Bulk Data Export

`GET /v1/api/sensor-networks/<network>/download`

Because the [raw data endpoint /query](#raw-observations) is designed to respond quickly,
it is limited to 10000 observations.It also only lets you query over one feature of interest per call.

If you want a larger dump of data, potentially over a long period
of time and over many features, you can use /download to request a
bulk export.

/download returns a ticket that you can use to check on your request's progress at `/v1/api/jobs/{ticket}`.
When the export is complete, `/v1/api/jobs/{ticket}` will give you a link where you can download the file.

```json
{
  "status": {
    "status": "queued",
    "meta": {
      "queueTime": "2016-10-13 17:51:47.980835"
    }
  },
  "ticket": "12a639f8f73acdab7d48835c9b404664",
  "request": {
    "query": {
      "start_datetime": "2016-07-15t17:51:47.978722",
      "network": "array_of_things",
      "datadump_urlroot": "http://plenar.io/",
      "query_fn": "aot_point",
      "limit": 1000,
      "end_datetime": "2016-10-13t17:51:47.978770",
      "offset": 0,
      "features": [
        "temperature"
      ]
    },
    "endpoint": "observation_datadump"
  },
  "result": ""
}
```

### Common Query Syntax

|**Parameter Name**                                    | **Required?** | **Default**            |
|----------------------------------------------------- | ------------- | ---------------------- |
| [**features**](#features-of-interest)                | no            | all network features   |
| [**nodes**](#nodes)                                  | no            | all nodes in network   |
| [**sensors**](#sensors)                              | no            | all sensors in network |
| [**geom**](#space-filtering)                         | no            | none                   |
| [**start_datetime**](#sensor-network-time-filtering) | no            | 7 days ago             |
| [**end_datetime**](#sensor-network-time-filtering)   | no            | now                    |

### Responses

| **Attribute Name** | **Attribute Description**      |
| ------------------ | ------------------------------ |
| **status**         | Details of the ongoing export  |
| **ticket**         | Ticket ID of the current job   |
| **request**        | Contains query and endpoint    |
| **query**          | Shows values used in the query |
| **result**         | On success, the download link  |
| **error**          | Contains any errors            |
