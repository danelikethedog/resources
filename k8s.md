# K8S

## Kubectl

```bash
# Get detailed info about the pods
kubectl get pods -o wide

# Get info on the services
kubectl get services

# Get detailed info on the services
kubectl describe services

# Open a shell in a pod
kubectl exec -it [pod_name] -- /bin/sh
```

## K3S

* Lightweight version of K8S.

## Helm

* Essentially like a package manager for K8S.
* Instead of having to write a YAML file for each resource (especially in the case of replicas), you can define a helm chart which will allow you to use the template YAML to inject values into.

Dir structure

```
/some_dir
|- values.yaml
|- /charts
```

### Service Types

You can have a few different types of services:

* ClusterIP
  * Makes the service only accessible within the cluster.
* NodePort
  * Makes the service accessible from outside the cluster.
* LoadBalancer
  * Makes the service accessible from outside the cluster, but also creates a load balancer to route traffic to the service.

## CRD (Custom Resource Definition)

* Allows you to define your own resources.
* Helps to create first class resources in a cluster.
