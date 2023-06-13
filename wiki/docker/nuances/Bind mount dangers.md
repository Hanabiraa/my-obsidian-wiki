---
created: [" 13-06-2023 23:35"]
aliases: []
tags:
- article/
---

# Bind mount dangers

## By default container is root - work in container can alter crucial files on a machine


## How to solve this

- don't bind root
- try to bind particular files
- read-only **ro** option : `docker run -v <host_path>:<container_path>:ro <image>`
- use `USER ..` in Dockerfile, `--user ...` in container