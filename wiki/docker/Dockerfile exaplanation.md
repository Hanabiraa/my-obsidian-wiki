---
created: [" 19-03-2023 21:56"]
tags:
- article/
---

# Introduction

a [[Dockerfile]] is a text file that contains instructions for [[wiki/docker/terms/Docker|Docker]] on how to build an image. By default, a [[Dockerfile]] has no extension, but you can add one if you need more than one -- e.g., _Dockerfile.prod_.

Here's an example of a very simple [[Dockerfile]]:
```Dockerfile
FROM python:3.10-slim-buster

WORKDIR /usr/src/app

ENV PYTHONDONTWRITEBYTECODE 1
ENV PYTHONUNBUFFERED 1

RUN pip install --upgrade pip
COPY ./requirements.txt .
RUN pip install -r requirements.txt

COPY . .

CMD uvicorn main:app --reload --host 0.0.0.0 --port 8000
```

> This example is as simple as can be, so you can easily understand it

A [[Dockerfile]] is essentially a list of [commands](https://docs.docker.com/engine/reference/builder) in the following form: `INSTRUCTION arguments`. The majority of the most widely used commands can be seen in the above [[Dockerfile]]. Let's look at each of them in detail...

## Some of dockerfile instructions

### From

All Dockerfiles include a parent/base image on which the new image will be built. You use the [FROM](https://docs.docker.com/engine/reference/builder/#from) instruction to define the parent image:

```Dockerfile
FROM python:3.10-slim-buster`
```

A valid [[Dockerfile]] always includes a `FROM` instruction.

> Although _parent_ and _base_ image terms are sometimes used interchangeably, there's a difference between them. A parent image has its own parent image. Meanwhile, a base image has no parent; it starts with `FROM scratch`.
> 
> [Alpine image](https://github.com/alpinelinux/docker-alpine/blob/adebcfd075c83ef788b5f071678dcfb8ea118bb3/x86_64/Dockerfile) is a base image and [Python:alpine](https://github.com/docker-library/python/blob/9fd031d91ced6ddc1bf1f0f34b893ad82fa0d010/3.11-rc/alpine3.16/Dockerfile) is a parent image (whose parent (base) image is actually the alpine image).
> 
> It's possible to create a [base image on your own](https://docs.docker.com/develop/develop-images/baseimages/), but the probability of you needing your own image is small.

You can find parent images on [Docker Hub](https://docs.docker.com/docker-hub/), which is Docker's library/registry for [[Docker image|Docker images]]. You can think of it as GitHub for [[Docker image|Docker images]]. You'll probably want to use either [official images](https://docs.docker.com/docker-hub/official_images/) or verified images from trusted sources since they're more likely to adhere to [Docker best practices](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) and contain the latest security fixes.

In the example above, we used the official [Python parent image](https://hub.docker.com/_/python), specifically `python:3.10-slim-buster`.

Notes about `python:3.10-slim-buster`:

-   The number tells you which version of the technology the image uses (e.g., the `python:3.11.0a5` image uses Python version _3.11.0a5_ while `node:18.9.0` uses Node version _18.9.0_). You'll probably want to avoid any versions with `rc` in it (e.g., `python:3.11.0rc2`) since rc means _release candidate_.
-   Names like _buster_, _bullseye_, or _alpine_ tell you which OS images were used for this image (_buster_ and _bullseye_ refer to Debian releases while _alpine_ is a lightweight Linux distribution). Additionally, there are tags such as `slim` and `slim-buster` that use lighter versions of the full image.

### RUN

The [RUN](https://docs.docker.com/engine/reference/builder/#run) instruction executes any commands in a new layer on top of the current [[Docker image|image]] and commits the result.

Examples:

```Dockerfile
RUN mkdir /home/app/web  

RUN python manage.py collectstatic --noinput`
```

### ENV

You use the [ENV](https://docs.docker.com/engine/reference/builder/#env) instruction to set an environment variable. This variable will be available in all subsequent instructions.

Examples:

```Dockerfile
ENV TZ=UTC

ENV HOME=/home/app
```

### CMD and ENTRYPOINT

see [[Difference between CMD and ENTRYPOINT]]

### ADD and COPY

see [[Difference between ADD and COPY]]

### Another commands

see [official docs](https://docs.docker.com/engine/reference/builder/)


## Multi-stage builds
- build image on the top of already built image - save space and get rid of redundant packages/dependencies

![[Pasted image 20230615162444.png]]

- only last stage layers will be kept in image
- to build only current stage, use `--target <BUILD_NAME>` option for `docker build`