# Where the helm are your binaries

Baruch Sadogursky (@jbaruch):

Links:
* https://twitter.com/jbaruch
* jfrog.com/shownotes

Why i believe printers were sent from hell to make us miserable

## Package managers
Suck :)

## k8s
You have an image and you create a new version:
* use latest: nooooooooooo ;-)
* use bash & sed: mehhhh
* use helm

## Helm
Helm provides 
* the capability to create encapsulated packages of kubernetes deployments
* powerful templating for descriptor files
* values file to define the values to replace in templates

Artifactory has support for helm & helm templates

## Helm chart - image relationship
* using templates we can reuse charts for multiple image versions
* but not really
  * templates will be populated from values within the chart
  * otherwise they has to be provided on each run
  * you don't want to require versions of images from the client!

## Kubernetes cluster control
helm client (e.g., helm install) -> Tiller server -> kubernetes

Helm client:
* local chart development
* managing repositories
* interacting with the tiller server

Tiller server:
* listening for incoming requests from the helm client
* combining a chart and configuration to build a release
* installing charts into kubernetes
* ...

## Helm comands
* helm init: init the repo
* helm search: look for packages inside index.yaml
* helm install: ...
* helm status
* helm repo: add/repo/maange repos

## Helm repositories
* official repo: kubeapps.com
* get a local repository!
  * option 1: create your own
    * run an http server with index.yaml
    * run helm repo index to generate the index
  * option 2: use JFrog Artifactory :)

## Age of binaries
Agile, ci, cd, devops, microservices, docker, serverless, IoT, ... ;-)

Issue: promotion of those binaries from local to dev to staging to production.. :)