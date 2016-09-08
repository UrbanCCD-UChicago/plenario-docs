---
title: Plenario API Documentation

language_tabs:
  - html: Examples

toc_footers:
  - <a href='http://www.urbanccd.org/#urbanccd'>UrbanCCD</a>
  - <a href='http://www.uchicago.edu/'>University of Chicago</a>
  - <a href='http://plenar.io/'>Plenario</a>
  - Docs powered by <a href='https://github.com/tripit/slate'>Slate</a>

includes:
  - plenario
  - concepts
  - query_syntax
  - response_format
  - metadata_endpoints
  - event_data
  - shape_data
  - temporal_aggregation
  - spatial_aggregation
  - sensor_network_concepts
  - sensor_network_metadata
  - sensor_network_data
  - sensor_data_streaming
  - weather

search: true
---

# Welcome!

>  In this rightmost column, you'll see example API calls and responses.

Plenario is a centralized hub for open datasets from around the world, ready to search and download.

<aside class=info>
    If you'd rather access Plenario’s data through a visual interface,
    check out the <a href="http://plenar.io/explore/discover">Data
    Explorer</a>.
</aside>

The Plenario API can be used to perform custom geospatial and temporal queries.

A few things to know:

* All API calls should be made with `HTTP GET`
* All methods are accessed via: `http://plenar.io/v1/api/<ENDPOINT>`
* All API responses default to [JSON format](http://www.json.org/), but you can
specify alternate formats specific to some endpoints.
