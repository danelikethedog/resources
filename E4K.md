# E4K

## Parts to the Broker

* Operator
  * Orchestrates the broker
* Connector
  * Connects the broker to the cloud
* Auth
  * Authenticates the broker
* Bridge
  * Bridges the broker to other brokers
* dMQTT
  * The broker itself

## Running the Broker

You can run the built broker within an image using `cargo`, `docker`, `k3d`, and `helm`.

```bash
# Create cluster
k3d cluster create test -p 1883:31883

# Build
cargo build

# Build the images
make image "MANIFEST=./dmqtt/dmqttd/Cargo.toml" STRIP=1 TAGS='dmqtt-pod'
make image "MANIFEST=./dmqtt/operatord/Cargo.toml" STRIP=1 TAGS='dmqtt-operator'
make image "MANIFEST=./dmqtt/authd/Cargo.toml" STRIP=1 TAGS='dmqtt-authentication'

# Import images into the cluster so that they don't have to be fetched from repo.
k3d image import dmqtt-pod dmqtt-operator dmqtt-authentication -c test

# Deploy to the cluster (using dev so that we can use local versions)
helm install -f distrib/helm/E4K_CRD/charts/e4kdmqtt/values.dev.yaml edgy ./distrib/helm/E4K_CRD/charts/e4kdmqtt && kubectl apply -f ./distrib/helm/E4K_CRD/deployment.dev.yml

# Monitor pods until they're ready.
watch kubectl get pods

# Send MQTT messages with Mosquitto CLI
mosquitto_pub -t dane/will -d -u "client1" -P "password" -i "publisher0" -h 127.0.0.1 -m "hello1" --repeat 10 --repeat-delay 1 --will-payload "goodbye" --will-qos 1 --will-retain --will-topic "dane/will"

# Send message without authentication
mosquitto_pub -t dane/will -d -i "publisher0" -h 127.0.0.1 -m "hello1" --repeat 10 --repeat-delay 1 --will-payload "goodbye" --will-qos 1 --will-retain --will-topic "dane/will"

# Create client to subscribe to the will topic
mosquitto_sub -t dane/will -d -u "client2" -i "subscriber0" -P "password2" -h 127.0.0.1

# Create client to sub without auth
mosquitto_sub -t dane/will -d -i "subscriber0" -h 127.0.0.1

# Get logs for all pods and save them to files
kubectl get pods | grep azedge | awk '{print $1}' | xargs -I {} sh -c 'kubectl logs {} > {}.log'

# Get logs for crashed pods and save them to files
kubectl get pods | grep azedge | awk '{print $1}' | xargs -I {} sh -c 'kubectl logs --previous {} > {}-crash.log'

# Get support bundle
kubectl azedge-e4k support-bundle --namespaces default

# Delete cluster
k3d cluster delete test

# KeyValue store
mosquitto_pub -q 1 -t "\$store/pet" -i "publisher0" -u "client1" -P "password" -V mqttv5 -d -m "kittens" \
 -D PUBLISH response-topic "response" \
 -D PUBLISH user-property OPERATION 'UPSERT'

 # Message can be anything (ignored)
mosquitto_pub -q 1 -t "\$store/pet" -i "publisher0" -u "client1" -P "password" -V mqttv5 -d -m "" -D PUBLISH response-topic "response"  -D PUBLISH user-property OPERATION 'GET'

```


From Arnav

```bash
for d in operatord authd; do make "MANIFEST=./dmqtt/$d/Cargo.toml" STRIP=1 image TAGS='{{repository}}'; done; make MANIFEST=./dmqtt/dmqttd/Cargo.toml RELEASE=1 STRIP=1 image TAGS='{{repository}}'

k3d cluster delete; k3d cluster create --volume /dev/mapper:/dev/mapper -p '1883:31883'; k3d image import dmqtt-pod dmqtt-operator dmqtt-authentication

helm install -f distrib/helm/E4K/charts/e4kdmqtt/values.dev.yaml edgy ./distrib/helm/E4K/charts/e4kdmqtt
```

Test commands

```bash
date +"%m-%d-%Y-%T" | xargs -I {} sh -c 'mkdir {} && cd {}'
date +"%m-%d-%Y-%T" | mkdir `date +"%m-%d-%Y-%T`
date +"%m-%d-%Y-%T"
```