# Providers

This section is specifically for those who are providers of sensor network
data to Plenario. It assumes that you have received an administrative
account on [apiary](LINK).


## About

We ensure Plenario does not ingest junk data by filtering the incoming
stream. This filtering is based on metadata descriptions of sensor
networks which Plenario utilizes to store and query observations in a
meaningful way.

If you would rather use the [browsable api](LINK), it's still recommended
to skim this section just to see an example of what valid data looks
like, as well as how all the pieces fit together.


## Adding a network

**`POST /api/networks/?format=vnd.api%2Bjson`**

```bash
curl "http://localhost:8000/api/networks/?format=vnd.api%2Bjson" \
  --user username:password \
  --data "name=mysensornetwork&info={}"
```

Plenario first needs to know what [network](#sensor-networks) a node will
belong to. If you intend to add nodes to an existing network, you can
skip this section.

| **Parameter** | **Description**                                              |
| ------------- | ------------------------------------------------------------ |
| name          | Unique name for a network                                    |
| info          | Miscellaneous metadata about the network in the form of JSON |


## Adding a feature

**`POST /api/features/?format=vnd.api%2Bjson`**

```bash
curl "http://localhost:8000/api/features/?format=vnd.api%2Bjson" \
  --user username:password \
  --data 'name=sound&observed_properties=[
      {"name": "intensity", "type": "double precision"},
      {"name": "duration", "type": "double precision"}
    ]'
```

If any of your node's sensors are reporting something we do not have a
[feature](#features-of-interest) for, you can add it like this. Here we've
created a hypothetical feature for sensors which report on 'sound' as
'intensity' and 'duration'.

| **Parameter**       | **Description**                                              |
| ------------------- | ------------------------------------------------------------ |
| name                | Unique name for a feature                                    |
| observed_properties | List of json objects containing the 'name' and 'type' keys   |

Values for the type key must be an accepted [Redshift data type](http://docs.aws.amazon.com/redshift/latest/dg/c_Supported_data_types.html).


## Adding a sensor

**`POST /api/sensors/?format=vnd.api%2Bjson`**

```bash
curl "http://localhost:8000/api/sensors/?format=vnd.api%2Bjson" \
  --user username:password \
  --data 'name=snd3000&observed_properties={
      "dur": "sound.duration",
      "val": "sound.intensity"
    }&info={}'
```

This adds a sound [sensor](#sensors) that reports values as 'dur' and 'val'.
Given that we have a sound feature that we created earlier, we go ahead
and map these values to that feature. Now this sensor's sound values can
be queried alongside any other sensor that also reports sound values.

| **Parameter**       | **Description**                                       |
| ------------------- | ----------------------------------------------------- |
| name                | Unique name for a feature                             |
| observed_properties | Map of reported sensor values to feature properties   |


## Adding a node

**`POST /api/nodes/?format=vnd.api%2Bjson`**

```bash
curl "http://localhost:8000/api/nodes/?format=vnd.api%2Bjson" \
  --user username:password \
  --data "id=mynode2&sensor_network=plenario_development&info={}\
    &sensors=snd3000&sensors=tmp112&location={\
      \"type\": \"Point\",\
      \"coordinates\": [-104.7858045, 39.8087029]\
    }"
```

Finally we can register [nodes](#nodes). Here we've created a node that has both
a sound and a temperature sensor.

| **Parameter**  | **Description**                                       |
| -------------- | ----------------------------------------------------- |
| id             | Unique name for a feature                             |
| sensor_network | Name of the network this node belongs to              |
| info           | Miscellaneous information about the node              |
| sensors        | Name of a sensor contained by this node               |
| location       | Physical location of the node as GeoJSON or WKT       |


## Reporting an observation

```json
{
  "network": "mysensornetwork",
  "meta_id": "version_id",
  "node_id": "mynodeid",
  "sensor": "snd3000",
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
you send to its observation stream. Observations are pushed
using any of Amazon's Kinesis client libraries. An observation represents
a single report made by a sensor. They must be formatted as
json, with the observation values matching the observation property
map defined for that sensor.
