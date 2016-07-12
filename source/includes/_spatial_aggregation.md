# Spatial Aggregation

## `GET v1/api/grid`

> Heat map of Chicago 311 tree trim requests in 2014

```
http://plenar.io/v1/api/grid/?obs_date__ge=2014-01-01&obs_date__le=2014-12-31dataset_name=311_service_requests_tree_trims
```

```json
{
   "type":"FeatureCollection",
   "features":[
      {
         "geometry":{
            "type":"Polygon",
            "coordinates":[
               [
                  [
                     -87.69635961403962,
                     41.876826253090584
                  ],
                  [
                     -87.69635961403962,
                     41.88132164531356
                  ],
                  [
                     -87.70239743700178,
                     41.88132164531356
                  ],
                  [
                     -87.70239743700178,
                     41.876826253090584
                  ],
                  [
                     -87.69635961403962,
                     41.876826253090584
                  ]
               ]
            ]
         },
         "type":"Feature",
         "properties":{
            "count":7
         }
      }

      ...

   ]
}
~~~~

Create a GeoJSON grid heatmap from an event dataset.
See example grid heatmaps in the [Plenario explorer](http://plenar.io/explore/event/bristol_crime_stats?obs_date__ge=2014-01-31&obs_date__le=2014-05-01).

### Common Query Syntax

|**Parameter Name**  | **Required?** | **Default**
|--------------- | -----------------| ---
| [**dataset_name**](#specifying-a-dataset) | yes | n/a
| [**location_geom__within**](#space-filtering) | no | none
| [**obs_date__ge** & **obs_date__le**](#time-filtering) | no | 90 days ago - today
| [**[dataset_name]__filter**](#attribute-filtering) | no | none

### Query Parameters

All query parameters are optional except for `dataset_name`.

| Parameter Name       | Parameter Default | Parameter Description                                                                                                                                                                                              |
|----------------------|-------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **resolution**         | 500               | Square grid size (in meters).                                                                                                                                                                                      |
| **buffer**               | 100               | If `location_geom__within` is a [GeoJSON LineString](http://geojson.org/geojson-spec.html#linestring), the size of the buffer around that line to query (in meters).                                               |

### Response

**See right**. Plenario will output a GeoJSON with a feature for each grid square containing
a `count` property of the number of observations within that square.

## `GET /v1/api/shapes/<polygon_dataset_name>/<point_dataset_name>/`

> Count the number of historical landmarks in each Chicago neighborhood.

```
http://plenar.io/v1/api/shapes/boundaries_neighborhoods/individual_landmarks?obs_date__ge=1900-09-22
```

```json
{
    "type": "FeatureCollection",
    "features": [
        {
            "geometry": {
                "type": "MultiPolygon",
                "coordinates": [...]
            },
            "type": "Feature",
            "properties": {
                "shape_area": 48492503.1554,
                "pri_neigh": "Grand Boulevard",
                "sec_neigh": "BRONZEVILLE",
                "shape_len": 28196.837157,
                "count": 9
            }
            ...
        },
    ]
}
```

Given an event dataset and a polygon dataset, return a document of all of the
polygons in the polygon dataset together with the counts of the points that
intersect them.
Like `/grid`, except using shapes defined in Plenario as opposed to an automatically generated grid.
Returns GeoJSON by default for easy heatmapping.

### Query Parameters

| Parameter Name        | Parameter Default | Parameter Description                                                                                                                                                                                                                                                               |
|-----------------------|-------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **data_type**             | geojson              | Response data format. Current options are `geojson` and `csv`.                                                                                                                                                                                                              |
