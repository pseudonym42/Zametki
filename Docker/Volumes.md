# How to use volumes?

---

There are two types of data storage for docker - volumes and bind mounts.

Docker data storage are like "external drives" for your containers - they can be used to pass files
to and from host machines, also they can be used to exchange files between different containers.

They are useful in situations where you can't use links (which are TCP/IP only)

Note that they don't have to be connected to a particular container.

What is the difference between them? Extract from docker official docs:

> Bind mounts have been around since the early days of Docker. Bind mounts have
limited functionality compared to volumes. When you use a bind mount, a file or
directory on the host machine is mounted into a container. The file or directory
is referenced by its full or relative path on the host machine. By contrast, when
you use a volume, a new directory is created within Docker’s storage directory on
the host machine, and Docker manages that directory’s contents.

You create either of them via `-v` or `--mount` flags when running a container:

#### Using `--mount`:

Create bind mount:
```
docker run  \
    --mount type=bind,source=/home/user,destination=/root,readonly \
    nginx
```

Create volume:
```
docker run \
    --mount type=volume,source=my_volume,destination=/root \
    nginx
```

#### Using `-v`:

Create bind mount:
```
docker run \
    -v /home/user:/root:ro \
    nginx
```

Create volume:
```
docker run \
    -v my_volume:/root:ro \
    nginx
```

Useful volume commands:

```
docker volume create my_volume          -> create volume
docker volume ls                        -> list all volumes
docker volume inspect my_volume         -> get info about the volume
docker volume rm my_volume              -> remove volume
```
