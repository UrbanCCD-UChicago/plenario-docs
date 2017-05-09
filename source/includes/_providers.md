# Providers

This section is specifically for those who are providers of sensor network
data to Plenario. It assumes that you have received an administrative account on [apiary](LINK).


## About

We ensure Plenario does not ingest junk data by filtering out incoming
stream data. This filtering is based on metadata descriptions of sensor
networks. Plenario utilizes this metadata in order to store and query
sensor observations in a meaningful way.

If you would rather use the browsable api, it's still recommended to skim
this section just to see an example of what valid data looks like, as well
as how all the pieces fit together.


## Adding a network

```bash
curl "http://localhost:8000/api/networks/?format=vnd.api%2Bjson" \
  --user username:password \
  --data "name=mysensornetwork&info={}"
```

Plenario first needs to know what network a sensor will belong to. This is
the highest level of organization that describes a collection of nodes. If
you intend to add nodes to an existing network, you can skip this section.

Networks let us namespace all of the nodes you intend to register.
The info field is simply miscellaneous metadata about the network
that you can add, but is optional.


## Adding a feature

```bash
curl "http://localhost:8000/api/features/?format=vnd.api%2Bjson" \
  --user username:password \
  --data 'name=motion&observed_properties=[
      {"name": "intensity", "type": "double precision"},
      {"name": "duration", "type": "double precision"}
    ]'
```

If any of your sensors are reporting something we do not have a feature
for, you can add it like this

Here we've created a hypothetical feature for sensors which report on
'sound' as 'intensity' and 'duration'.

VALID TYPES


## Adding a sensor

```bash
curl "http://localhost:8000/api/sensors/?format=vnd.api%2Bjson" \
  --user username:password \
  --data 'name=mot3000&observed_properties={
      "dur": "motion.duration",
      "val": "motion.intensity"
    }&info={}'
```

Here we've added a motion sensor that reports values as 'dur' and 'val'.
Given that we have a motion feature that we created earlier, we go ahead
and map these values to that feature. Now this sensor's motion values can
be queried alongside any other sensor that also reports motion values.


## Adding a node

Finally we can register nodes. Nodes are either a single sensor, or a
collection of sensors at some physical location.

```bash
curl "http://localhost:8000/api/nodes/?format=vnd.api%2Bjson" \
  --user username:password \
  --data "id=mynode2&sensor_network=plenario_development&info={}\
    &sensors=tmp112&sensors=tmp421&location={\
      \"type\": \"Point\",\
      \"coordinates\": [-104.7858045, 39.8087029]\
    }"
```

The location argument is represented by geojson, WKT EWKT, or HEXEWKB


## Reporting an observation

```json
{
  "network": "mysensornetwork",
  "meta_id": "version_id",
  "node_id": "mynodeid",
  "sensor": "mysensor",
  "datetime": "2017-01-01T00:00:00",
  "data": {
    "dur": 3.0,
    "val": 15.7
  }
}
```

> **Python 3**

```python
import boto3

kinesis_client = boto3.client(
    'kinesis',
    aws_access_key_id=AWS_ACCESS_KEY,
    aws_secret_access_key=AWS_SECRET_KEY,
    region_name='us-east-1',
)

kinesis_client.put_record(**{
    'StreamName': 'ObservationStream',
    'PartitionKey': 'arbitrary',
    'Data': json.dumps(payload)
})
```

With all this metadata in place, Plenario can begin to ingest data that
you send to its observation stream. Observations can be pushed
using any of Amazon's Kinesis client libraries. They must be formatted as
json, with the sensor observation values matching the observation property
map defined for that sensor.
