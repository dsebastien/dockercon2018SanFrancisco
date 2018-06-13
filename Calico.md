# Calico Kubernetes network stack

## Kubernetes networking stack
Flat network: any container can communicate with any other container without NAT
CNI Container Network Interface
* defines a simple plug-in model for networking drivers
  * e.g., calico network plugin

# Calico
* IPAM: by default in Calico IPAM handles the IP addresses assignments
* container (pod) networking
* routing

# IPAM
* pods are VERY ephemeral, and there are a lot of them
  * can't maintain that manually
* calico takes a few large blocks of addresses (v4 and or v6) and efficiently assigns them, based on topology to control the routing table size

# Intra-Node routing
* no on-node switch: all traffic is IP routed between pods on the node or for ingress and egress using the Linux Kernel
* filters applied at the pod to worker interfaces
* works with all common Kubernetes constructs without modification (kube-proxy, ingress controllers, ...)

# Inter-Node routing
* does not require overlays (can integrate with your existing network)
  * can use either native (ip-ip) or existing overlays (e.g., VXLAN) if needed
  * docker ee ships with ip-ip enabled by default (to match swarm's behavior)
* used standard routing protocols to communicate where workloads are in the fabric
* if you understand basic IP routing and Linux networking you understand the data path. Standard tools and techniques work for troubleshooting if required

# Network policy
* kubernetes concept
* stage/tier separation (prod, dev, test)
* tenant/namespace isolation
* microservice segmentation
* compliance (e.g., PCI)

# Kubernetes network policy
* leveraged and extended by calico
* specifies how groups of pods are allowed to communicate with each other and other network endpoinds using
  * pod label selectors, namespace label selectors, protocols/ports
* pods selected by a network policy are
  * isolated by default
  * allowed incoming traffic if that traffic matches a network policy
* requires a controller to implement the API

Calico supports both ingress and egress policies

# Calico architecture
* distributed control plane, calico/node on each host
* shared state in etcd key value store
* overlay (ip-ip) or unencapsulated (flat nedtworking) - ip - ip default in EE
* BGP for route...
* ...
