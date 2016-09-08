# Sensor Network Concepts

## Sensor Networks

The sensor network is the top-level unit, representing a collection of
nodes managed by the same organization.
## Nodes

A node is a collection of sensors with the same location. A node belongs 
to a single sensor network. A node ID is unique within each network but 
not globally unique between networks.
## Sensors

A sensor is a physical device measuring some observable property. Each 
node may contain many sensors models. Many nodes from different networks may 
contain the same sensor models.
## Features of Interest

A feature of interest is a feature of the physical world that sensors 
measure. These features of interest apply to all nodes and sensors within 
all networks. This allows queries against heterogeneous sensors that are 
measuring the same physical thing. For example, even if some nodes have 
temperature sensor A and some have temperature sensor B, the network 
maintainer can say these both apply to the temperature feature as long as 
they both report values in the correct units on the same concept of 
temperature.

A single sensor may report values for multiple properties from multiple 
features of interest. A sensor may also report only a subset of the 
observed properties of a feature of interest.

## Metadata Model

![metadatamodel](https://cloud.githubusercontent.com/assets/19937363/18366166/3a8cc5e4-75db-11e6-83ea-027afaef6f0e.png)

## Sensor Network Time Filtering

> Query over January 2015

```
&start_datetime=2015-1-1T00:00:00.000&end_datetime=2015-2-1T00:00:00.000
```

For sensor networks, you can provide the query parameters `start_datetime` (observation date greater than or equal to) and `end_datetime` (less than or equal to). Both must be formatted as **YYYY-MM-DDThh:mm:ss.sss**.

|parameter|description|default
|---|---|---|
|`start_datetime `|start of date range|90 days ago|
|`end_datetime `|end of date range|now|

