# Docker storage designing a platform for persistent data

## What does immutable means for your data
Operating system:
* kerbel, libs, userland tools for all uses
* automation requires scripts, 3rd party tools
* patched

Containers:
* application + required libs/assets ONLY
* designed to be automated
* rebuilt

docker run --rm dan/container:1.0
* Copy on write layer (CoW)
* that's mutable but is not persisted (lost when the container dies)

Persistent data:
* should be there regardless of the lifecycle of containers
* the container could be scheduled to run on any node of the cluster, meaning persistent data may need to be accessed from any node

Accessing data can happen through different means:
* iSCSI / Fibre Channel
* NFS
* REST
* ...

Databases:
* latency
* IOPS
* bandwidth/throughput
* security requirements

Batch processing:
* image processing, format conversion, transcoding, batch processing, ...

## Applications with persistent data requirements
A large number of apps will typically "park" cold data to a disk under the following circumstances:
* waiting for a back-end system to respond
* out of order data being processed
* datasets that are typically too large to be mapped to memory

## Bad practices
* writing and storing logs inside a running container
  * NEVER log anything inside the container
  * push logs to an external / centralized platform!
  * if logs are written within the container and the container dies, the logs are gone

## Persisternt data with Docker
Persistence implementations:
* per container storage: storage for the container itself
  * /var/lib/docker/volumes/<uuid>/storage
  * transient, only persisted for the life span of the container
* shared storage (same host)
  * docker volume create <foo>
    * /var/lib/docker/volumes/<foo>
    * `docker run -it --rm -v dockercon:/mnt busybox sh`
* multi-host shared storage
  * across multiple containers/hosts
  * best option to scale out ;-)
  * `docker volume create --opt type=nfs --opt o=addr=192..., rw --opt device=:/volume1/docker nfs`
    * there's no mounting or anything with this when defining this volume
    * it's only when we run a container that requires that volume that we establish the connection to that storage

## Docker volume plugins
* extend the functionality of the docker engine
* uses the extensible Docker plugin API
* allows end users to consume existing storage and their functionality
* create docker storage volumes that are linked to a container lifecycle (can be persisted afterwards if needed)

Volume plugin workflow:
* host has docker engine running
* docker volume create -> does an API call through the plugin

Plugin benefits:
* data intensive apps: volume plugins expose specialized functionality in storage providers that can be utilized
* ...

Use cases:
* stateful app failover
* ...

## Orchestrating storage (swarm, k8s)

With Docker EE:
* swarm
  * as part of the compose file, specify a volume with a name and pass the settings
  * `volumes: ...`
  * service volumes: in the service definition, reference the volume along with additional configuration
* k8s
  * a persistent volume when applied becomes a resource that is available to the cluster
  * persistent volume claim: a claim is a request from a user for a persistent volume, the request can include specifics
    * volume size, volume capabilities, access methods
  * pod spec: defines a container or collection of containers that share networking and volumes (specify a PVC and map the PVC to a path inside the pod)
    * PVC = Persistent Volume Claim

```
kind: PersistentVolume
...
kind: PersistentVolumeClaim
```

## Takeaways
* shared storage
  * smaller images
  * efficient usage of repetitive data
  * decouples application and data
* running a database is fine in a container as long as perf requirements are met
* don't write logs inside a container
