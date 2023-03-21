---
created: [" 22-03-2023 00:12"]
aliases: []
tags:
- article/
---

# Dockerfiles

## Use Multi-stage Builds

Take advantage of multi-stage builds to create leaner, more secure [[Docker image|Docker images]].

Multi-stage Docker builds allow you to break up your [[Dockerfile|Dockerfiles]] into several stages. For example, you can have a stage for compiling and building your application, which can then be copied to subsequent stages. Since only the final stage is used to create the image, the dependencies and tools associated with building your application are discarded, leaving a lean and modular production-ready image.

Web development example:

```Dockerfile
# temp stage
FROM python:3.9-slim as builder

WORKDIR /app

ENV PYTHONDONTWRITEBYTECODE 1
ENV PYTHONUNBUFFERED 1

RUN apt-get update && \
    apt-get install -y --no-install-recommends gcc

COPY requirements.txt .
RUN pip wheel --no-cache-dir --no-deps --wheel-dir /app/wheels -r requirements.txt


# final stage
FROM python:3.9-slim

WORKDIR /app

COPY --from=builder /app/wheels /wheels
COPY --from=builder /app/requirements.txt .

RUN pip install --no-cache /wheels/*
```

In this example, the [GCC](https://gcc.gnu.org/) compiler is required for installing certain Python packages, so we added a temp, build-time stage to handle the build phase. Since the final run-time image does not contain GCC, it's much lighter and more secure.

Size comparison:
![[Pasted image 20230322002200.png]]

Data science example:

```Dockerfile
# temp stage
FROM python:3.9 as builder

RUN pip wheel --no-cache-dir --no-deps --wheel-dir /wheels jupyter pandas


# final stage
FROM python:3.9-slim

WORKDIR /notebooks

COPY --from=builder /wheels /wheels
RUN pip install --no-cache /wheels/*
```

![[Pasted image 20230322002240.png]]

In summary, **multi-stage builds can decrease the size of your production images, helping you save time and money**. In addition, this will simplify your production containers. Also, due to the smaller size and simplicity, there's potentially a smaller attack surface.

## Order Dockerfile Commands Appropriately

_Pay close attention to the order of your Dockerfile commands to leverage layer caching._

[[wiki/docker/terms/Docker|Docker]] caches each step (or layer) in a particular [[Dockerfile]] to speed up subsequent builds. When a step changes, the cache will be invalidated not only for that particular step but all succeeding steps.

Example:

```Dockerfile
FROM python:3.9-slim

WORKDIR /app

COPY sample.py .

COPY requirements.txt .

RUN pip install -r /requirements.txt
```

In this [[Dockerfile]], **we copied over the application code _before_ installing the requirements.** **Now, each time we change _sample.py_, the build will reinstall the packages.** This is very inefficient, especially when using a [[wiki/docker/terms/Docker|Docker]] container as a development environment. Therefore, it's crucial to keep the files that frequently change towards the end of the [[Dockerfile]].

> You can also help prevent unwanted cache invalidations by using a _.dockerignore_ file to exclude unnecessary files from being added to the Docker build context and the final image. More on this here shortly.

So, in the above [[Dockerfile]], you should move the `COPY sample.py .` command to the bottom:

```Dockerfile
FROM python:3.9-slim

WORKDIR /app

COPY requirements.txt .

RUN pip install -r /requirements.txt

COPY sample.py .
```

Notes:

1.  Always put layers that are likely to change as low as possible in the [[Dockerfile]].
2.  Combine `RUN apt-get update` and `RUN apt-get install` commands. (This also helps to reduce the image size. We'll touch on this shortly.)
3.  If you want to turn off caching for a particular Docker build, add the `--no-cache=True` flag.

## ### Use Small Docker Base Images

_Smaller [[Docker image|Docker images]] are more modular and secure._

Building, pushing, and pulling images is quicker with smaller images. They also tend to be more secure since they only include the necessary libraries and system dependencies required for running your application.

_Which Docker base image should you use?_

Unfortunately, it depends.

Here's a size comparison of various Docker base images for Python:

![[Pasted image 20230322002547.png]]

While the Alpine flavor, based on [Alpine Linux](https://www.alpinelinux.org/), is the smallest, it can often lead to increased build times if you can't find compiled binaries that work with it. As a result, you may end up having to build the binaries yourself, which can increase the image size (depending on the required system-level dependencies) and the build times (due to having to compile from the source).

> Refer to [The best Docker base image for your Python application](https://pythonspeed.com/articles/base-image-python-docker-images/) and [Using Alpine can make Python Docker builds 50× slower](https://pythonspeed.com/articles/alpine-docker-python/) for more on why it's best to avoid using Alpine-based base images.

In the end, it's all about balance. When in doubt, start with a `*-slim` flavor, especially in development mode, as you're building your application. You want to avoid having to continually update the [[Dockerfile]] to install necessary system-level dependencies when you add a new Python package. As you harden your application and [[Dockerfile]](s) for production, you may want to explore using Alpine for the final image from a multi-stage build.

> Also, don't forget to update your base images regularly to improve security and boost performance. When a new version of a base image is released -- i.e., `3.9.6-slim` -> `3.9.7-slim` -- you should pull the new image and update your running containers to get all the latest security patches.

## Minimize the Number of Layers

It's a good idea to combine the RUN, COPY, and ADD commands as much as possible since they create layers. Each layer increases the size of the image since they are cached. Therefore, as the number of layers increases, the size also increases.

You can test this out with the docker history command:

![[Pasted image 20230322002928.png]]

Take note of the sizes. Only the `RUN`, `COPY`, and `ADD` commands add size to the image. You can reduce the image size by combining commands wherever possible. For example:

```Dockerfile
RUN apt-get update
RUN apt-get install -y netcat
```

Can be combined into a single `RUN` command:

```Dockerfile
RUN apt-get update && apt-get install -y netcat
```

**Thus, creating a single layer instead of two, which reduces the size of the final image.**

While it's a good idea to reduce the number of layers, it's much more important for that to be less of a goal in itself and more a side-effect of reducing the image size and build times. In other words, focus more on the previous three practices -- multi-stage builds, order of your [[Dockerfile]] commands, and using a small base image -- than trying to optimize every single command.

Notes:

1.  `RUN`, `COPY`, and `ADD` each create layers.
2.  Each layer contains the differences from the previous layer.
3.  Layers increase the size of the final image.

Tips:

1.  Combine related commands.
2.  Remove unnecessary files in the same RUN `step` that created them.
3.  Minimize the number of times `apt-get upgrade` is run since it upgrades all packages to the latest version.
4.  With multi-stage builds, don't worry too much about overly optimizing the commands in temp stages.

Finally, for readability, it's a good idea to sort multi-line arguments alphanumerically:

```Dockerfile
RUN apt-get update && apt-get install -y \
    git \
    gcc \
    matplotlib \
    pillow  \
    && rm -rf /var/lib/apt/lists/*
```

## Use Unprivileged Containers

By default, Docker runs container processes as root inside of a container. However, this is a bad practice since a process running as root inside the container is running as root in the Docker host. Thus, if an attacker gains access to your container, they have access to all the root privileges and can perform several attacks against the Docker host, like-

copying sensitive info from the host's filesystem to the container
executing remote commands
To prevent this, make sure to run container processes with a non-root user:

```Dockerfile
RUN addgroup --system app && adduser --system --group app

USER app
```

You can take it a step further and remove shell access and ensure there's no home directory as well:

```Dockerfile
RUN addgroup --gid 1001 --system app && \
    adduser --no-create-home --shell /bin/false --disabled-password --uid 1001 --system --group app

USER app
```

Verify:

```bash
$ docker run -i sample id

uid=1001(app) gid=1001(app) groups=1001(app)
```

Here, the application within the container runs under a non-root user. However, keep in mind, the [[dockerd|Docker daemon]] and the container itself is still running with root privileges. Be sure to review [Run the Docker daemon as a non-root user](https://docs.docker.com/engine/security/rootless/) for help with running both the daemon and containers as a non-root user.

## Cache Python Packages to the Docker Host

When a requirements file is changed, the image needs to be rebuilt to install the new packages. The earlier steps will be cached, as mentioned in [[#Minimize the Number of Layers|Minimize the Number of Layers]]. Downloading all packages while rebuilding the image can cause a lot of network activity and takes a lot of time. Each rebuild takes up the same amount of time for downloading common packages across builds.

You can avoid this by mapping the pip cache directory to a directory on the host machine. So for each rebuild, the cached versions persist and can improve the build speed.

Add a volume to the docker run as `-v $HOME/.cache/pip-docker/:/root/.cache/pip` or as a mapping in the Docker Compose file.

The directory presented above is only for reference. Make sure you map the cache directory and not the site-packages (where the built packages reside).

Moving the cache from the [[docker image]] to the host can save you space in the final image.

If you're leveraging Docker BuildKit, use BuildKit cache mounts to manage the cache:

```Dockerfile
# syntax = docker/dockerfile:1.2

...

COPY requirements.txt .

RUN --mount=type=cache,target=/root/.cache/pip \
        pip install -r requirements.txt

...
```

## Run Only One Process Per Container

_Why is it recommended to run only one process per container?_

Let's assume your application stack consists of a two web servers and a database. While you could easily run all three from a single container, you should run each in a separate container to make it easier to reuse and scale each of the individual services.

1.  **Scaling** - With each service in a separate container, you can scale one of your web servers horizontally as needed to handle more traffic.
2.  **Reusability** - Perhaps you have another service that needs a containerized database. You can simply reuse the same database container without bringing two unnecessary services along with it.
3.  **Logging** - Coupling containers makes logging much more complex. We'll address this in further detail later in this article.
4.  **Portability and Predictability** - It's much easier to make security patches or debug an issue when there's less surface area to work with.

## Prefer Array Over String Syntax

You can write the `CMD` and `ENTRYPOINT` commands in your [[Dockerfile|Dockerfiles]] in both array (exec) or string (shell) formats:

```Dockerfile
# array (exec)
CMD ["gunicorn", "-w", "4", "-k", "uvicorn.workers.UvicornWorker", "main:app"]

# string (shell)
CMD "gunicorn -w 4 -k uvicorn.workers.UvicornWorker main:app"
```

Both are correct and achieve nearly the same thing; however, you should use the exec format whenever possible. From the Docker documentation:

Make sure you're using the exec form of CMD and ENTRYPOINT in your [[Dockerfile]].
For example use ["program", "arg1", "arg2"] not "program arg1 arg2". Using the string form causes [[wiki/docker/terms/Docker|Docker]] to run your process using bash, which doesn't handle signals properly. Compose always uses the JSON form, so don't worry if you override the command or entrypoint in your Compose file.
So, since most shells don't process signals to child processes, if you use the shell format, CTRL-C (which generates a SIGTERM) may not stop a child process.

Example:

```Dockerfile
FROM ubuntu:18.04

# BAD: shell format
ENTRYPOINT top -d

# GOOD: exec format
ENTRYPOINT ["top", "-d"]
```

Try both of these. Take note that with the shell format flavor, `CTRL-C` won't kill the process. Instead, you'll see `^C^C^C^C^C^C^C^C^C^C^C`.

Another caveat is that the shell format carries the PID of the shell, not the process itself.

![[Pasted image 20230322003723.png]]

## Understand the Difference Between ENTRYPOINT and CMD

_Should I use ENTRYPOINT or CMD to run container processes? see [[Difference between CMD and ENTRYPOINT]]_

There are two ways to run commands in a container:

```Dockerfile
CMD ["gunicorn", "config.wsgi", "-b", "0.0.0.0:8000"]

# and

ENTRYPOINT ["gunicorn", "config.wsgi", "-b", "0.0.0.0:8000"]
```

Both essentially do the same thing: Start the application at `config.wsgi` with a Gunicorn server and bind it to `0.0.0.0:8000`.

The `CMD` is easily overridden. If you run `docker run <image_name> uvicorn config.asgi`, the above CMD gets replaced by the new arguments -- e.g., `uvicorn config.asgi`. Whereas to override the `ENTRYPOINT` command, one must specify the `--entrypoint` option:


`docker run --entrypoint uvicorn config.asgi <image_name>`

Here, it's clear that we're overriding the entrypoint. So, it's recommended to use `ENTRYPOINT` over `CMD` to prevent accidentally overriding the command.

They can be used together as well.

For example:

```Dockerfile
ENTRYPOINT ["gunicorn", "config.wsgi", "-w"]
CMD ["4"]
```

When used together like this, the command that is run to start the container is:

`gunicorn config.wsgi -w 4`

As discussed above, `CMD` is easily overridden. Thus, `CMD` can be used to pass arguments to the `ENTRYPOINT` command. The number of workers can be easily changed like so:

`docker run <image_name> 6`

This will start the container with six Gunicorn workers rather then four.

# Images

## Version Docker Images

Whenever possible, avoid using the `latest` tag.

If you rely on the `latest` tag (which isn't really a "tag" since it's applied by default when an image isn't explicitly tagged), you can't tell which version of your code is running based on the image tag. It makes it challenging to do rollbacks and makes it easy to overwrite it (either accidentally or maliciously). Tags, like your infrastructure and deployments, should be [immutable](https://sysdig.com/blog/toctou-tag-mutability/).

Regardless of how you treat your internal images, you should never use the `latest` tag for base images since you could inadvertently deploy a new version with breaking changes to production.

For internal images, use descriptive tags to make it easier to tell which version of the code is running, handle rollbacks, and avoid naming collisions.

For example, you can use the following descriptors to make up a tag:

1.  Timestamps
2.  [[Docker image]] IDs
3.  Git commit hashes
4.  Semantic version

> For more options, check out [this answer on stackoverflow](https://stackoverflow.com/a/56213290/1799408) from the "Properly Versioning [[Docker image|Docker Images]]" Stack Overflow question.

For example:

`docker build -t web-prod-a072c4e5d94b5a769225f621f08af3d4bf820a07-0.1.4 .`

Here, we used the following to form the tag:

1.  Project name: `web`
2.  Environment name: `prod`
3.  Git commit hash: `a072c4e5d94b5a769225f621f08af3d4bf820a07`
4.  Semantic version: `0.1.4`

It's essential to pick a tagging scheme and be consistent with it. Since commit hashes make it easy to tie an image tag back to the code quickly, it's highly recommended to include them in your tagging scheme.

## Don't Store Secrets in Images

_Secrets are sensitive pieces of information such as passwords, database credentials, SSH keys, tokens, and TLS certificates, to name a few. These should not be baked into your images without being encrypted since unauthorized users who gain access to the image can merely examine the layers to extract the secrets._

Do not add secrets to your Dockerfiles in plaintext, especially if you're pushing the images to a public registry like [Docker Hub](https://hub.docker.com/):

```Dockerfile
FROM python:3.9-slim  ENV DATABASE_PASSWORD "SuperSecretSauce"
```

Instead, they should be injected via:

1.  Environment variables (at run-time)
2.  Build-time arguments (at build-time)
3.  An orchestration tool like Docker Swarm (via Docker secrets) or Kubernetes (via Kubernetes secrets)

Also, you can help prevent leaking secrets by adding common secret files and folders to your _.dockerignore_ file:

```
**/.env
**/.aws
**/.ssh
```

Finally, be explicit about what files are getting copied over to the image rather than copying all files recursively:

```Dockerfile
# BAD
COPY . .

# GOOD
copy ./app.py .
```

Being explicit also helps to limit cache-busting.

### Environment Variables

You can pass secrets via environment variables, but they will be visible in all child processes, linked containers, and logs, as well as via `docker inspect`. It's also difficult to update them.

![[Pasted image 20230322004524.png]]

This is the most straightforward approach to secrets management. While it's not the most secure, it will keep the honest people honest since it provides a thin layer of protection, helping to keep the secrets hidden from curious wandering eyes.

Passing secrets in using a shared volume is a better solution, but they should be encrypted, via [Vault](https://www.vaultproject.io/) or [AWS Key Management Service](https://aws.amazon.com/kms/) (KMS), since they are saved to disc.

### Build-time Arguments

You can pass secrets in at build-time using [build-time arguments](https://docs.docker.com/engine/reference/commandline/build/#set-build-time-variables---build-arg), but they will be visible to those who have access to the image via `docker history`.

Example:

```Dockerfile
FROM python:3.9-slim

ARG DATABASE_PASSWORD
```

Build:

```bash
$ docker build --build-arg "DATABASE_PASSWORD=SuperSecretSauce" .
```

If you only need to use the secrets temporarily as part of the build -- i.e., SSH keys for cloning a private repo or downloading a private package -- you should use a multi-stage build since the **builder history is ignored for temporary stages**:

```Dockerfile
# temp stage
FROM python:3.9-slim as builder

# secret
ARG SSH_PRIVATE_KEY

# install git
RUN apt-get update && \
    apt-get install -y --no-install-recommends git

# use ssh key to clone repo
RUN mkdir -p /root/.ssh/ && \
    echo "${PRIVATE_SSH_KEY}" > /root/.ssh/id_rsa
RUN touch /root/.ssh/known_hosts &&
    ssh-keyscan bitbucket.org >> /root/.ssh/known_hosts
RUN git clone git@github.com:testdrivenio/not-real.git


# final stage
FROM python:3.9-slim

WORKDIR /app

# copy the repository from the temp image
COPY --from=builder /your-repo /app/your-repo

# use the repo for something!
```

The multi-stage build only retains the history for the final image. Keep in mind that you can use this functionality for permanent secrets that you need for your application, like a database credential.

You can also use the new `--secret` option in Docker build to pass secrets to [[Docker image|Docker images]] that do not get stored in the images.

```Dockerfile
# "docker_is_awesome" > secrets.txt

FROM alpine

# shows secret from default secret location:
RUN --mount=type=secret,id=mysecret cat /run/secrets/mysecret
```

This will mount the secret from the `secrets.txt` file.

Build the image:

![[Pasted image 20230322004851.png]]

Finally, check the history to see if the secret is leaking:

![[Pasted image 20230322004906.png]]

> For more on build-time secrets, review [Don't leak your [[Docker image]]'s build secrets](https://pythonspeed.com/articles/docker-build-secrets/).

## Use a .dockerignore File

We've mentioned using a [_.dockerignore_ file](https://docs.docker.com/engine/reference/builder/#dockerignore-file) a few times already. This file is used to specify the files and folders that you don't want to be added to the initial build context sent to the [[dockerd|Docker daemon]], which will then build your image. Put another way, you can use it to define the build context that you need.

When a Docker image is built, the entire Docker context -- i.e., the root of your project -- is sent to the [[wiki/docker/terms/Docker|Docker]] daemon _before_ the `COPY` or `ADD` commands are evaluated. This can be pretty expensive, especially if you have many dependencies, large data files, or build artifacts in your project. Plus, the Docker CLI and daemon may not be on the same machine. So, if the daemon is executed on a remote machine, you should be even more mindful of the size of the build context.

What should you add to the _.dockerignore_ file?

1.  Temporary files and folders
2.  Build logs
3.  Local secrets
4.  Local development files like _docker-compose.yml_
5.  Version control folders like ".git", ".hg", and ".svn"

Example:

```
**/.git
**/.gitignore
**/.vscode
**/coverage
**/.env
**/.aws
**/.ssh
Dockerfile
README.md
docker-compose.yml
**/.DS_Store
**/venv
**/env
```

In summary, a properly structured _.dockerignore_ can help:

1.  Decrease the size of the [[Docker image]]
2.  Speed up the build process
3.  Prevent unnecessary cache invalidation
4.  Prevent leaking secrets

## Lint and Scan Your Dockerfiles and Images

Linting is the process of checking your source code for programmatic and stylistic errors and bad practices that could lead to potential flaws. Just like with programming languages, static files can also be linted. With your Dockerfiles specifically, linters can help ensure they are maintainable, avoid deprecated syntax, and adhere to best practices. Linting your images should be a standard part of your CI pipelines.

[Hadolint](https://github.com/hadolint/hadolint) is the most popular Dockerfile linter.

You can see it in action online at [https://hadolint.github.io/hadolint/](https://hadolint.github.io/hadolint/). There's also a [VS Code Extension](https://marketplace.visualstudio.com/items?itemName=exiasr.hadolint).

You can couple linting your Dockerfiles with scanning images and containers for vulnerabilities.

Some options:

1.  [Snyk](https://docs.docker.com/engine/scan/) is the exclusive provider of native vulnerability scanning for [[wiki/docker/terms/Docker|Docker]]. You can use the `docker scan` CLI command to scan images.
2.  [Trivy](https://aquasecurity.github.io/trivy/) can be used to scan container images, file systems, git repositories, and other configuration files.
3.  [Clair](https://github.com/quay/clair) is an open-source project used for the static analysis of vulnerabilities in application containers.
4.  [Anchore](https://github.com/anchore/anchore-engine) is an open-source project that provides a centralized service for inspection, analysis, and certification of container images.

In summary, lint and scan your [[Dockerfile|Dockerfiles]] and images to surface any potential issues that deviate from best practices.

## Sign and Verify Images

_How do you know that the images used to run your production code have not been tampered with?_

Tampering can come over the wire via [man-in-the-middle](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) (MITM) attacks or from the registry being compromised altogether.

[Docker Content Trust](https://docs.docker.com/engine/security/trust/) (DCT) enables the signing and verifying of [[Docker image|Docker images]] from remote registries.

To verify the integrity and authenticity of an image, set the following environment variable:

```
DOCKER_CONTENT_TRUST=1
```

Now, if you try to pull an image that hasn't been signed, you'll receive the following error:

![[Pasted image 20230322005422.png]]

You can learn about signing images from the [Signing Images with Docker Content Trust](https://docs.docker.com/engine/security/trust/#signing-images-with-docker-content-trust) documentation.

When downloading images from Docker Hub, make sure to use either [official images](https://docs.docker.com/docker-hub/official_images/) or verified images from trusted sources. Larger teams should look to using their own internal [private container registry](https://docs.docker.com/registry/deploying/).

# Conclusion

This article looked at several best practices to make your [[Dockerfile|Dockerfiles]] and images cleaner, leaner, and more secure.

Additional Resources:

1.  [Docker development best practices](https://docs.docker.com/develop/dev-best-practices/)
2.  [Best practices for writing Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)