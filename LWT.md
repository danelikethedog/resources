# LWT

## How Does LWT Fit In?

* It seems like the frontend has support for the LWT, but the backend doesn't.
  * [Link to code](https://github.com/Azure/iotedge-broker/blob/fd1cf35351dd4bb5fabe4557521942cb0cc515dd/core/server/src/dmqtt/chain_replication_manager/dmqtt_chain_replication/connect.rs#L53)
* `PatchInner::Connect` doesn't have support.
* `Session` however does have support. There has to be a bridge between the `packet_info: &DecapsulatedConnect<BS>` and the `Session`. This is done in the `connect` function in `chain_replication_manager/dmqtt_chain_replication/connect.rs`.


## MQTT Spec

### Will Flag

* If the Will Flag is set to 1 this indicates that, if the Connect request is accepted, a Will Message MUST be stored on the Server and associated with the Network Connection. The Will Message MUST be published when the Network Connection is subsequently closed unless the Will Message has been deleted by the Server on receipt of a DISCONNECT Packet [MQTT-3.1.2-8].
* If the Will Flag is set to 1, the Will QoS and Will Retain fields in the Connect Flags will be used by the Server, and the Will Topic and Will Message fields MUST be present in the payload [MQTT-3.1.2-9].
* The Will Message MUST be removed from the stored Session state in the Server once it has been published or the Server has received a DISCONNECT packet from the Client [MQTT-3.1.2-10].
* If the Will Flag is set to 0 the Will QoS and Will Retain fields in the Connect Flags MUST be set to zero and the Will Topic and Will Message fields MUST NOT be present in the payload [MQTT-3.1.2-11].
* If the Will Flag is set to 0, a Will Message MUST NOT be published when this Network Connection ends [MQTT-3.1.2-12].

### Will QoS

* If the Will Flag is set to 0, then the Will QoS MUST be set to 0 (0x00) [MQTT-3.1.2-13].
* If the Will Flag is set to 1, the value of Will QoS can be 0 (0x00), 1 (0x01), or 2 (0x02). It MUST NOT be 3 (0x03) [MQTT-3.1.2-14].

### Will Retain

* If the Will Flag is set to 0, the Will Retain MUST be set to 0 (0x00) [MQTT-3.1.2-15].
* If the Will Flag is set to 1, the Will Retain can be set to 0 (0x00) or 1 (0x01) [MQTT-3.1.2-16].
