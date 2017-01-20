## Query Syntax

## -- Time Filtering

> Query over 2015

```
&obs_date__ge=2015-1-1&obs_date__le=2015-12-31
```

For endpoints that accept temporal filters, you can provide the query parameters `obs_date__ge` (observation date greater than or equal to) and `obs_date__le` (less than or equal to). Both must be formatted as **YYYY-MM-DD**.

|parameter|description|default
|---|---|---|
|`obs_date__ge`|start of date range|90 days ago|
|`obs_date__le`|end of date range|today|

## -- Space Filtering

> Query over the author's block

```
location_geom__within={"type":"Polygon","coordinates":[[[-87.58695602416992,41.79224063145134],[-87.58695602416992,41.7996633276003],[-87.5745964050293,41.7996633276003],[-87.5745964050293,41.79224063145134],[-87.58695602416992,41.79224063145134]]]}
```

For endpoints that accept spatial filters, you can provide the `location_geom__within` parameter.
It must be a URL encoded [GeoJSON](http://geojson.org/) polygon or multipolygon.

<aside class="notice">
    This is a nice site to <a href=http://geojson.org/>generate geojson</a>.
</aside>

## -- Specifying a Dataset

Many endpoints require you to specify a dataset to query.
In that case, specify the `dataset_name` query parameter.
If you can specify more than one dataset, you can use `dataset_name__in`
to provide a list of dataset names.

## -- Asynchronous Jobs

> You can invoke the Jobs interface by appending `job=true` to your query.

```
http://plenar.io/v1/api/datasets?job=true
```

>This will return a jobs ticket instead of the usual datasets endpoint. You can then use the link provided by the ticket to access the job status and results.

You can add `job=true` to launch a query as an asynchronous job.
Plenario will run your query as a background task
and give you a ticket.
You can check on your job's progress at `/v1/api/jobs/{ticket}`.
When the job is complete, `/v1/api/jobs/{ticket}` will contain the result of your job in the `result` field.

This can be useful when you want to launch a very large query against Plenario, like aggregating a large event dataset over a large shape dataset.
It might take too long for Plenario to process a massive request in a synchronous HTTP call.
If you notice that a call is timing out,
try running it as an asynchronous job instead.

## -- Attribute Operators

> Use the "columns" information from each dataset's metadata
> to find the name


```json
...
"columns": [
    {
        "field_type": "DOUBLE PRECISION",
        "field_name": "shape_area"
    },
    {
        "field_type": "VARCHAR",
        "field_name": "pri_neigh"
    },
    {
        "field_type": "VARCHAR",
        "field_name": "sec_neigh"
    },
    {
        "field_type": "DOUBLE PRECISION",
        "field_name": "shape_len"
    }

], ...
```

When you make a query against a specific dataset,
you can filter against that dataset's attributes.
You can find out the name and types of each dataset's attributes
through the event and shape metadata endpoints. For a detailed list of all `field_type` options, consult the table at the bottom of [this PostgreSQL documentation](https://www.postgresql.org/docs/9.3/static/datatype.html).

You can use the following binary operators:

> LIKE examples:
>
> "%rat%" matches "rat poisoning" and "crater".
> "jo_" matches "joe" and "job" but not "josephine"

|operator|description|
|---|---|
|`eq`|equal to (==)|
|`gt`|greater than (>)|
|`ge`|greater than or equal to (>=)|
|`lt`|less than (<)|
|`le`|less than or equal to (<=)|
|`ne`|not equal (!=)|
|`in`|within a list of provided values like `0560,0110`|
|`like`|Match string pattern|
|`ilike`|Match string pattern, case insensitive|


<aside class="notice">
    <code>like</code> and <code>ilike</code> match patterns using the SQL LIKE operator. That lets you use <code>%</code> and <code>_</code> as wildcard characters. <code>%</code> matches a string of any length, while <code>_</code> matches exactly one character.
</aside>

## -- Attribute Filtering

> Filter Chicago Department of Health complaints to only return records with the word "asbestos" in the complaint description

```json
{"op":"ilike", "col":"complaint_detail", "val":"%asbestos%"}
```

A single conditions that applies to one feature can be expressed in the following syntax: `{"op":"<operator>", "prop":"<property>", "val":"<target_value>"}`

> Filter Chicago crimes dataset to only include narcotics crimes that took place on a street or sidewalk

```json
{"op": "and", "val": [
    {"op": "eq", "col": "primary_type", "val": "NARCOTICS"},
    {"op":"or", "val": [
        {"op":"eq","col":"location_description","val":"STREET"},
        {"op":"eq","col":"location_description","val":"SIDEWALK"}
        ]}
    ]
}
```

You can also apply ANDs and ORs to conditions with the following syntax:
`{"op": <"and" or "or">, "val": [<list of conditions>]}`. You can nest conditions arbitrarily deep.

>Applying filters to the Chicago crimes and Chicago neighborhoods datasets to return homicides from Logan Square and Humboldt Park

```
plenar.io/v1/api/detail/?dataset_name=crimes_2001_to_present&crimes_2001_to_present__filter={"op": "eq", "col": "primary_type", "val": "HOMICIDE"}&shape=boundaries_neighborhoods&boundaries_neighborhoods__filter={"op": "in", "col": "pri_neigh", "val": "Logan Square,Humboldt Park"}
```

You can specify filters as query parameters in your GET requests as `<dataset_name>__filter={filter}`.
You need to specify which dataset a filter applies to because a single query might include more than one dataset.

## -- Attribute Filtering (Old Syntax)

_This syntax is deprecated._

```
pri_neigh__in=%3DLogan%20Square%2CHumboldt%20Park
```

The original method for applying conditions to attributes of a dataset
is to provide one query parameter for each condition in the form of
`[col]__[op]=[val]`.
The trouble with this syntax is that you can't specify
whether you want to combine the conditions with ANDs or ORs.
So this syntax assumes you just want to AND things together.
You also can't specify which dataset a condition should apply to.

<aside class="warning">
    You cannot mix the new and old style attribute filtering syntaxes.
    If you do, Plenario will ignore the old style parameters and only use the new style.
</aside>
