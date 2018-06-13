# Five patterns for success for application transformation

* Elton Stoneman (Docker Inc)
* Demos and scripts: https://github.com/sixeyed/presentations/tree/master/dockercon/2018-sanfrancisco
* Other links
  * https://github.com/sixeyed/docker-windows-workshop
  * https://github.com/sixeyed/docker-networking-workshop
  * https://github.com/sixeyed/docker-kube-workshop
  * https://github.com/sixeyed/presentations
  * https://github.com/BretFisher/container.training

## Integration points
* Logs
* Configuration
* Dependencies (everything there?)
* Health/Monitoring
* Metrics

## Making old apps work like new apps
Without
* changing code
* taking a technology dependency
* breaking the dev workflow

## Logging
Package a utility on the side of the application to read from the logs and write out to Docker.

Docker has plugins for logs, for example to push to Splunk or other solutions. BUT first we must get the logs out of the containers!

VOLUME C:\logs

## Config
Default configuration in the image
Know the file path to the configuration inside the image
Configuration gets injected by the platform (kubernetes config map or docker config)

stack yaml:
* java-web
  * configs:
    * ...
* ...
* configs
  * 

Define defaults in the image and override the files/values per environment through the Docker container platform/cluster.

## Dependencies
* dependency check utility: help legacy applications by checking for their dependencies (e.g., is the DB tier available yet?)
* runs once before startup
* configuration aware and ROBUST (it cannot fail :p)

## Health
* utility/script to check if the app is up and running / ready (e.g., get a basic resource)
* runs periodically
* exercises the application logic

Issue for heavily secured systems! Need test pages

## Metrics
* metrics exporter utility
* runs in background
* exposes runtime metrics