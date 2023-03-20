---
created: [" 19-03-2023 22:15"]
tags:
- article/
---

# Introduction

An [[Docker image|image]] might be the most confusing concept of the three. You create a [[Dockerfile]] and then use a container, but an image lies between those two.

So, an image is a read-only embodiment of a [[Dockerfile]] that's used to create a [[Docker container]]. It consists of [layers](https://docs.docker.com/glossary/#layer) -- each line in a [[Dockerfile]] makes one layer. You can't change an image directly; you change it by changing the [[Dockerfile]]. You don't directly use an image either; you use a container created from an image.

The most important image-related tasks are:

1.  **building** images from Dockerfiles
2.  **listing** all the built images
3.  **removing** images

> [Since 2017](https://github.com/docker/docker.github.io/issues/3031), [[wiki/docker/terms/Docker|Docker]] switched from shorter commands (i.e., `docker <command>`) to a more descriptive format (i.e., `docker <top-level command> <command>`). Despite Docker users being encouraged to use the new version, even the [official tutorial](https://docs.docker.com/get-started/02_our_app/) uses the shorter version. As of now, the old version still works, and I have not been able to find any proof of the old commands getting deprecated. What's more, people (and even the [documentation](https://docs.docker.com/engine/reference/commandline/create/#description)) started referring to it as a 'shorthand'.
> 
> The advantage of using the new version is that you'll better understand which of the three concepts the command is dealing with. The commands are also easier to find in the docs. The advantage of the old version is that it's shorter, and the documentation is more comprehensive.
> 
> I'll be using the descriptive form of commands in this article. At the end of the article, you can find all the commands with their shorthand version.
> 
> [Here](https://docs.docker.com/engine/reference/commandline/image/), you can find all the new-form commands that deal with images.

## Building

To build an image from a [[Dockerfile]], you use the [docker image build](https://docs.docker.com/engine/reference/commandline/image_build/) command. This command requires one argument: either a path or URL of the context.

This image will use the current directory as a context:

```bash
$ docker image build .
```

There are a number of [options](https://docs.docker.com/engine/reference/commandline/image_build/#options) you can provide. For example, `-f` is used to specify a specific [[Dockerfile]] when you have multiple Dockerfiles (e.g., `Dockerfile.prod`) or when the `Dockerfile` isn't in the current directory (e.g., `docker image build . -f docker/Dockerfile.prod`).

Probably the most important is the `-t` tag that is used to name/tag an image.

When you build an image, it gets assigned an ID. Contrary to what you might expect, the IDs are not unique. If you want to be able to easily reference your image, you should name/tag it. With `-t`, you can assign a name _and_ a tag to it.

Here's an example of creating three images: one without the usage of `-t`, one with a name assigned, and one with a name and a tag assigned.

![[Pasted image 20230319222307.png]]

Notes:

1.  For the image that was built without a name or tag, you can only reference it via its image ID. Not only is it difficult to remember, but, again, it might not be unique (which is the case above). You should avoid this.
2.  For the image that only has a name (`-t hello_world`), the tag is automatically set to `latest`. You should avoid this as well. For more, review [Version Docker Images](https://testdriven.io/blog/docker-best-practices/#version-docker-images).

## Listing

The [docker image ls](https://docs.docker.com/engine/reference/commandline/image_ls/) command lists all built images.

![[Pasted image 20230319222409.png]]

## Removing

There are two use cases for removing images:

1.  You want to remove one or more selected images
2.  You want to remove all the unused images (you don't care which images specifically)

For the first case, you use `docker image rm`; for the second, you use `docker image prune`.

#### Remove

[docker image rm](https://docs.docker.com/engine/reference/commandline/image_rm/) removes and untags the selected image(s). It requires one argument: The reference to the image(s) you want to remove. You can reference it by name or short/long ID.

If you think back to the explanation of image tagging... there can be multiple images with a different name but the same ID. If you try to remove the image by image ID and multiple images with that ID exist, you'll get an `image is referenced in multiple repositories` error. In that case, you'll have to remove it by referencing it by name. If you wish to remove all images with the same ID, you can use the `-f` flag.

Example of unsuccessful and successful image removal:

![[Pasted image 20230319222521.png]]

#### Prune

[docker image prune](https://docs.docker.com/engine/reference/commandline/image_prune/) removes dangling images. Since `prune` is a command that can be used for cleaning containers, images, volumes, and networks, this command doesn't have a shorter version. If you use the `-a` flag, all unused images are removed (i.e., `docker image prune -a`).

> A dangling image is an image that is not tagged and is not referenced by any container.
> 
> An unused image is an image that isn't associated with at least one container.

Example:

![[Pasted image 20230319222605.png]]