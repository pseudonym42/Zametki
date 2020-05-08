# How to use containers?

---

To create a container:

```
docker create [options] IMAGE
  -t, --tty                  # pseudo-TTY session
      --name NAME            # container name
  -e, --env NAME=abc         # env vars
```

For example:

```
$ docker create -ti fedora bash

6d8af538ec541dd581ebc2a24153a28329acb5268abe5ef868c1f1a261221752

```

Once container is created you can start it (following the above command):

```
$ docker start -ai 6d8af538ec5

bash-4.2#
```

The above `create + start` commands could be done with one single command - run:

```
docker run --name my_nginx_cont nginx

```

However, when you run a container without any options, the container will start and stop immediately. So, if you want keep it running:

```
docker run -td container_id
```

here flag `-t` creates a pseudo-TTY session, flag `-d` allows to detach the container and run it in background (it will also print container ID)

Run a container passing some shell command:

```
docker run nginx echo hey
```

To enter a running container:

```
docker exec -it my_nginx_cont /bin/bash
```

From the above commands one might notice that flags `-i` and `-t` are doing similar jobs. However, `-i` is used to create interactive session and normally be used for piping as it keeps `STDIN` open:

```
$ echo hello | docker run -i nginx cat
  hello
```

If you want a container to get auto-destroyed once it stops then:

```
docker run --rm
```

There are lots of other commands that could be used to manage containers, for example:

```
docker stop                     -> stops running container
docker restart                  -> stops and starts a container
docker kill                     -> sends SIGKILL to a running container
docker ps                       -> list running containers
docker ps -a                    -> list running & stopped containers
docker stop $(docker ps -aq)    -> stop all running containers
docker container prune          -> delete all stopped containers
docker inspect                  -> show container info
docker                          -> show container resource usage
```
