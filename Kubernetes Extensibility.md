# Kubernetes Extensibility

Eric Tune & Tim Hockin

## Kubernetes
* Abstraction layer over infrastructure
* A framework for declarative APIs for distributed control

## Extensibility goals
* infrastructure extensibility
  * support portability, scalable growth of the project, autonomy
* api extensibility
  * support cutomization, encourage new uses, autonomy

## Kubernetes & extensibility
* major focus
* https://goo.gl/2qz8jW

## Network plugins
No one size fits all.

Present
* CNI: Container Network Interface
  * standardized interface (used by Mesos and k8s)
  * "exec" interface with stdin/stdout/env API
* Widely used, also by other projects (e.g., mesos)
* Underpins the default implementation in Kubernetes

Future
* proposal open for a gRPC based API which covers more than just intf & IPAM
* tighter coupling with service api ?
* proposals for open multi API...

## Storage plugins
Many storage technologies and many vendors willing to support k8s

Present
* CSI: Container Storage Interface
  * collaboration: google, mesosphere, docker, cloud foundry
  * gRPC spec with Kubernetes-specific adaptators
  * in dev now (alpha in 1.10)
* plan to transition most in-tree plugins to CSI

## Device plugins
GPUs and other "accelerator" hardware is becoming very common

Part of the larger resource model in k8s

gPRC based plugins

Beta in 1.10

## Container Runtimes
Docker was backed-in, but other ideas too: rkt, containerd, cri-o, kata containers, hyper.sh, gVisor

gVisor: different approach for security of containers and leveraging CRI

CRI - gRPC based plugins

## Lessons learned
* evolution: exec -> RPC
* evolution: loose spec -> tight
* stateful daemon plugins
* containerized plugins FTW

gRPC plugins
* runtimes (CRI)
* storage (CSI)
* devices
* key management
* ...

## Controllers
Watch the world and what we expect and reconcile both.

Fundamental design pattern in k8s.

Examples: scheduler, kubelet, deployments, kube-proxy, cloud providers, load balancers, volume provisioners, auto-scalers, ...

Allows automation & extension of almost anything.

## Cloud providers
Built-in cloud provider API is hooked into many core control loops.

Multiple implementations

## Services
...

## More
* secret management (KMS)
* http load-balancing (ingress)
* NetworkPolicy
* DNS
* Scheduler extenders & whole extenders
* ...

## API extensibility
* add new types of resources to your clusters
* add custom policy hooks (to custom and built-in APIs)
* APIs that add and modify APIs

## Kubernetes for Docker
* supports API extensions
* certified Kubernetes
* Docker stacks uses API extensions

https://goo.gl/JT7v8Z

Exploring stacks: `kubectl config current-context`, `kubectl get services`, `kubectl get deployments`, `kubectl get stacks`, ...

Resources are exposed through the API server
* service resource -> Service API
* deployment resource -> Deployment API

kubectl proxy -v 5
* use to debug
* example of usage: kubectl get stacks -

kubectl get apiservices.apiregistration.k8s.io
* lists APIs

kubectl describe apiservices.apiregistration.k8s.io

kubectl get services -n docker

kubectl get deployments -n docker

When kubernetes for docker is enabled:
* deploys compose-api
* registers docker.com API
* adds a "proxy" API compose.docker.com
* ...

## Why use API extensions
* users
  * already have a client installed
  * already know how to find, trust it (TLS) and auth to the API
* controllers can efficiently watch your resources
* admins
  * can separate your resources by namespace
  * can authorize and audit log access to your resources

## API aggregation & extension API servers (EAS)
...

## Extension ecosystem
* serverless frameworks
* PaaS
* CI/CD systems
* database controllers
* ML

## Conclusion
* kubernetes for Docker: super easy
* API extensions: use them, author them, on Docker, for Kubernetes