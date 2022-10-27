# Learning Docker

Hey there, if you're reading this then you're most likely interested in checking my notes on how I'm learning to use Docker. 

## What Docker does

- Written in Go
- Manages kernel features
  - Uses "cgroups" to contain processes
  - Users "namespaces" to contain networks
  - Users "copy-on-write" filesystems to build images
- Used for years



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

Save images to archive:

`docker save -o my-images.tar.gz *image1* *image2:latest* *image3:14.04*1`

Load images from archive:

`docker load -i my-iamges.at.gz`

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



## Dockerfiles

- small program to create an image
- run with
  - `docker build -t *name_of_result* .`
- the parts that change the most belong at the of the Dockerfile
- they are NOT shell scripts
  - processes you start on one line will not be running on the next line
  - each line is a call to `docker run`
- Environment variables *will* be set on the next line

*RUN* will run a command during building, and *CMD* will run when accessing the container (if no specific command was provided eg. bash).

Example of Dockerfile:
`FROM ubuntu:14.04 `
`RUN echo "building simple docker image."`
`CMD echo "hello container."`

`docker build -t *name_of_new_image* .`

`docker run [--rm] *new_image*`

Other examples:
`FROM debian:sid`
`RUN apt-get -y update`
`RUN apt-get install nano -y`
`CMD ["/bin/bash", "/tmp/notes"]`

`docker build -t example/notes .`

Chain other images:
`FROM example/notes`
`ADD notes.txt /notes.txt`
`CMD "nano" "/notes.txt"`

### Dockerfile syntax

 - **FROM** - must be the first command in the file, which image to download and start from

    `FROM ubuntu:16.04`

 - **MAINTAINER** - defines the author of this Dockerfile 
    `MAINTAINER Firstname Lastname <email@example.com>`

 - **RUN** - runs the command line, waits for it to finish and saves the result
    `RUN unzip install.zip /opt/install/`
    `RUN echo hello docker`

 - **ADD** 
    adds local files
    `ADD run.sh /run.sh`
    add contents of tar archives
    `ADD project.tar.gz /install/`
    works with URLs as well
    `ADD https://project.example.com/download/1.0/project.rpm /project/`

 - **ENV** - sets environment variables, both during build and when running the result
    `ENV DB_HOST=db.production.example.com`
    `ENV DB_PORT=5432`

 - **ENTRYPOINT** - specifies the start of the command to run (USE IF YOU WANT CONTAINER TO ACT LIKE A COMMAND-LINE PROGRAM)

 - **CMD** - specifies the whole command to run

 - **Shell form vs Exec form**
    shell form:
    `nano notes.txt`
    exec form:
    `["/bin/nano", "notes.txt"]`

 - **EXPOSE** - maps a port into the container
    `EXPOSE 8080`

 - **VOLUME** - defines shared or ephemeral volumes - avoid defining shared folders in Dockerfiles
    shared
    `VOLUME ["/shared-data"]`
    ephemeral
    `VOLUME ["/host/path/", "/container/path/"]`

 - **WORKDIR** - sets the directory the container starts in
    `WORKDIR /install/`

 - **USER** - sets which user the container will run as
    `USER arthur`
    `USER 1000`

Check more: https://docs.docker.com/engine/reference/builder/



### Multi-project building

**COPY** - used for multi-project. copies from previously stated `FROM` in Dockerfile

```FROM ubuntu:16.04 as builder
RUN apt-get update
RUN apt-get -y install curl
RUN curl https://google.com | wc -c > google-size

FROM alpine
COPY --from=builder /google-size /google-size
ENTRYPOINT echo google is this big; cat google-size
```



This will generally reduce the size of the container

## Docker control socket

 - docker is two programs: client and server
 - the server receives commands over a socket (either over a network or a "file")

`/var/run/docker.sock`

Starts client within a docker container:
`docker run -ti --rm -v /var/run/docker.sock:/var/run/docker.sock docker sh`

### Bridging

 - Docker uses bridges to create virtual networks
 - these are software switches
 - control ethernet layer

turn off protection (for debugging):

`docker run --net=host options *image-name* *command*`

### Routing

- creates "firewall" rules to move packets between networks
- NAT
- change the source address on the way out
- change the destination address on the way back in
- "exposing" a port is really just "port forwarding"

`sudo iptables -n -L -t nat` or iptables-legacy

`docker run -ti --rm --net=host --privileged=true` <-- gives full control to the container

### Namespaces

- they allow processes to be attached to private network segments
- these private networks are bridged into a shared network with the rest of the containers
- containers have virtual network "cards"
- containers get their own copy of the networking stack

## Processes and CGroups

Find name of the root process of a specific container:

`docker inspect --format '{{.State.Pid}}' *container_name*`

## COWs (Copy-On-Write)

- Read from the original
- Copy when you write (with layers)
- contents of layers are moved between containers in gzip files
- containers are independent of the storage engine
- any container can be loaded anywhere
- it is possible to run out of layers on some of the storage engines

### Volumes and bind mounting

- linux VFS (Virtual File System)
- mounting devices on the VFS
- mounting directories on the VFS

`mount -o bind *directory*`
