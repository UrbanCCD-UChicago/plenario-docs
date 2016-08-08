# Event Data Queries

## `GET /v1/api/detail`

> All Chicago homicides since May 1<sup>st</sup>, 2016

```
http://plenar.io/v1/api/detail/?dataset_name=crimes_2001_to_present&obs_date__ge=2016-5-1&crimes_2001_to_present__filter={"op": "eq", "col": "primary_type", "val": "HOMICIDE"}
```

```json
{
    "meta": {
        "status": "ok",
        "query": {
            "obs_date__ge": "2016-05-01",
            "data_type": "json",
            "obs_date__le": "2016-07-01T14:49:32.720091",
            "iucr": "0110",
            "dataset": "crimes_2001_to_present",
            "shape": null,
            "geom": null,
            "offset": 0,
            "metatable": "meta_master"
        },
        "message": [ ],
        "total": 121
    },
    "objects": [
        {
            "id": 22444,
            "case_number": "HZ247790",
            "date": "2016-05-02T01:35:00",
            "block": "059XX N RIDGE AVE",
            "iucr": "0110",
            "primary_type": "HOMICIDE",
            "description": "FIRST DEGREE MURDER",
            "location_description": "STREET",
            "arrest": false,
            "domestic": false,
            "beat": 2012,
            "district": 20,
            "ward": 40,
            "community_area": "77",
            "fbi_code": "01A",
            "x_coordinate": 1164091,
            "y_coordinate": 1939858,
            "year": 2016,
            "updated_on": "2016-05-09T15:51:54",
            "latitude": 41.990596764,
            "longitude": -87.671796648,
            "location": "(41.990596764, -87.671796648)"
        },
        ...
      ]
    }
```

Query an [event dataset](#dataset-types) and get back the raw individual records.

### Common Query Syntax

|**Parameter Name**  | **Required?** | **Default**
|--------------- | -----------------| ---
| [**dataset_name**](#specifying-a-dataset) | yes | n/a
| [**location_geom__within**](#space-filtering) | no | none
| [**obs_date__ge** & **obs_date__le**](#time-filtering)| no | 90 days ago - today
| [**[dataset_name]__filter**](#attribute-filtering)| no | none

### Endpoint-Specific Parameters

| Parameter Name       | Parameter Default | Parameter Description                                                                                                                                                                                              |
|----------------------|-------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **data_type**            | json              | Response data format. Current options are `json`, `geojson` and `csv`                                                                                                                                                         |
| **shape**                 | none              | Filter with a shape dataset                                                                                           |
| **offset**               | none              | Used to paginate through results of more than 1000.  Example: `offset=1000` will fetch the second page of results.                                                                                  |
| **limit** | 1000 | Set a custom limit on the amount of records returned; maximum is 10,000 |

### Responses

**See right**. The API responds with a list of raw records for the particular dataset. The
fields returned will vary per dataset. By default, the response is limited to 1000 results; the absolute maximum is 10,000 results, so set a custom `limit` within those bounds if necessary. These results can still be paginated by using the `offset` parameter.

| **Attribute Name** | **Attribute Description**                              |
| ------------------ | ------------------------------------------------------ |
| **status**         | Indicates query success, can be `ok` or `error`.       |
| **query**          | Shows values used in the query.                        |
| **message**        | Reports errors or warnings (if any).                   |
| **total**          | Total number of records found.                         |
| **_objects_**      | Contains records        |

### Filtering with a Shape Dataset

> Only return events that happened in Logan Square

```
&shape=boundaries_neighborhoods&boundaries_neighborhoods__filter={"op": "eq", "col": "pri_neigh", "val": "Logan Square"}
```

Use the `shape` parameter to specify the name of a [shape dataset](#dataset-types).
Then the endpoint will only return events
that intersect or are contained by shapes in the dataset.
This feature is particularly useful when you apply [filters](#attribute-filtering) to the dataset
by adding a `<shape_dataset_name>__filter` parameter.
For example, you can specify the Chicago neighborhoods shape dataset
and specify a neighborhood in your filter to only fetch events from one neighborhood.

### Response Formatting

By default, this endpoint returns data exactly as it was written in the source dataset.
Behind the scenes, Plenario parses event datasets' latitude and longitude columns
to produce a geometry column that it uses internally.
If you want to get the geometry Plenario has parsed,
use the `data_type` parameter to specify `geojson`.
Plenario will return each record as a GeoJSON point with the parsed latitude and longitude in the `coordinates` object and the original values in the `attributes` object.

## Plenario Datadump

> The `datadump` endpoint is housed on Plenario's backend; access it through [Plenario's Explore feature](http://plenar.io/explore/discover) by selecting a dataset and choosing Download.

Plenario Datadump is a streamlined method of downloading large (above 1000 results) data in both CSV and JSON format. Compare this to the existing `detail` endpoint; due to HTTP timeouts and other limitations, `detail` only returns a pre-specified number of rows, and it was necessary to page through these rows in order to access all the data. Instead, Plenario Datadump takes advantage of the long-running queries enabled by the Jobs Framework to dump the entire query into a file, allowing the query to be downloaded later as one unit. Plenario Datadump is located at the `/datadump` endpoint, and takes the same query parameters as `detail`. `jobs=true` is implied, so you do not need to use it explicitly.
