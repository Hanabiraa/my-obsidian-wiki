---
created: [" 19-03-2023 22:27"]
tags:
- article/
---

# Introduction

The  concept you need to understand is a [container](https://docs.docker.com/glossary/#container), which is a controlled environment for your application. An [[Docker image|image]] becomes a container when it's run on [[Docker Engine]]. It's the end goal: You use [[wiki/docker/terms/Docker|Docker]] so you can have a container for your application.

The main operations you can do with a container are

1.  **running** a container
2.  **listing** all the containers
3.  **stopping** a container
4.  **removing** a container

> You can see all the commands related to a container in the [official docs](https://docs.docker.com/engine/reference/commandline/container/).

## Running

You can either create a new container of an image and **run** it, or you can **start** an existing container that was previously stopped.

### Run

The [docker container run](https://docs.docker.com/engine/reference/commandline/container_run/) command actually combines two other commands, [docker container create](https://docs.docker.com/engine/reference/commandline/container_create/) and [docker container start](https://docs.docker.com/engine/reference/commandline/container_start/).

So, the following essentially gives you the same result:

![[Pasted image 20230319223122.png]]

You need to provide one argument: The [[Docker image|image]] you want to use for the [[Docker container|container]].

When you run the run command, [[wiki/docker/terms/Docker|Docker]] creates a writable [[Docker container|container]] layer over the specified [[Docker image|image]] and then starts it using the specified command (`CMD`/`ENTRYPOINT` in the [[Dockerfile]]).

> The changes made in the writable layer do not persist after you delete the [[Docker container|container]], unless you store it. [[wiki/docker/terms/Docker|Docker]] has two [options for storing data](https://docs.docker.com/storage/).

Since you can override a number of the defaults, there are many options. You can see all of them in the [official docs](https://docs.docker.com/engine/reference/commandline/run/#options). The most important option is `--publish`/`-p`, which is used to publish ports to the outside world. Although it is technically possible to run a container without a port, it's not very useful since the service(s) running inside the container wouldn't be accessible outside the container. You can use `--publish`/`-p` for both the create and run commands:

Here's an example of how that looks like:

```bash
$ docker container run -p 8000:8000 my_image
```

> You can read more on port publishing in the [official docs](https://docs.docker.com/config/containers/container-networking/#published-ports).

You can run your container in detached mode by using `--detach`/`-d`, which lets you keep using the terminal.

If you run a container in detached mode, [[wiki/docker/terms/Docker|Docker]] will just return the container ID:

```bash
$ docker container run -p 8000:8000 -d my_image  0eb20b715f42bc5a053dc7878b3312c761058a25fc1efaffb7920b3b4e48df03
```

Your container gets a unique, quirky name by default, but you can assign your own name:

```bash
$ docker container run -p 8000:8000 --name my_great_container my_image
```

### Start

To start a stopped or just created container, you use the [docker container start](https://docs.docker.com/engine/reference/commandline/container_start/) command. Since with this command, you're starting an existing container, you have to specify the _container_ instead of an _image_ (as with `docker container run`).

Another difference from `docker container run` is that `docker container start` by default runs the container in the detached mode. You can attach it using `--attach`/`-a` (reverse from `docker container run -d`).

Example:

```bash
$ docker container start -a reverent_sammet
```

## Listing

You can list all running containers with [docker container ls](https://docs.docker.com/engine/reference/commandline/container_ls/).

Example:

![[Pasted image 20230319223547.png]]

If you want to also see the stopped containers, you can add the `-a` flag:

![[Pasted image 20230319223600.png]]

Let's take a look at the output for:

![[Pasted image 20230319223613.png]]

1.  `CONTAINER ID` (`73bd69d041ae`) and its `NAMES` (`my_great_container`) are both unique, so you can use them to access the container.
2.  `IMAGE` (`my_image`) tells you which image was used to run the container.
3.  `CREATED` is pretty self-explanatory: when the container was created (`2 hours ago`).
4.  We already discussed the need for specifying a command for starting a container... `COMMAND` tells you which command was used (`"/bin/sh -c 'uvicorn…"`).
5.  `STATUS` is useful when you don't know why your container isn't working (`Up 2 hours` means your container is running, `Exited` or `Created` means it's not)

> Some of the information is truncated. If you want the untruncated version, add `--no-trunc`.

## Stopping

To stop a container, use [docker container stop](https://docs.docker.com/engine/reference/commandline/container_stop/). The name or ID of the stopped container is then returned.

Example:

```bash
$ docker container stop my_great_container my_great_container  

$ docker container stop 73bd69d041ae 73bd69d041ae`
```

> A container can be started again with `docker container start`.

## Removing

Similar to images, to remove a container, you can either:

1.  remove one or more selected containers via [docker container rm](https://docs.docker.com/engine/reference/commandline/container_rm/).
2.  remove all stopped containers via [docker container prune](https://docs.docker.com/engine/reference/commandline/container_prune/)

Example of `docker container rm`:

```bash
$ docker container rm festive_euclid festive_euclid
```

Example of `docker container prune`:

![[Pasted image 20230319223717.png]]