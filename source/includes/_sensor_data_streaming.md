## Streaming Data Queries

> **Data Event**

```json
{
    "datetime": "2017-03-03T22:38:27", 
    "feature": "temperature", 
    "meta_id": 0.0, 
    "node": "0000001e0610ba72", 
    "results": {
        "internal_temperature": null, 
        "temperature": 23.15
    }, 
    "sensor": "tmp112"
}
```

Plenario uses the [pusher](https://github.com/socketio/socket.io-protocol) 
service to provide near real-time streaming of sensor data. A user may open 
sockets which specify the observations they're interested in. Plenario will 
push  observations that correspond their socket. 

Any invalid parameters and other errors cause the connection attempt to be
rejected with a `403`. If all parameters are valid and no errors occur, the 
client will begin receiving `data` events from the socket.

<aside class="info">
    If you're unsure of whether or not your parameters are allowed, try 
    validating them with the <a href="/#validation">check</a> endpoint first.
</aside>

## -- Connecting to a socket

> **Node.js** ([pusher-client](https://www.npmjs.com/package/pusher-client))

> Subscribe to all nodes in the array_of_things_chicago network

```javascript
var Pusher = require('pusher-client');

var pusher = new Pusher(PLENARIO_KEY, { 
    authEndpoint: PLENARIO_AUTH 
});

var channel = pusher.subscribe('private-array_of_things_chicago');

channel.bind('data', function(e) {
    console.log(e);
});
```

| **Variable**      | **Value**                                                          |
| ----------------- | ------------------------------------------------------------------ |
| **PLENARIO_KEY**  | c6851f0950381b69a136                                               |
| **PLENARIO_AUTH** | https://gm76b1jzz1.execute-api.us-east-1.amazonaws.com/development |

To get started with streaming, install the client library for your
respective language and copy over the client code. Plug in the values
provided above for the `PLENARIO_KEY` and `PLENARIO_AUTH` variables.

<aside class="info">
    Don't see your favorite language? There are alot more client libraries
    available <a href="https://pusher.com/docs/libraries">here</a>.
</aside>

## -- Filtering socket observations

> Allow all observations in the array_of_things_chicago network

```
private-array_of_things_chicago
```

> Allow node 0000001e0610ba72 observations in the array_of_things_chicago_network

```
private-array_of_things_chicago;0000001e0610ba72
```

> Allow tmp112 sensor observations on node 0000001e0610b9e7 in the array_of_things_chicago_network

```
private-array_of_things_chicago;0000001e0610b9e7;tmp112
```

> Allow atmospheric pressure observations from the bmp180 sensor in the array_of_things_chicago_network

```
private-array_of_things_chicago;0000001e0610b9e7;bmp180;atmospheric_pressure
```

> Allow temperature observations from ANY sensor on the 0000001e0610b9e7 node
in the array_of_things_chicago_network (NOTE the ';;')

```
private-array_of_things_chicago;0000001e0610b9e7;;temperature
```

> Allow temperature observations from ANY sensor on ANY node
in the array_of_things_chicago_network (NOTE the ';;'s)

```
private-array_of_things_chicago;;;temperature
```

> Allow ANY observation from the bmp180 sensor on ANY node
in the array_of_things_chicago_network (NOTE the ';;')

```
private-array_of_things_chicago;;bmp180
```

Observations are filtered by the name of your channel. You can filter
on any combination of sensor network aspects. The channel name format
is as follows: `private-<NETWORK>;<NODE>;<SENSOR>;<FEATURE>` where network 
is mandatory. If the channel name is improperly formatted or contains
values which do not exist - a `403` will be returned and you will not
be subscribed.