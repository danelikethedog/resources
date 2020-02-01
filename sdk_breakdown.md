# SDK Breakdown

## C2D

Relatively straight forward. The device will connect to an endpoint that the service will send messages to.

Subscribed to topic: `devices/<device_id>/messages/devicebound/#`

In place of the wildcard are the following properties

`$.mid=<message id>`

`$.to=<topic sent to>`

The property bag is appended to the end of those properties.


## Twin

Twin from an implementation perspective is a subscription to the following topics from
the device

`$iothub/twin/res/#` : for responses from the hub concerning ops/requests from the device

`$iothub/twin/PATCH/properties/desired/#`: for updates to the twin

All request for the twin feature are done by publishing certain _requests_ to the
hub from the device based on HTTP CRUD ops. The following are valid topics to send to:

`$iothub/twin/GET/?$rid=<request id>`: to get the whole twin document

`$iothub/twin/PATCH/properties/reported/?$rid=<request id>`: update the reported properties of the twin document. The payload to update the props might be the following
```javascript
{
    "prop": "new value"
}
```
where prop is some desired/reported property.


