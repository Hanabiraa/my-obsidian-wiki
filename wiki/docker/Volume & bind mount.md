---
created: [" 13-06-2023 19:21"]
aliases: []
tags:
- article/
---

# Volume & bind mount

## Volumes vs bind mount
- volumes ну зависят от хоста
- volumes безопаснее
- volumes находятся в специально отведенном месте (default `var/lib/docker/volumes`)
- volumes позволяют связывать только папки

### command `docker volume ls`
- List of all volumes

### create and remove volume(s)

```{bash}

docker volume create <vol_name>
```

```{bash}
docker volume rm <vol_name>
```

### Remove volumes not used by at least one container
```{bash}

docker volume prune
```

### Set volume for container - storage will be preserved between different container launches

```{bash}
docker run --rm -d -v <vol_name>:<dir_in_container> <image_name>
```

### Anonymous volumes
- Assigned only with id => can consume MUCH space
![[Pasted image 20230613192518.png]]

### command  `VOLUME <dir>`  
- инструкция в [[Dockerfile]] для добавления тома


## Bind mount

```{bash}

docker run --rm -d -v <abs_path_to_dir>:<dir_in_container_where_to_bind> <image_name>
```

Этой командой мы прокинули папку => синхронизируется ПАПКА (добавил в контейнере папку - она добавилась на компе)
