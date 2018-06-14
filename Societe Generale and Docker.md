# Societe Generale and Docker

## Project goals
Initiative started two years ago

Goals:
* support digitization
* reduce operations costs

## Docker usage
Today: 2000 containers
Goal: 15000

Societe generale:
* developers (investment, banking, retail banking, insurance)
* Docker EE cluster on top of vmware
* IT operations: global technology services

Currently they don't use it for high performance cases (AI/ML/Big data)

## Strategy
Phases:
* first: focus on performance, stability, quality of service and security

Their main puzzle: growth (x10) and need for always more security, quality of service.
They want to also reduce the cost of VMs and better utilize their hardware

All while still retaining user experience

## Multi-tenancy
Single or multiple clusters?

Left: CaaS
* single cluster with everything shared by different teams

Right: CLaaS
* multi-cluster
* cluster per team/group

Left: high multi-tenancy
Right: low multi-tenancy

At SG they started with CaaS platform in order to focus on security, ...

They have two clusters: non-production and production

* volumes: auto provisioning, simple & secured access (Vault, Scality, NFS, CIFS, NetApp)
* CI/CD: Jenkin master & slaves (100 nodes!)
  * ~1K builds/deployments per day
* Networks: VPN & public IPs

They started with CI/CD and had to do a lot of tweaking

## Security and compliance checks
How to design the RBAC model?

Pre-made collections:
* /System: UCP managers, DTR nodes, system services
* /Shared: worker nodes
* /Shared/Private/<username>: user-specific collections

Dimensions to take into account:
* application
* environment
* job role


At SG:
* split by organization and service offer
  * Collection /Run/Std
  * Collection /Build/Std/<Org>
  * /Run/Edge
  * /Run/Edge/<Org>
* resources shared by organizations
  * /Shared/Private/<Org>
    * secrets <org>
    * networks <org>
    * volumes <org>
* Build services on a dedicated collection
  * /Build/Std
  * /Build/Std/CI-CD
    * Jenkins <Org>

## Self-service onboarding
APIs:
* onboarding through APIs
* their goal: self-service
* they created a self-service API that organizations can call to have everything handled
* in the implementation they use standard Docker APIs
* they also created a CI/CD provisioning API

The APIs allow to create and manage network, collections, roles, volumes, ...

Hardware sharing:
* how to share hardware resources, RAM and CPU?
* safe strategy: reservation = limit)
  * one can never go beyond the limits
  * never any problem
  * but not the best for resource utilization
* optimized (reservation < limit)
  * might go above the limit, so tradeoff to consider

At SG they used minimum reservation to avoid resource waste
* minimum reservation
  * memory used after initialization
  * under minimum load
* CPU limits are mandatory

They have a local watcher on each cluster node that looks at CPU/memory usage and reacts when they go over threshold -> puts the node in "pause" to protect the host:
* thresholds
  * load > 80%
  * memory usage > 80%
* thresholds for reactivation
  * memory usage < 75%
  * load < 75%

Node auto scaling:
* watcher that looks at the cluster level
  * checks the number of nodes in "pause" (is it above 75%?)
  * average load across the cluster (is it > 75%)
  * average memory across the cluster (is it < 75%)
  * based on the checks it auto-scales the Docker infrastructure

Promotion mechanism to go from development to production
* images are scanned for vulnerabilities
* images are signed for authenticity
* RBAC can apply specific rules for production
  * e.g., NO "docker exec" on production

Developers can only push to the non-prod DTR

Developers use a Compliance Check API
* check the vulnerability scanning results
* checks the default user is not root
* check that you cannot become root
* enforce CPU limits, ... in compose file
* check for Trojan, Viruses, ...
* checks done via YAML parser

Developer machine
* docker for windows/mac

Non-production cluster
* Docker UCP

Production
* Docker UCP

Docker Trusted Registry (non-prod)
* version control

## Data sources and visualization
User experience
* ops do not wxant to spent time on Dev Support
* new developers want to use containers as a commodity

Data is key!

* docker daemon
* system containers
* docker hosts

* Get, List: UCP. Provisioning, troubleshooting
* Logs: Data lake, chargeback, metrology
* Events, Stats: scripts, monitoring, automation
* ...

They want to prototype a Dev Console including all the information useful for all developers
* stacks, services, nodes, tasks, state, status, timing, resources, ...
* access to inspect, overview of tasks, access to logs

## Compose file
From dev to prod: several versions of the same compose file

* dev
  * application parameters
* test
  * application parameters
  * integration parameters
* production
  * application parameters
  * integration parameters
  * infrastructure parameters

It expands as it moves closer to production.

They want to limit the exposure to complexities of compose files to developers and instead keeps infrastructure aspects to docker specialists and ops specialists.

They have in mind to work with two different compose files: one created/maintained by developers (simple one) and another one created/maintained by ops guys. With that, they want to merge compose files to get the final one that'll be used for production.

## First results
* stability and resource optimization
  * they could better utilize their resources
* security
  * prepared for their security audit
* their infrastructure is growing fast
  * they onboarded x users each month to progressively expand the usage
  * user acceptance and user feedback is very important to them

## Next steps
* Kubernetes will be used
  * they'll use Kubernetes scheduling policy
* they're investigating inheritance of compose files with helm and helm charts
* they're working on their data visualization tool

## Takeaways
* platform rules and constraints are many: security, multi-tenancy, ...
* they need to help/facilitate compliance with easy to use/apply rules

