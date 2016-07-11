# Shape Data Queries

## `GET /v1/api/shapes/<dataset_name>`

> ### Example Queries

```
http://plenar.io/v1/api/shapes/pedestrian_streets?
data_type=json
```

> Get a GeoJSON document with Chicago's streets that are reserved for pedestrian traffic.

```
http://plenar.io/v1/api/shapes/pedestrian_streets?
data_type=json&pedestrian_streets__filter={
"op": "eq", "col": "name", "val": "PEDESTRIAN STREET RETAIL"}
```

> Limit that document to only streets marked as "PEDESTRIAN STREET RETAIL".


> ### Example Response

> Some GeoJSON should go here.

Request shapes from a shape dataset.
Returns all records in the dataset by default.
You can also apply a spatial filter or filter on attributes to request a subset.

### Common Query Syntax

|**Parameter Name**  | **Required?** | **Default**
|--------------- | -----------------| ---
|**location_geom__within** | no | none
|**[dataset_name]__filter**| no | none

### Endpoint-Specific Parameters

| Parameter Name       | Parameter Default | Parameter Description                                                           |
|----------------------|-------------------|---------------------------------------------------------------------------------|
| **data_type**            | `json`            | `json` for GeoJSON, `shapefile` for ESRI Shapefile, or `kml` for KML|
