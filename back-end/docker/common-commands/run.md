---
description: Run a command in a new container
---

# run

Usage: docker run \[OPTIONS\] IMAGE \[COMMAND\] \[ARG...\]

> --add-host list Add a custom host-to-IP mapping\(host:ip\)
>
> -c, --cpu-shares int CPU shares \(relative weight\)
>
> -d, --detach Run container in background and print container ID
>
> -i, --interactive Keep STDIN open even if not attached
>
> -m, --memory bytes Memory limit
>
> --name string Assign a name to the container
>
> -p, --publish list Publish a container's port\(s\) to the host
>
> --restart Restart policy to apply when a container exits \(default "no"\) \(e.g -restart=always\)

> --rm Automatically remove the container when it exits
>
> -t, --tty Allocate a pseudo-TTY

> -v, --volume list Bind mount a volume 
>
> --volume-driver string Optional volume driver for the container 
>
> --volumes-from list Mount volumes from the specified container\(s\)
>
> -w, --workdir string Working directory inside the container

```text
docker run -t -i ubuntu:15.10 /bin/bash
```

> -t Allocate a pseudo-TTy
>
> -i Keep STDIN open even if not attached

