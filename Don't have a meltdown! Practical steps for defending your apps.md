# Don't have a meltdown! Practical steps for defending your apps

Links
* https://github.com/lizrice/no-meltdown

## Code quality
* Static analysis
* Code review
* ...

## Security testing
Goal: catch problems as early as possible

Automated testing is not just for application code!
Try and automate as many security checks as possible in the build pipeline.

Open source TLS tester: ssl-check

## Security policies
PCI compliance: Data security standard

Scanning tools:
* Aqua security solution for scanning for example
* Docker example
* MicroScanner
* Clair

Image admission controls:
* for example prevent unsafe images from running (e.g., docker image using vulnerable libs)

## Host vulnerabilities
* inside container images
* on the host machines

## CIS
* CIS Kubernetes Benchmark
* CIS Docker Community Edition Benchmark

## Least privilege
Less is better ;-)

* minimize bind mounts
* set USER in dockerfile
* avoid `--privileged`

## Defence in depth
Combine security measures, onion style :)
The more layers, the more difficult / time consuming it will be to hack our systems (hello captain obvious!)

Runtime protection:
* Seccomp
* AppArmor
* ...
