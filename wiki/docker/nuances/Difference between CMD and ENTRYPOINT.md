---
created: [" 19-03-2023 22:06"]
tags:
- article/
---

* [CMD](https://docs.docker.com/engine/reference/builder/#cmd)
* [ENTRYPOINT](https://docs.docker.com/engine/reference/builder/#entrypoint).

First, for the similarities:

-   `CMD` and `ENTRYPOINT` both specify a command / an executable that will be executed when running a container. Unlike `RUN`, which executes the command right away (the result is used in the image layer), the `CMD`/`ENTRYPOINT` command in the build-up specifies the command that will be used only when the container starts.
-   You can have only one `CMD`/`ENTRYPOINT` instruction in a [[Dockerfile]], but it can point to a more complicated executable file.

There's actually only one difference between those instructions:

-   `CMD` **can easily be overridden from Docker CLI**.

You should use `CMD` if you want the flexibility to run different executables depending on your needs when starting the container. If you want to clearly communicate that command is not meant to be overridden and prevent accidentally changing it, use `ENTRYPOINT`.

You'll probably use one or the other. If you don't use one, the container will be stopped immediately since there's no reason for it to exist (the exception being if you're also using Docker Compose).

You may also use both `CMD` and `ENTRYPOINT` in the same [[Dockerfile]], in which case `CMD` serves as the default argument for the `ENTRYPOINT`.

You can have only one `CMD` instruction in a [[Dockerfile]], but it can point to a more complicated executable file. If you have more than one `CMD`, only the last `CMD` will take effect. The same goes for the `ENTRYPOINT` instruction.

Example of `CMD` instruction usage:

```Dockerfile
CMD gunicorn core.wsgi:application --bind 0.0.0.0:$PORT
```

There's a big chance you'll see the `ENTRYPOINT` argument as an executable file since commands that should be executed are often more than a one-liner.

Example of `ENTRYPOINT` as executable file usage:

```Dockerfile
ENTRYPOINT ["./entrypoint.sh"]
```

And this is what the _entrypoint.sh_ file might look like:

```bash
#!/bin/sh

python manage.py migrate 
python manage.py collectstatic --noinput`

```

### Table of different `CMD`/ `ENTRYPOINT` combinations:

![[Pasted image 20230328183513.png]]

> It's important to understand the difference between `CMD` and `ENTRYPOINT`. For more, check out [official docs](https://docs.docker.com/engine/reference/builder/#understand-how-cmd-and-entrypoint-interact).

