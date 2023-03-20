---
created: [" 07-02-2023 17:17"]
tags:
- article/
---

# Introduction

## Objectives

By the end of this article, you should be able to:

1.  Explain what [[wiki/docker/terms/Docker]] is and how it works
2.  Describe and differentiate between the following concepts and components: [[Docker Engine]], Docker Desktop, [[Dockerfile]], [[Docker image]], and [[Docker container]]
3.  Follow more complicated tutorials that use [[wiki/docker/terms/Docker]]

## Containers and Virtual Machines

Before jumping into [[wiki/docker/terms/Docker]], it's important to understand the difference between containers and virtual machines.

Containers and virtual machines are similar in that they allow multiple apps to run on the same server with various software requirements -- e.g., different Python versions, different libraries, etc. **Their main difference is in the operating system**. While containers uses the host's operating system, each virtual machine has its own guest operating system on top of the host's operating system.

In a now almost famous image, you can see how Docker compares to virtual machines:

![[Pasted image 20230319151529.png]]

> if you have an application that needs to run on different operating systems, a virtual machine is the way to go

But if that's not a requirement, [[wiki/docker/terms/Docker]] has multiple advantages over a virtual machine:
* Lighter-weight
* Faster to build
* Can be easily ported across different platforms
* Less resource intensive
* Scaling up and duplicating is easier

# Docker

## Docker Engine

When people refer to [[wiki/docker/terms/Docker]], they're typically referring to [[Docker Engine]]

[[Docker Engine]] is the underlying open source containerization technology for building, managing, and running containerized applications. It's a client-server application with the following components:

1.  [[dockerd|Docker daemon or dockerd]]
2.  [Docker Engine API](https://docs.docker.com/engine/api/latest/) is a RESTful API that's used to interact with [[dockerd|Docker daemon]].
3.  [[wiki/docker/terms/Docker|Docker client]] (called _[[wiki/docker/terms/Docker]]_)

## Docker Desktop

These days, when you try to install Docker, you'll come across [Docker Desktop](https://www.docker.com/products/docker-desktop/). While [[Docker Engine]] is included with Docker Desktop, it's important to understand that **Docker Desktop is _not_ the same as [[Docker Engine]]**. 

> Docker Desktop is an integrated development environment for [[wiki/docker/terms/Docker]] containers. It makes it much easier to get your operating system configured for working with Docker.

If you haven't already, go ahead and install Docker Desktop:

-   [Linux](https://docs.docker.com/desktop/install/linux-install/)
-   [MacOS](https://docs.docker.com/desktop/install/mac-install/)
-   [Windows](https://docs.docker.com/desktop/install/windows-install/)

### Docker Concepts

At the heart of [[wiki/docker/terms/Docker]], there are three core concepts:

1. [[Dockerfile]]
2.  [[Docker Image]]
3.  [[Docker container]]

![[Pasted image 20230319160258.png]]

> _A [[Dockerfile]] is used to create a [[Docker image]], which is then used to create (multiple) [[Docker container|Docker containers]]._