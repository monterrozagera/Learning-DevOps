# Learning Docker

Hey there, if you're reading this then you're most likely interested in checking my notes on how I'm learning to use Docker. 

Pull an image: `docker *image*`

List all available images: `docker images`



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
