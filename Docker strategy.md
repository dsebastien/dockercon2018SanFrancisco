# Docker strategy

## People
How to build the container platform for everyone

## Focus first on
* Business case
* Deploy first applications in containers and learn from that experience
* Training support, SLA, operating model, ...
  * Standardize

## Proof of value
Business case:
* CapEx
* OpEx
* Agility

Application migration map
* Not all applications require the same level of effort to Dockerize
x: stateless, stateful
y: stand-alone, cluster

Hardest: stateful and clustered

Focus on the low hanging fruits first (e.g., angular front-end applications that are stateless & stand-alone)

Create a scorecard based on application criteria important to your
* criteria
* increasing ROI

Examples: frequency of deployments, number of environments, team culture, ...

Gains in number of servers, licenses, personnel, ...

## First application in production
Deployment methodology
* PoC on temporary infra
* assessment: measure the maturity level of teams
* choose pilot application
* do core training for the team
* kick-off
* work streams
  * governance: everything non-technical (support, training, operating model, marketing, SLAs)
  * platform: deploying the clusters, making them highly available, DRP, ...
  * pipeline: CI/CD systems, images management (signing, ...)
  * applications: dockerizing the apps, creating dockerfiles, deployment descriptors, ...
* Go live!
* platform, pipeline and governance make up the docker container platform service

## Organization and culture
* centralized vs decentralized?
  * impact on infrastructure choices

## Services Classes
Define service classes for the docker container platform service:
* sandbox: great for training, for new users, ...
  * no need for HA, backups or anything
* development: application development, CI/CI pipeline, testing, need more storage, shared infrastructure for all development teams
* production: internal applications, HA, DRP, high performance, secure, self-service, business day support
* mission critical: mission critical applications, highest availability, highest performance, highest security, white glove service (help onboarding), business critical support

## Application migration plan
Order:
* first application in production
* highest ROI applications
* entire business line
* general availability: open up to all teams, tech stacks, ...

Progressive onboarding

## Marketing
Important to make good marketing for the introduction.

## Governance
* Marketing
* SLAs
* Training
* Support
* Operating model (who's responsible for what)
* Knowledge base

Training:
* Docker fundamentals (2D)
* Docker for enterprise operations (2D), Docker for Enterprise developers (2D)
* ...

Pets vs Cattle:
* old way: treat servers as pets: when they get sick, we heal them
* new way (cloud native): treat servers anonymously, if something is wrong you kill it and bring a new one

Culture change:
* containers are ephemeral, critical idea to get through
* automation matters most
* gold images are critical

-> Impact on the runbooks, operating model and automation of the infrastructure

Operating model:
* different cultures affect the organization of teams and operating models
* classic
  * developers code
  * ops team deploy and run
* with docker
  * developers code
  * devops team dockerize
  * ops team deploys and runs
* with docker and shift left
  * developers code
  * devops team use CI/CD, dockerize and deploy
  * ops team run
* full left shift
  * devops team use CI/CD, dockerize, deploy and run

Operating model for
* end of life apps: devops team dockerize, ops team deploy and run
* volume migration: dev team codes, external app migration factory dockerizes and deploys, externals manage the run

Support
* level 1: wiki self service portal/knowledge base
* level 2: internal support (hotline/chat)
* level 3: docker support

## New workloads
* traditional apps
* microservices
* ISV apps
* big data
* serverless
* IoT
* ...

New types can be easily adapted and deployed on the container platform
