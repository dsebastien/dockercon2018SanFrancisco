#  Stack Overflow FAQ
Speaker:
* Brandon Mitchell
* Twitter: @sudo_bmitch

## How do containers differ from a VM
App isolation vs hardware isolation, Containers have a shared kernel
* Who likes uptime
* Who wants to maintain a server that hasn't been rebooted since 3 years
* Uptime quickly becomes a ticking time bomb

What we want is availability, not uptime! We want a LB poiting to replicas spread across multiple availability zones so we can have low uptime and high availability

Practical:
* don't ssh into containers (exec and only in dev)
* don't upgrade containers in place (replace them)
* don't install multiple apps inside a single container (compose files)
* don't give containers static IPs (LB/reverse proxies)
* don't backup containers (backup volumes)
* don't export containers to make new images (use a Dockerfile)

Pet vs Cattle!

## Dockerfile: how does cache work?
Cache requires:
* same command to be run
* same checksum on all files
* same previous layer
* image was built locally

Cache can be broken by
* changing a build ARG value
* changing a timestamp
* the previoous layer being rebuilt

To trust images pulles from a registry, use: `docker build --cache-from my_image ...`

## Why is my image so large?
* many run commands cause image growth
* any change to a file (even single bit), the whole file is copied on the next layer (copy on write)
* a chmod or chown changes a timestamp on files and that causes copies (copy on write)

To to examine layers? Build with `docker build ...`

Check differences: `docker diff ${cid}`
* A: added
* C: changed -> copy on write
* D: delete
* ...

Reducate the image size by merging RUN lines
```
RUN mkdir /data \
 & ...
 & ...
```

## Invalid reference format?
* a ref is a pointer to an image
* the docker command line is order dependent
  * docker ${docker_args} run ${run_args} image ${cmd}
* frequently happens when an invalid arg gets parsed as the image name or invalid characters from copy/pasting from a source that changes dashes and quotes

## Executable not found
* everything after the image name is the command that docker should run!
* if we have a shell script, the executable is the first line of the shell script
* check for windows linefeeds on linux shell scripts
* if it is a binary, there is likely a missing library

## Networking issues between containers
* make sure app is listening on 0.0.0.0 and not 127...
* use a user generated network
* use DNS: container id, container name, service name or network alias
* connect to the container port, not the host published port
* with overlay networking, open 7946/both, 4789/tcp and protol 50

