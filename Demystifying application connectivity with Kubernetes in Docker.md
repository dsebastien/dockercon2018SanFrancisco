# Demystifying application connectivity with Kubernetes in Docker

* Karthik Prabhakar (@worldhopper)
* Nicola Kabar (@nicolakabar)

## Networking design considerations
* heterogeneous infrastructure
* diverse app portfolio
* zero-trust security

## k8s connectivity basics
Every pod has an IP address.

Concepts:
* pod networking (CNI): allows pods to talk to each other over the network
* services (implemented by kube-proxy): service endpoints are well defined and fixed, while pods come and go
* network policy: each pod has a distinct IP address and things are much more transient. Network policies allow to dynamically define rules for dynamic microsegmentation based on kubernetes labels
* kube-dns: provides dynamic names for pods
* ingress: when connecting to apps from outside the cluster, we want to do ssl termination, headers inspection, ...

Pod creation: kubelet
* kubelet runs on each node
* kubelet creates the pods when requested
* kubelet calls the CNI plugin and says "can you connect this pod to the rest of the container network?"
  * IPAM for address attribution (by default with Calico)
  * the information is stored in the Calico data store (in etcd)

On the host, there's a Calico pod that is notified when new pods are created on that node. That Calico pod updates the routing table and the other nodes in the cluster get informed.

Pod to pod connectivity is based on classic IP routing -> Linux handles it.

Kube-proxy:
* all pods in the cluster can talk to each other
* pods are transient -> when a pod dies, k8s replaces it dynamically
  * those new pods get different addresses, ...
  * services allow to have a fixed entry point
    * when we expose a deployment as a service, k8s gives that service a fixed IP address (VIP) and updates IP table (NAT) rules on each node
    * basic load balancing (round robin)
* node ports allow to expose a specific port on all nodes and have traffic sent there to be forwarded where needed
* load balancer: allow to link to external load balancers
  * ??

Ingress:
* when trying to access an app from outside the cluster
* one way: ingress
* in a deployment, we can say that for URL x, service a should be called, ...
* works with HTTP & HTTPS and a few other protocols
* there are ingress "controllers" on k8s nodes

Network policy:
* k8s is highly dynamic
* we can define policies to say that all things with label X can or can't talk with each other
* instead of doing IP address based isolation, we isolate based on labels
  * allows to create micro segmentation rules

## Calico CNI (Tigera)
Secure networking for the cloud-native era.

Open source project maintained by Tigera.

* scalable, distributed control plane
* policy-driven network security
* no overlay required
* integrated with all major cloud platforms (azure, aws, gcloud)
* widely deployed, proven at scale

## Docker EE + Calico integration overview
Docker EE
* UCP: control/manage
* DTR: secure registry
* worker nodes

In Docker EE 2.0, calico is used by default.

On manager nodes:
* calico-node
* calico-kube-controller
* k8s-scheduler
* k8s-controller
* k8s-apiserver
* kubelet
* kube-dns
* kube-proxy
* UCP Manager / k8s master

On worker nodes:
* calico-node
* kubelet
* kube-proxy

## Revisited design considerations
Infra:
* no underlay dependency = no lock-in
* simple zero-touch provisioning
* any infrastructure/cloud (on prem, cloud, hybrid)

Diverse app portfolio:
* pod-pod: Calico CNI
* services: ClusterIP, NodePort, LoadBalancer
* ingress: NGINX Ingress Controller
* DNS: kube-dns
* k8s network policy: Calico

Zero-trust security
* elements
  * stage/tier separation
  * tenant/namespace isolation
  * micro-segmentation
  * compliance
* declarative policy-driven isolation
* fine-grained access control
* dynamic, in lock step with Kubernetes

## Takeaways
Docker EE 2.0 seamlessly integrates Calico as the default CNI, providing choice of using native k8s connectivity abstractions on any infrastructure all while enforcing a dynamic policy-based microsegmentation.

## Notes
* `ip routes show`
  * all cali... interfaces...
* `kubectl get svc -n stars -o wide`
* `ip tables -L -n -t nat`
* slack.projectcalico.org