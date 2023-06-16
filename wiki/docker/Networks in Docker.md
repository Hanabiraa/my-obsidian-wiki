---
created: [" 15-06-2023 13:06"]
aliases: []
tags:
- article/
---

# Networks in Docker

## none
![[Pasted image 20230615130652.png]]

- can't get to internet from container in none network and *vice versa*:
	![[Pasted image 20230615133349.png]]

- but can connect to localhost (because it's self-loop and request doesn't leave container):
	![[Pasted image 20230615133613.png]]

## host
![[Pasted image 20230615130736.png]]
- no network isolation
- container is host now

## bridge (*default*)
![[Pasted image 20230615130809.png]]
- default 
- containers inside network can interact with each other
- isolated from outer world by blocking ports
- to access from outside - open ports

# Network interface in containers
### Network structure across containers:
![[Pasted image 20230615141806.png]]
- containers must run inside same subnetwork to have access to each other
- access through IP-address
![[Pasted image 20230615131109.png]]

### can create custom networks:
![[Pasted image 20230615131149.png]]
- for more sophisticated isolation (e.g. first two containers can't interact with container 3)

### access to container inside net through its name:
```{bash}
curl <container_name>:<port>[/<route>]
```
![[Pasted image 20230615144352.png]]

### example of 2 containers inside custom network:
![[Pasted image 20230615145403.png]]
# Commands

## `docker network` - command for networks

- get current working net: `docker network ls`
	![[Pasted image 20230615132745.png]]

## `docker run --net=...`
- choose net at container start: `docker run <...> --net=none/bridge/host <...>`

## create own network
on the top of `bridge` network
```{bash}
docker network create <net_name>
```


## delete network
```{bash}
docker network rm <net_name>
```