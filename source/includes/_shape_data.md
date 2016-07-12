# Shape Data Queries

## `GET /v1/api/shapes/<dataset_name>`

> Select Chicago's streets that are reserved for pedestrian traffic, then filter so that only those set aside for retail are included.

```
http://plenar.io/v1/api/shapes/pedestrian_streets/?data_type=json&pedestrian_streets__filter={"op": "eq", "col": "name", "val": "PEDESTRIAN STREET RETAIL"}
```

> This endpoint's response will be sent directly to the user as a GeoJSON document.

Request shapes from a shape dataset.
Returns all records in the dataset by default.
You can also apply a spatial filter or filter on attributes to request a subset.

### Common Query Syntax

|**Parameter Name**  | **Required?** | **Default**
|--------------- | -----------------| ---
| [**location_geom__within**](#space-filtering) | no | none
| [**[dataset_name]__filter**](#specifying-a-dataset) | no | none

### Endpoint-Specific Parameters

| Parameter Name       | Parameter Default | Parameter Description                                                           |
|----------------------|-------------------|---------------------------------------------------------------------------------|
| **data_type**            | `json`            | `json` for GeoJSON, `shapefile` for ESRI Shapefile, or `kml` for KML|
