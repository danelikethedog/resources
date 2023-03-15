# E4K

## Running the Broker

You can run the built broker within an image using `cargo`, `docker`, `k3d`, and `helm`.

```bash
# Create cluster
k3d cluster create test

# Build
cargo build

# Build the images
make image "MANIFEST=./dmqtt/dmqttd/Cargo.toml" STRIP=1 TAGS='dmqtt-pod'
make image "MANIFEST=./dmqtt/operatord/Cargo.toml" STRIP=1 TAGS='dmqtt-operator'
make image "MANIFEST=./dmqtt/authd/Cargo.toml" STRIP=1 TAGS='dmqtt-authentication'

# Import images into the cluster so that they don't have to be fetched from repo.
k3d image import dmqtt-pod dmqtt-operator dmqtt-authentication -c test

# Deploy to the cluster (using dev so that we can use local versions)
helm install -f distrib/kubernetes/dmqtt/values.dev.yaml edgy ./distrib/kubernetes/dmqtt

# Monitor pods until they're ready.
kubectl get pods

# Monitor logs
kubectl logs azedge-dmqtt-frontend-5dd9d4959-x6pdq

# Send MQTT messages with Mosquitto CLI
mosquitto_pub -t test -d -u "client1" -P "password"  -h 127.0.0.1 -m "hi" --repeat 10 --repeat-delay 1
```


```bash
for d in operatord authd; do make "MANIFEST=./dmqtt/$d/Cargo.toml" STRIP=1 image TAGS='{{repository}}'; done; make MANIFEST=./dmqtt/dmqttd/Cargo.toml RELEASE=1 STRIP=1 image TAGS='{{repository}}'

k3d cluster delete; k3d cluster create --volume /dev/mapper:/dev/mapper -p '1883:31883'; k3d image import dmqtt-pod dmqtt-operator dmqtt-authentication

helm install -f distrib/kubernetes/dmqtt/values.dev.yaml edgy ./distrib/kubernetes/dmqtt
```