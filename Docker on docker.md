# Docker on Docker

## What they do at Docker
Run Docker EE internally for SaaS and internal workloads

Infra:
* 200 hosts in production on EE
* 100+ microservices
* hub/store/cloud: 1B pulls every ~2 weeks
* ...

They provide a self-service container platform

Interfaces for developers (internal):
* Docker EE
* logging
* metrics and monitoring

Dogfooding

Acronyms
* UCP: Universal Control Plane

## Planning process for EE2.0
* create test cluster
* deploy EE to test cluster
* deploy swarm workloads to test cluster
* k8s routing setup
* deploy k8s workloads to test cluster
* ...

## Infrastructure preparation
Sizing
* min 8GB, 16GB recommended

Networking:
* Calico
* Kubernetes concepts (ingress controllers, nodeport)
  * ingress: early, limited customization
  * nodeport: simple building block

hub.docker.com:443 -> {ip_haproxy_node_x}:4321 -> (nodeport service) -> {ip_routing_node_y}:32775 -> (calico) -> {calico_ip_hub_pod_z}:80

Metrics
* prometheus
  * every in-house application exposes a /metrics endpoint
  * exporters for third party applications

Service discovery
* consul: doesn't work well with k8s
* kube api

Logging
* no changes there


## Application migration
* upgrading UCP

Gotchas:
* images must be accessible on EVERY node
* no down nodes in UCP cluster
* ubuntu 14.04 - see release notes for shared mounts
* manager load balancing - kube API port 6443
* kube dns

Application selection/ordering:
* low traffic, non-critical
* high traffix, non-critical
* low traffic, critical
* high traffic, critical

Registry secrets
* credentials used to authenticate to a private registry

k8s deployment object

```
...
spec:
  template:
    metadata:
      labels:
        app: ...
    spec:
      imagePullSecrets # secrets to use
        - name: saasdeploycreds
...
```

Service object:
```
...
kind: Service
...
spec:
  type: NodePort
  ports:
    - port: 80
      nodePort: 32768 # on every worker node it opens this port and any traffic hitting this on a node is transported/routed to the pods with the matching labels 
    selector:
      app: ...
```

Resource sets
* different UCP collections for Swarm & k8s
  * k8s: /hub-k8s
  * swarm: /hub

## Testing the migration
* test requests against the k8s pods
* service backed by swarm containers and k8s pods
* change in deploy tooling

Airboss: based on engine labels:
* set node orchestrator
* create UCP collection
* add node to UCP collection
* apply collection label in kube
* create kube namespace
* create annotation linking collection label to namespace

Pods:
* deploy containers together
* useful for breaking up monoliths
  * localhost
  * sharing volumes
* useful for metrics exporters

CRON jobs:
* batch processing
* moving system services into containers (e.g., docker prune)