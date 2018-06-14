# SecOps with Docker

Scott Coulton

## Docker & Kubernetes 
Communications are encrypted using certificates that are automatically generated, renewed, etc.
Nothing is sent in clear text.

## Security
Security at the earliest points in the application lifecycle.
For example during the build process: vulnerability scanning, static code analysis, image signing, ...

## Differences between Swarm & Kubernetes
Swarm
* uses docker cli
* deploys applications through "stacks"
* overlay networking

Kubernetes
* kubectl as the cli tool
* more deployments definitions ie pods, svc, deployments
* plug and play networking with CNI

## SecOps with Kubernetes
Secure Supply Chain
* developer pushes image to DTR
* vulnerability scanner kicks in and looks for vulnerabilities
  * if vulnerabilities are found then block the image
* implement automated policies and scanning
* implement signing
* verify signatures prior to production deployments

Advices:
* tag with semantic versioning and never deploy "latest".
* always sign images and verify signatures
* check out Notary

## Push and sign
```
export DOCKER_CONTENT_TRUST=1
docker push {dtr_url/registry_url}/{account}/{repository}:{tag}
```

## Kubernetes

## Never run as root
```
apiVersion: v1
kind: Pod
metadata:
  name: my-dockercon-app
spec:
  securityContext:
    runAsUser: 1000
```

## Use a read-only file system
```
  securityContext:
    readOnlyRootFilesystem: true
```

## Privilege escalation
```
  securityContext:
    allowPrivilegeEscalation: false
```

## SELinux
Kubernetes doesn have SELinux enabled
To check:
* https://blog.jessfraz.com
* Apparmor

SELinux:

```
...
  securityContext
    seLinuxOptions:
      level: "s0:c123,c456"
```

