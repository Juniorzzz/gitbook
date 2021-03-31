---
description: Run a command in a running container
---

# exec

Usage: docker exec \[OPTIONS\] CONTAINER COMMAND \[ARG...\]

> -d, --detach Detached mode: run command in the background 
>
> --detach-keys string Override the key sequence for detaching a container 
>
> -e, --env list Set environment variables 
>
> --env-file list Read in a file of environment variables
>
> -i, --interactive Keep STDIN open even if not attached 
>
> --privileged Give extended privileges to the command 
>
> -t, --tty Allocate a pseudo-TTY 
>
> -u, --user string Username or UID \(format: \[:\]\) 
>
> -w, --workdir string Working directory inside the container

```text
docker exec -it mysqldock bash
```

