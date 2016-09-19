## Temporal Aggregation

## -- `GET v1/api/timeseries`

> Generate counts of crime and environmental complaints per year from 2010 to 2015.

```
http://plenar.io/v1/api/timeseries/?obs_date__ge=2010-1-1&obs_date__le=2015-12-31&dataset_name__in=crimes_2001_to_present,cdph_environmental_complaints&agg=year
```

```json
{
    "meta": {
        "status": "ok",
        "query": {
            "obs_date__ge": "2007-11-12",
            "data_type": "json",
            "obs_date__le": "2008-11-12",
            "agg": "year",
            "dataset": null,
            "dataset_name__in": [
                "crimes_2001_to_present",
                "cdph_environmental_complaints"
            ],
            "geom": null
        },
        "message": [ ]
    },
    "objects": [
        {
            "dataset_name": "cdph_environmental_complaints",
            "count": 6836,
            ...
            "items": [
                {
                    "count": 1262,
                    "datetime": "2010-01-01"
                },
                {
                    "count": 1460,
                    "datetime": "2011-01-01"
                },
                {
                    "count": 1032,
                    "datetime": "2012-01-01"
                },
                {
                    "count": 1187,
                    "datetime": "2013-01-01"
                },
                {
                    "count": 1074,
                    "datetime": "2014-01-01"
                },
                {
                    "count": 821,
                    "datetime": "2015-01-01"
                }
            ],
        },
        {
            "dataset_name": "crimes_2001_to_present",
            ...
            "count": 429324,
            "items": [
                {
                    "count": 370092,
                    "datetime": "2010-01-01"
                },
                {
                    "count": 351483,
                    "datetime": "2011-01-01"
                },
                {
                    "count": 335602,
                    "datetime": "2012-01-01"
                },
                {
                    "count": 306603,
                    "datetime": "2013-01-01"
                },
                {
                    "count": 274258,
                    "datetime": "2014-01-01"
                },
                {
                    "count": 261662,
                    "datetime": "2015-01-01"
                }
            ],
        }
    ]
}
```

This endpoint creates timeseries over one or more [event datasets](#dataset-types)
by counting how many events fall into each time slice.
You can set the unit of aggregation with the `agg` parameter.

_Common Query Syntax_

|**Parameter Name**  | **Required?** | **Default**
|--------------- | -----------------| ---
| [**dataset_name**/**dataset_name__in**](#specifying-a-dataset) | no | every event dataset
| [**location_geom__within**](#space-filtering) | no | none
| [**obs_date__ge** & **obs_date__le**](#time-filtering) | no | 90 days ago - today
| [**[dataset_name]__filter**](#attribute-filtering) | no | none

_Endpoint-Specific Parameters_

| **Parameter Name**       | **Parameter Default** | **Parameter Description**                                                                                                                                                                                                                                          |
|----------------------|-------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **agg**                  | week              | Time resolution to aggregate observation counts by. Supported values: <ul><li>day</li><li>week</li><li>month</li><li>quarter</li><li>year</li></ul>                                                                                                                                                   |
| **data_type**            | json              | Response data format. Current options are `json` and `csv`.


_Response_

The API responds with a list of datasets with a dense matrix of counts grouped
by temporal aggregation.
The actual timeseries values are inside of `items`.
Many of the [metadata attributes](#get-v1-api-datasets) from `/datasets` are included as well.

| **Attribute Name** | **Attribute Description**                              |
| ------------------ | ------------------------------------------------------ |
| **status**         | Indicates query success, can be `ok` or `error`.       |
| **query**          | Shows values used in the query.                        |
| **message**        | Reports errors or warnings (if any).                   |
| **dataset_name**   | The dataset this timeseries was created from.          |
| **count**          | Total number of records found.  |
| **datetime**       | Date of aggregation
| **items**          | Counts of aggregated records and delimiting datetimes. |

## -- `GET v1/api/detail-aggregate/`

_This endpoint is deprecated in favor of `/timeseries`_

```
http://plenar.io/v1/api/detail-aggregate/?agg=year&dataset_name=crimes_2001_to_present&obs_date__ge=2012-1-1
```

Just like `/timeseries`,
except that it requires exactly one dataset through the `dataset_name` parameter.
