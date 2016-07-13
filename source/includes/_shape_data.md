# Shape Data Queries

## `GET /v1/api/shapes/<dataset_name>`

> ### Example Queries

> Get a GeoJSON document with Chicago's streets that are reserved for pedestrian traffic.
> Filter so that only those set aside for retail are included.

```
http://plenar.io/v1/api/shapes/pedestrian_streets/?data_type=json&pedestrian_streets__filter={"op": "eq", "col": "name", "val": "PEDESTRIAN STREET RETAIL"}
```

Request shapes from a [shape dataset](#dataset-types).
Returns all records in the dataset by default.
You can also apply a spatial filter or [filter on attributes](#attribute-filtering) to request a subset.

### Common Query Syntax

|**Parameter Name**  | **Required?** | **Default**
|--------------- | -----------------| ---
| [**location_geom__within**](#space-filtering) | no | none
| [**[dataset_name]__filter**](#specifying-a-dataset) | no | none

### Endpoint-Specific Parameters

| Parameter Name       | Parameter Default | Parameter Description                                                           |
|----------------------|-------------------|---------------------------------------------------------------------------------|
| **data_type**            | `json`            | `json` for GeoJSON, `shapefile` for ESRI Shapefile, or `kml` for KML|
