# Concepts

There are a few basic concepts to keep in mind
as you work with the Plenario API.

## Space and time

> Filtering datasets by political boundaries

```
plenar.io/v1/api/detail/?dataset_name=crimes_2001_to_present&shape=boundaries_neighborhoods&boundaries_neighborhoods__filter={"op": "eq", "col": "pri_neigh", "val": "Logan Square"}
plenar.io/v1/api/detail/?dataset_name=food_inspections&shape=boundaries_neighborhoods&boundaries_neighborhoods__filter={"op": "eq", "col": "pri_neigh", "val": "Logan Square"}
```

All data in Plenario is spatial.
If it's in Plenario, you can put it on a map.
Because Plenario makes sure all of its data lives on the same map,
you can apply common spatial operations to different datasets.
For example, you can take two datasets - crimes and food inspections in Chicago - and extract only the events that happened in the neighborhood of Logan Square.

> Creating timeseries aggregated by day

```
plenar.io/v1/api/timeseries/?obs_date__ge=2015-10-12&obs_date__le=2015-11-12&dataset_name__in=crimes_2001_to_present,food_inspections&agg=day
```

Many - but not all - of Plenario's datasets also have a temporal component.
Similarly, Plenario makes sure those datasets live on the same timeline.
That makes it easy to aggregate datasets up to the same temporal unit and compare trends.

## Dataset Types

Plenario currently supports two types of datasets:
**event** datasets and **shape** datasets.
The type of a dataset lets you know what kind of
spatial and temporal information it is guaranteed
to have.

> See all event datasets in Plenario

```
plenar.io/v1/api/datasets
```

### Events

All records in event datasets have a temporal component (a point in time)
and a spatial component (a point in space).
For example, every record in the Chicago food inspections dataset represents one inspection.
An inspector walked into a restaurant - represented as a latitude longitude point - at a time - represented as a timestamp.

Of course, a point and a timestamp are just the _minimum_
information event datasets need.
For example, the food inspections dataset also has
a column with the name of the restaurant that was inspected
and another column for whether the restaurant passed the inspection.

> See all shape datasets in Plenario

```
plenar.io/v1/api/shapes
```

### Shapes

All records in shape datasets have a spatial component.
While events are always tied to a _point_ in space,
shape datasets can have different spatial types,
like polygons to represent neighborhood boundaries
or line segments to represent bike routes.
Shape datasets do not have a temporal component.

Shape datasets usually have extra columns that
aren't spatial.
For example, the shape dataset with the borders of
Chicago's neighborhoods has a column with the
neighborhoods' names.

> See all NOAA weather stations in Plenario

```
plenar.io/v1/api/weather-stations
```

### Datasets That Don't Fit

You can probably think of examples of spatial
datasets that don't fit the above types.
What about neighborhood unemployment rates?
That has a static spatial component plus a timeseries.
Or urban bike share trips?
A single record should have a point in space and a timestamp for both the origin and the destination.

We can't support these fully _yet_.
We're working to expand the types of datasets Plenario can handle.
For example, you'll find an API for querying NOAA weather reports in a [later section](#weather).
We're currently building more general support for urban sensor networks - static locations with many observations.
