# Sensor Data Streaming

> `data` event format

```json
{ 
  "node_id": "00A",
  "meta_id": 11,
  "datetime": "2016-08-05T00:00:08.246000",
  "sensor": "HTU21D",
  "feature_of_interest": "temperature",
  "results": {     
      "temperature": 37.90
  } 
}
```

Plenary utilizes the [socket.io protocol](https://github.com/socketio/socket.io-protocol) in order to provide near real time streaming of sensor data. A user may specify lists of nodes, features_of_interest, and/or sensors. They will then only be streamed data that satisfies these parameters. If no parameters are specified, the default is to stream all data from the array_of_things network.

Any invalid parameters and other errors will be emitted as JSON `internal_error` events and no connection will be created. If all parameters are valid and no errors occur, the client will begin receiving `data` events from the socket.

> Start a socket client that will print any `internal_error` and all temperature `data` events from the HTU21D sensor on nodes 00A and 00B

> **Sample Node.js client** using the [socket.io-client](http://socket.io/docs/) module

```javascript
var socket = require('socket.io-client')('http://streaming.plenar.io?' +
    'sensor_network=array_of_things&' +
    'features_of_interest=temperature&' +
    'nodes=00A,00B&' +
    'sensors=HTU21D');

socket.on('data', function (data) {
    console.log(data);
});
socket.on('internal_error', function (err) {
    console.log(err);
});
```

> **Sample Python client** using the [socketIO-client](https://pypi.python.org/pypi/socketIO-client) package

```python
import json
from socketIO_client import SocketIO

socketIO = SocketIO("streaming.plenar.io", params={
    'sensor_network': 'array_of_things',
    'features_of_interest': 'temperature',
    'sensors': ['HTU21D'],
    'nodes': ['00A', '00B']})

def on_data(data):
    print json.dumps(data)

def on_error(err):
    print json.dumps(err)

socketIO.on('data', on_data)
socketIO.on('internal_error', on_error)
socketIO.wait()
```

### Common Query Syntax

|**Parameter Name**  | **Required?** | **Default**
|--------------- | -----------------| ---
| [**nodes**](#nodes) | no | all nodes in network
| [**sensors**](#sensors) | no | all sensors in network
| [**features_of_interest**](#features-of-interest) | no | all features reported on by network
