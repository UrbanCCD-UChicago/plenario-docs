#Big Data

## Jobs Framework

> You can invoke the Jobs interface by appending `job=true` to your query.

```
http://plenar.io/v1/api/datasets?job=true
```

>This will return a jobs ticket instead of the usual datasets endpoint. You can then use the link provided by the ticket to access the job status and results.

The Plenario Jobs Framework introduces support for longer-running queries on the Plenario API through an asynchronous ticket-style interface. Now endpoints which support the jobs framework can return a ticket with a link to a page which shows the job query, its status, and its result when ready.

## Plenario Datadump

> The `datadump` endpoint is housed on Plenario's backend; access it through [Plenario's Explore feature](http://plenar.io/explore/discover) by selecting a dataset and choosing Download.

Plenario Datadump is a streamlined method of downloading large (above 1000 results) data in both CSV and JSON format. Compare this to the existing `detail` endpoint; due to HTTP timeouts and other limitations, `detail` only returns a pre-specified number of rows, and it was necessary to page through these rows in order to access all the data. Instead, Plenario Datadump takes advantage of the long-running queries enabled by the Jobs Framework to dump the entire query into a file, allowing the query to be downloaded later as one unit. Plenario Datadump is located at the `/datadump` endpoint, and takes the same query parameters as `detail`. `jobs=true` is implied, so you do not need to use it explicitly.
