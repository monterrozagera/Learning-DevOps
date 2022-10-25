# Learning Docker

Hey there, if you're reading this then you're most likely interested in checking my notes on how I'm learning to use Docker. 

Pull an image: `docker pull *image*`

List all available images: `docker images`

Delete image: `docker rmi *image_nameOrId*`



Run process container: `docker run *image*`

​		-delete container after finishing: `--rm`

​		-run in the background (de-attach): `-d`

​		-run multiple commands: `bash -c "sleep 3; echo all done"`

​		-run more stuff after creation: `docker exec [-ti] *name_of_process* *command*`

Stop container: `docker kill *container_name*`

Remove: `docker rm *container_name*`

Exit container: `exit` or ctrl-d

Run with interactive shell: `docker run -ti *image* bash`

Check all running processes (containers): `docker ps -a`

Check last exited container: `docker ps -l`

Make image out of container: `docker commit *process_ID* *new_image_name*`

Rename image: `docker tag *image_ID* *tag*` 



Attaching/Re-attaching processes: `docker run -d` (ctrl-p, ctrl-q) , `docker attach *name_of_process*`

Check process logs: `docker logs *container_name*`

Get all containers IDs: `docker ps -aq`

Remove all containers: `docker rm $(docker ps -aq)`

Add environment variable:

`docker run [--rm] [-ti] -e VARIABLE=string`

Add a tag when creating image out of container (instead of "LATEST"):

`docker commit *container_id* *new_image_name*:*tag_version*`

## Resource Constraints

Memory limits : `docker run --memory maximum-allowed-memory image-name command`

CPU limits: `docker run --cpu-shares` relative to other containers

​					`docker run --cpu-quota` to limit in general

*Orchestration generally requires resource limiting.*

 

## Networking

Name for referring for internal containers (instead of using IP): **host.docker.internal**

eg:

`docker --rm -ti --add-host=host.docker.internal:host-gateway *image* bash`

`nc host.docker.internal *port_number*`

Add listening ports: `docker run [--rm] [-ti] -p *number_port*:*number_port* image bash`

Check listening ports for specific container:

`docker port *container_name*`

Specify protocol:

`docker run -p 1234:1234/udp`

Create virtual network:

`docker network create *network_name*`

Connect new container:

`docker run [--rm] [-ti] --net *network_name*`

Connect running container to virtual network:

`docker network connect *network_name* *container_name*`



## Volumes

These are virtual "discs" to store and share data between containers

- Persistent - data will remain even if container gets removed
- Ephemeral - exist as long as a container is using it



create shared volume: 

`mkdir example`

`docker run [-ti] -v /home/user/example:/shared-folder`

connect new container to another container's volume:

`docker run [-ti] -v /example` <-- not shared with host (WILL BE DELETED AFTER ALL CONTAINERS ARE GONE)

`docker run [-ti] --volumes-from *container_with_volume*`



## Docker Registries

- Registries manage and distribute images
- docker (the company) offers these for free
- you can run your own, for safety and privacy



Search for an image:

`docker search *name*`



`docker login`

`docker push *username*/*image_name*:*image_tag*`



