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

***hint:*** when app is running but you have changed `.yaml` file, you can update containers by running `docker-compose run -d` again without stopping existing containers - this will update changed containers

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
	- keys are services names
- `volumes` - volumes
	- can define relative pat
	- can explicitly define name, otherwise name will be assigned from directory name:
		![[Pasted image 20230617123727.png]]
	Some instructions with its analogous from single container `docker run` command:

| **docker-compose.yaml keyword** | **docker run container command**                     |
| ------------------------------- | ---------------------------------------------------- |
| build                           | docker build -f \<Dockerfile path>                   |
| image                           | docker build \<image>                                |
| container_name                  | docker run --name \<contaner name>                   |
| volumes                         | docker run -v <host_path/volume>/<path_in_container> |
| environment                     | docker run -e <VARIABLE_NAME>=\<value>               |
| networks                        | docker run --net<network_name>                       |
| ports - port_1:port_2                           | docker run -p <host_port>:<port_in_container>        |

**new instructions**:
	- `restart`, `restart_policy` - actions in case of container failure
	- `deploy: replicas: N` - number of instances of container
	- `depends_on` - dependency of container from another (e.g. backend should run after database start and not vice-versa)
	- `healthcheck` - run sanity-check of container work after start

- `networks` - networks used
	- same name definition as with volumes:
		![[Pasted image 20230617123816.png]]
	- custom networks are isolated from each other,  and if we don't define `networks` keyword, by default the network name will be `<dir name where docker-compose.yaml stays>_default` => apps from different directories are isolated