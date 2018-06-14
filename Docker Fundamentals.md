# Docker Fundamentals Training

Present at the training from Docker:
* Ken Lavoie
* Tammy Fox

Instructor: kmarsh66

## Notes
Lowest level: Container Runtime
Above: Orchestrator (also included in Docker CE)
Above with DockerEE: tooling (e.g., UI: Universal Control Plane, RBAC, ...)

Q: Docker CE won't include K8S anymore??
TC: Windows CRI (container runtime interface)

Kernel namespaces: divide and isolate things

Root inside the container is root outside the container too :)

Logs: docker container logs <container name>

Attaching to a running container: run it as a daemon with -ti in addition to -d
Example: `docker container run -d -it centos:7 ping 127.0.0.1`
Ref: https://stackoverflow.com/questions/25267372/correct-way-to-detach-from-a-container-without-stopping-it


Docker ps -> right hand site = scientist name :)

docker stop is friendlier than docker kill

Even if an image created off of a Dockerfile including a RUN command, running a container based on that image and and specifying the command to execute will override the RUN command defined in the Dockerfile

When using ENTRYPOINT instead of run, it's possible to pass arguments to the container process when executing docker run.
Example: having a Docker image based on a Dockerfile with `ENTRYPOINT ["ping"]` in it and running a container with the following command will pass the arguments to the "ping" entrypoint: `docker container run myimage:1.0 127.0.0.1`

TC: Docker volume drivers

Volumes can be mounted in ro mode

Show bridges on CentOS: `yum -y instlal bridge-utils` gives the `brctl` command. `brctl show docker0` lists connections to the Docker bridge.
Alternative: `docker network inspect <bridge name>`

TC: VXLan tunnel

TC: https://www.opencontainers.org/

Containers on separate networks are firewalls from one another

Docker does not provide name lookup on the default bridge (called 'bridge')

Useful networking commands:
* docker network ls lists all networks on the host
* docker network inspect <network name> gives more detailed info about the named network
* docker network create --driver <driver> <network name> creates a new network using the specified driver; so far, we’ve only seen the bridge driver, for creating a linux bridge based network.
* docker network connect <network name> <container name or id> connects the specified container to the specified network after the container is running; the --network flag in docker container run achieves the same result at container launch.
* docker container inspect <container name or id> yields, among other things, information about the networks the specified container is connected to.

List used space: `docker system df`

Prune stuff: `docker system prune`

Docker Swarm vs load balancing: VIP in front of the service and uses round robin to distribute the traffic. No session management included.

Swarm mode ports:
* TCP port 2377 for cluster management & raft sync communications (management plane)
* TCP and UDP port 7946 for gossip discovery communication between all nodes (control plane)
* UDP port 4789 for VXLAN overlay network traffic (data plane)

Kubernetes
Get a join token: `sudo kubeadm token create --print-join-command`