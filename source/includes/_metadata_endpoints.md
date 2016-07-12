# Metadata Endpoints

## `GET /v1/api/datasets/`

> List all event datasets and fields within them

```
http://plenar.io/v1/api/datasets/
```

```json
{
    "meta": {
        "status": "ok",
        "query": { },
        "message": [
            [ ]
        ],
        "total": 118
    },
    "objects": [
        {
            "attribution": "Los Angeles Police Department",
            "description": "LAPD Crime and Collision Raw Data for 2013",
            "view_url": "https://data.lacity.org/api/views/iatr-8mqm/rows",
            "columns": [
                {
                    "field_type": "VARCHAR",
                    "field_name": "status"
                },
                {
                    "field_type": "DATE",
                    "field_name": "date_occ"
                },
                ...
            ],
            "last_update": "2015-04-17T19:30:44.922015",
            "obs_from": "2013-01-01",
            "bbox": {
                "type": "Polygon",
                "coordinates": [
                    [
                        [
                            -117.7071,
                            33.706
                        ],
                        ...
                    ]
                ]
            },
            "human_name": "LAPD Crime and Collision Raw Data for 2013",
            "obs_to": "2013-12-31",
            "date_added": "2015-04-17T19:30:44.922015",
            "source_url": "https://data.lacity.org/api/views/iatr-8mqm/rows.csv?accessType=DOWNLOAD",
            "dataset_name": "lapd_crime_and_collision_raw_data_for_2013",
            "update_freq": "yearly"
        },
    ]
}
```

Used to list available event datasets and fetch their metadata.

### Bounding Boxes

Each event dataset's metadata contains a spatial and temporal bounding box,
the smallest timespan and rectangle in which all events in the dataset are contained.
If you apply spatial or temporal filters to this endpoint,
it will only return datasets with events that fell within your filters.
It is often helpful to use these bounding boxes
to narrow down a search over all the datasets in Plenario.

### Common Query Syntax

By default, this endpoint returns every available event dataset.

|**Parameter Name**  | **Required?** | **Default**
|--------------- | -----------------| ---
| [**dataset_name**/**dataset_name__in**](#specifying-a-dataset)  | no | none
| [**location_geom__within**](#space-filtering) | no | none
| [**obs_date__ge** & **obs_date__le**](#time-filtering)| no | none

### Response

One record per event dataset.

| **Attribute Name** 	| **Attribute Description**                                                                   	|
|----------------	|------------------------------------------------------------------------------------------	|
| **attribution**    	| The data provider                                            	|
|**description**| Verbose, official description of the dataset. |
| **view_url**     	| A URL where you can learn more about the dataset.                             	|
|**columns**| Name and type of each column.
| **obs_from**       	| Oldest date available in the dataset.                                                     	|
|**bbox**| The smallest rectangle that contains every event in this dataset. (GeoJSON polygon)
| **human_name**     	| Human-friendly name for the dataset.                                                     	|
| **obs_to**         	| Newest date available in the dataset.                                                    	|
| **source_url** | If available, the URL where the data was originally sourced. |
| **dataset_name**   	| Machine name for the dataset. The name you need to supply in other endpoints.
| **update_freq**    	| How often Plenario checks the source dataset for updates.                                                                        	|

## `GET /v1/api/shapes`

> This query yields information about every shape dataset.

```
http://plenar.io/v1/api/shapes/
```

```json
{
    "meta": {
        "status": "ok",
        "message": ""
    },
    "objects": [
        {
            "attribution": "City of Chicago",
            "description": "Major streets in Chicago. ",
            "view_url": "",
            "num_shapes": 16065,
            "source_url": "https://data.cityofchicago.org/download/ueqs-5wr6/application/zip",
            "bbox": "{\"type\":\"Polygon\",\"coordinates\":[...]}",
            "date_added": "2016-04-20",
            "human_name": "Major Streets",
            "dataset_name": "major_streets",
            "update_freq": "yearly",
            "columns": [{"field_type": "VARCHAR", "field_name": "street_nam"},{"field_type": "VARCHAR", "field_name": "direction"}, ...]
        },
        ...
    ]
}
```

Used to list available shape datasets and fetch their metadata.

### Bounding Boxes

Each shape dataset's metadata contains a spatial bounding box,
the smallest rectangle in which all shapes in the dataset are contained.
If you apply a spatial filter to this endpoint,
it only returns datasets with shapes within the box.
Furthermore, the `num_shapes` response parameter will only
count the shapes that were contained by or intersected your filter.

### Common Query Syntax

Specify `location_geom_within` to only view metadata about shape datasets in a particular area.

| Parameter Name       | Required? | Default                                                           |
|----------------------|-------------------|---------------------------------------------------------------------------------|
| [**location_geom_within**](#space-filtering) | no             | None |

### Responses

One record per shape dataset.

| Attribute Name | Attribute Description                                         |
|----------------|---------------------------------------------------------------|
| **attribution**    	| The data provider                                            	|
| **description**    | Verbose, official description of the dataset.                 |
| **source_url**     | If available, the URL where the data was originally sourced.  |
| **date_added**     | the date the shape dataset was added to Plenario              |
| **human_name**     | a nicer name to refer to the shape dataset in user interfaces |
| **dataset_name**   | the name by which you can query the shape dataset             |
| **update_freq**    | Update frequency                                              |
|**columns**| Name and type of each column.

## `GET /v1/api/fields/<dataset_name>/`

> Field definitions for Chicago crime report dataset

```
http://plenar.io/v1/api/fields/crimes_2001_to_present/
```

_This endpoint is deprecated in favor of `/datasets`._

Given an event dataset name, return all fields and their types.

Gives you a listing of all the fields and their data types for the given dataset.

### Query Parameters

This API endpoint has no query parameters.

### Response

```json
{
    "meta": {
        "status": "ok",
        "query": { },
        "message": [
            [ ]
        ],
        "total": 1
    },
    "objects": [
        {
            "field_type": "INTEGER",
            "field_name": "y_coordinate"
        },
        {
            "field_type": "INTEGER",
            "field_name": "year"
        },
        {
            "field_type": "BOOLEAN",
            "field_name": "domestic"
        },
        ...
    ]
}
```

| Attribute Name | Attribute Description                                                                                                              |
|----------------|------------------------------------------------------------------------------------------------------------------------------------|
| **field_type**     | [Postgres data type](https://www.postgresql.org/docs/9.3/static/datatype.html) for given field.                                                                                                |
| **field_name**     | Name of database field. Useful for performing additional queries using the `/v1/api/detail-aggregate/` and `/v1/api/detail/` endpoints. |
