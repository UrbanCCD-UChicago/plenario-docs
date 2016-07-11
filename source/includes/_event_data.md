# Event Data Endpoints

## `GET /v1/api/detail`

> ### Example Query

```
http://plenar.io/v1/api/detail/?dataset_name=crimes_2001_
to_present&obs_date__ge=2016-5-1&iucr=0110
```

> A list of all Chicago homicides (IUCR code 0110) since May 1<sup>st</sup>, 2016

> ### Example Response

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

Query a particular dataset and get back the raw individual records.

### Common Query Syntax

|**Parameter Name**  | **Required?** | **Default**
|--------------- | -----------------| ---
|**dataset_name** | yes | n/a
|**location_geom__within** | no | none
|**obs_date__ge** & **obs_date__le**| no | 90 days ago - today
|**[dataset_name]__filter** or **[dataset_name]__[operator]**| no | none

### Query Parameters

All query parameters are optional except for `dataset_name`.

| Parameter Name       | Parameter Default | Parameter Description                                                                                                                                                                                              |
|----------------------|-------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **data_type**            | json              | Response data format. Current options are `json`, `geojson` and `csv`                                                                                                                                                         |
| **geom**                 | none              | Join to a shape dataset; columns in the shape dataset can also be used as filters with the `[dataset_field]*` parameter                                                                                            |
| **offset**               | none              | Used to paginate through results of more than 1000.  Example: `offset=1000` will fetch the second page of results.                                                                                  |

### Responses

**See right**. The API responds with a list of raw records for the particular dataset. The
fields returned will vary per dataset. Response is limited to 1000 results,
which can be paginated by using the `offset` parameter.

| **Attribute Name** | **Attribute Description**                              |
| ------------------ | ------------------------------------------------------ |
| **_meta_**         |                                                        |
| **status**         | Indicates query success, can be `ok` or `error`.       |
| **query**          | Shows values used in the query.                        |
| **message**        | Reports errors or warnings (if any).                   |
| **total**          | Total number of records found.                         |
| **_objects_**      | Contains row values of raw dataset information.        |
