---
created: [" 14-06-2023 01:08"]
aliases: []
tags:
- article/
---

# Ports in container

Some programs need ports
![[Pasted image 20230614010847.png]]


In container:
![[Pasted image 20230614011139.png]]

## Map ports on host with ports in container

`docker run -p <PORT_ON_HOST>:<PORT_ON_CONTAINER>`

- can map multiple ports *(if program in container can listen to multiple ports of course*):
`docker run -p 80:80 -p 443:443 some_web_image`


or can add ip-address:

`docker run -p 127.0.0.1:80:80`, by default ip is 0.0.0.0

## One host port - one container/program

## Expose ports in [[Dockerfile]] - `EXPOSE <PORT> [<PORT>/protocol]`
- container will listen on the specified network port at runtime

## Discover which port container is listening (in case you didn't use `EXPOSE` command...)

1. Enter container with bash: `docker exec -it <container> bash`
2. Check status of network: `netstat -tuplen`:
	![[Pasted image 20230615003718.png]]

	and you can see the port!