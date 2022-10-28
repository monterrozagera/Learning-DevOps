# Docker Orchestration

- Start containers and restart them if they fail
- service discovery- allow them to find each other
- resource allocation- match containers to computers



## Kubernetes

- containers run programs
- pods group containers together
- services make pods available to others
- labers are used for advanced service discovery

## AWS EC2 Container Service

- Task Definitions - define a set of containers that always run together
- Tasks - actually makes a container run right now
- Services and exposes it to the Net - Ensures that a task is running all the time
- Load balances (ELBs) to service
- Can create your own host instances in AWS
- make your instances start the agent and join the cluster
- pass the docker control socket into the agent
- provides Docker repos and could build your own
- CloudFormation stacks