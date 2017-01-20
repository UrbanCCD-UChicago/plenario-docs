## Bulk Data Export

`GET /v1/api/sensor-networks/<network>/download`

Because the [raw data endpoint /query](#raw-observations) is designed to respond quickly,
it is limited to 10000 observations. It also only lets you query over one feature of interest.

If you want a larger dump of data, potentially over a long period
of time and over many features, you can use /download to request a
bulk export.

### Common Query Syntax

|**Parameter Name**                                    | **Required?** | **Default**            |
|----------------------------------------------------- | ------------- | ---------------------- |
| [**features**](#features-of-interest)                | no            | all network features   |
| [**nodes**](#nodes)                                  | no            | all nodes in network   |
| [**sensors**](#sensors)                              | no            | all sensors in network |
| [**geom**](#space-filtering)                         | no            | none                   |
| [**start_datetime**](#sensor-network-time-filtering) | no            | 7 days ago             |
| [**end_datetime**](#sensor-network-time-filtering)   | no            | now                    |
