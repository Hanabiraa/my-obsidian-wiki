---
created: [" 19-03-2023 22:09"]
tags:
- article/
---

* [ADD](https://docs.docker.com/engine/reference/builder/#add) 
* [COPY](https://docs.docker.com/engine/reference/builder/#copy).

Both instructions copy new files or directories from the path to the filesystem of the image at the path:

```Dockerfile
ADD <src> <dest>

COPY <src> <dest>
```

Additionally, `ADD` can copy from remote file URLs (for example, it allows adding a git repository to an image directly) and directly from a compressed archive (`ADD` will automatically unpack the contents to the given location).

You should prefer `COPY` over `ADD` unless you specifically need one of the two additional features of ADD:
* downloading example files
* unpacking a compressed file

Examples of `ADD` and `COPY` instruction usage:

```Dockerfile
# copy local files on the host to the destination 
COPY /source/path  /destination/path 
COPY ./requirements.txt .  

# download external file and copy to the destination 
ADD http://external.file/url  /destination/path 
ADD --keep-git-dir=true https://github.com/moby/buildkit.git#v0.10.1 /buildkit  

# copy and extract local compresses files 
ADD source.file.tar.gz /destination/path`
```