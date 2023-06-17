---
created: [" 16-06-2023 23:47"]
aliases: [docker compose, docker-compose, Docker compose]
tags:
- article/
---

# Docker-compose

## Definition
- tool for launching and orchestrating multi-container app
- contains the description of the app launch
- all logic is described in `docker-compose.yml`/`docker-compose.yaml`

## Basic commands
To start app, you need to be in the directory with compose `.yaml` file:
### start the app
`docker-compose up`
- `<container>` - particular container
- `-d` - detached mode
- `-f docker-compose.dev.yml` - set `docker-compose.yaml` file to be used for building

### list of working containers
`docker-compose ps`

### stop working containers
`docker-compose stop`

### stop and remove running containers
`docker-compose down`
- by default doesn't remove volumes, but with option `--volumes` can 

### start stopped containers
`docker-compose start`


## some keywords in `docker-compose.yaml`

- `version: ` 
- `services: ` - containers
- `volumes` - volumes
	- can explicitly define name, otherwise name will be assigned from directory name:
		![[Pasted image 20230617123727.png]]
- `networks` - networks used
	- same name definition as with volumes:
		![[Pasted image 20230617123816.png]]
	- custom networks are isolated from each other,  and if we don't define `networks` keyword, by default the network name will be `<dir name where docker-compose.yaml stays>_default` => apps from different directories are isolated