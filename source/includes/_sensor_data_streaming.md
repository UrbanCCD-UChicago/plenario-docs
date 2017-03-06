## Streaming Data Queries

> **Data Event Format**

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
sockets which specify the node they're interested in. Plenario will push 
observations that correspond their socket. If no parameters are specified, 
the default is to stream all data from the array_of_things_chicago network.

Any invalid parameters and other errors cause the connection attempt to be
rejected. If all parameters are valid and no errors occur, the client will 
begin receiving `data` events from the socket.

### Required Variables

| **Variable**      | **Value**                                                          |
| ----------------- | ------------------------------------------------------------------ |
| **PLENARIO_KEY**  | c6851f0950381b69a136                                               |
| **PLENARIO_AUTH** | https://gm76b1jzz1.execute-api.us-east-1.amazonaws.com/development |


> **Node.js** ([pusher-client](https://www.npmjs.com/package/pusher-client))

> Subscribe to all nodes in the array_of_things_chicago network

```javascript
var Pusher = require('pusher-client');

var pusher = new Pusher(PLENARIO_KEY, { 
    authEndpoint: PLENARIO_AUTH 
});

var channel = pusher.subscribe('private-all');

channel.bind('data', function(e) {
    console.log(e);
});
```

**PLENARIO_AUTH** 

Given that pusher allows channels to be generated on the fly for subscribers,
we need a way to check the validity of a channel during attempted subscription.
That is what the `PLENARIO_AUTH` endpoint is for, it allows us to intercept and
approve subscriptions.

**"private-" prefix**

This is another aspect of how pusher works, the authentication request is only
made for channels which start with the prefix "private-". Because of this
behaviour **we do not publish on any channels not prefixed with "private-"**.


> Subscribe to node 0000001e0610b9e7 in the array_of_things_chicago network

```javascript
var Pusher = require('pusher-client');

var pusher = new Pusher(PLENARIO_KEY, { 
    authEndpoint: PLENARIO_AUTH 
});

var channel = pusher.subscribe('private-0000001e0610b9e7');

channel.bind('data', function(e) {
    console.log(e);
});
```

**Don't see your favorite language?**

Pusher has support for a whole lot more than the examples we've listed. Take
a look [here](https://pusher.com/docs/libraries) - don't forget that the
channels are private!