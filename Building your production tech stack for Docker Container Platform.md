# Building you Docker tech stack

Bret Fisher

Links:
* Slides: https://www.bretfisher.com/dockercon18/
* https://github.com/BretFisher/awesome-docker

## Orchestrator
High availability vs auto-recovery

## Digital Ocean
* droplets
* block storage
* load balancer (incoming http)

Use overlay networks to isolate traffic

## OSS stack
* docker machine
  * install docker easily locally or on a remote host
  * manage docker on the local machine or on a remote host
* RexRay: https://github.com/rexray/rexray
* swarm exec: https://github.com/mavenugo/swarm-exec
  * execute commands on a swarm easily
* Traefik
* Prometheus + ELK + Portainer
  * portainer: Web GUI to manage swarm
  * prometheus: logging
  * docker stack deploy -c stack-swarmprom.yml prom, stack-elk.yml elk, stack-portainer.yml portainer
* ...

## Maintenance tasks
Things to do in the Swarm like cleaning images

## Operations: updates
Tune reservations and limits.

## Security
* Docker Bench: host setup scanning
* Aqua Microscanner: image scanning
* Sysdig Falco: behavior monitoring (someone going into containers)
* user namespaces: great way to lock down containers
* ...

## Docker Certified Infrastructure (DCI)
* Reference documentation (docker success website), tools 
* Terraform + Ansible + AWS
  * terraform apply
  * ansible-playbook -i inventory install.yml

More flexible deployment tools, more SecOps tools, ops tools are fully HA, ops tools are team-ready, deploy to k8s is just as easy

## For AWS
Use parts of Docker Certified Infrastructure:
* cloudwatch and telegraph
* logging via CloudWatch logs

## Summary
* infrastructure as code makes everything repeatable
* no "special" nodes! use remote management
  * make sure that any server may be replaced!
* help is cool on the Kubernetes side
* look for compose files of popular tools to make stacks
