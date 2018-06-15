# Deep dive in container service discovery

Laurent Bernaille (@lbernail)

Links:
* https://github.com/lbernail/dockercon2018

## Service discovery
Automatic detection of devices and services offered by these devices on a network

Important for containers do to their ephemeral and dynamic nature.

## Service discovery in k8s
* Deployment
  * ReplicaSet
    * Pod 1..n
    * Service
    * Endpoints

## Pod readiness
A pod can be started but not ready to serve requests
* initialization
* connection to backends

Kubernetes provides an abstraction for this: Readiness probes

```
...
readinessProbe:
  httpGet:
    ...
```

kubelet calls the API server whenever containers start, become ready, ...
The API stores the information in etcd
The cluster controller ...

## Load balancing solutions
DNS round robin:
* old way: service has a DNS record with one entry per endpoint
* many clients will only use the first IP
* many clients will perform resolution only at startup

VIP + IP based LB
* service has a single VIP
* traffic sent to this VIP is load-balanced to endpoints IPs
* requires a "process" to perform and configure this load balancing

In kubernetes:
* kube-proxy runs on all cluster nodes
* when a client connects to a service, it'll use the single IP
* ... (TC, missing info!)

## kube-proxy modes
* userpsace: original implementation, userland TCP/UDP proxy
* iptables: default now (since 1.2). Uses iptables to load-balance traffic. Faster than userspace
* ipvs: brand new. Uses kernel load-balancing. Still relies on iptables for some NAT rules. Faster than iptables

## iptables
Overview:
* client sends traffic to the host
* iptables performs DNAT on the traffic
* in reverse path, it'll be transformed back

kube-proxy does a lot of things in the NAT table in iptables:
* all traffic goes through the kube-services chain
  * global service chain: identify service and jump to appropriate service chain
  * one SVC chain per service: responsible for load balancing. Uses statistic iptables module (probability of rule being applied). Rules are evaluated sequentially

Once a rule is matched, the traffic is sernt to an endpoit chain
* responsible to mark hairpin traffic (client = target) for SNAT DNAT to the endpoint

Edge casae: hairpin traffic
* client can also be a destination (e.g., traffic from pod 1 to pod 1)
* after DNAT: src ip = Pod 1, dst ip = Pod 1
* the traffic will never hit the reverse NAT (not possible)
* SNAT on host for this traffic
  * pod1 IP => SVC IP
  * SNAT HostIP > SVC IP
  * DNAT HostIP > Pod1 IP
* reverse path
  * Pod1 IP => HostIP
  * Reverse NAT: SVC IP > ...

## Persistency (session affinity)
For stateful apps, k8s provides a way to configure session affinity
*  uses the "recent" module of iptables
* in addition to the previous rules in the endpoint chain, when we see traffic from a source, we add the IP to a set that has the same name as the chain. Later when traffic comes again, the set will be checked and it will be routed back to the same destination

To see the chains:
* `sudo iptables -t nat -L PREROUTING`
* `...-L KUBE-SERVICES`
* `... -L KUBE-SVC-<SERVICE NAME>`

## iptables proxy gotchas
* rules synchronization: every sync flushes and reloads all Kubernetes chains
  * with many services and pods, reloading everything takes long
* performance
  * with many services and endpoints, there might be many rules to go through to route traffic
  * also takes long and adds latency

## IPVS
* L4 load balancer built in the Linux Kernel
* many load-balancing algorithms are supported
* very fast
* still relies on iptables for some use cases (SNAT is particular)

With IPVS, the IP address has to be on the host for IPVS to be able to do the load balancing

`ip addr show dev kube-ipvs0`
  * virtual interface

sudo ipvs

## Alternatives to kube-proxy
* kube-router
  * cloud native labs
  * pod networking with BGP
  * network policies
  * IPVS based service proxy
* cilium
  * relies on eBPF to implement service proxying
  * implement security policies
  * ...

## What about DNS
Just another kube service (kube-dns).

DNS pods get DNS information from the API server.

`while true; do curl blabla; sleep 1; done`

## L7 load balancing options
Ingress controllers
Service mesh (Istio)

## Key takeaways
* complicated under the hood
  * helps to know where to look at when debugging complex setups
* service discovery
  * challenge: intrgrate with hosts outside of k8s
* load balancing
  * L4 is still very dynamic (IPVS, eBPF)
  * L7 is only starting, expect to see a lot!
