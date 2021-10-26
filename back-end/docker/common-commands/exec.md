---
description: Run a command in a running container
---

# exec

Usage: docker exec \[OPTIONS] CONTAINER COMMAND \[ARG...]

> \-d, --detach Detached mode: run command in the background&#x20;
>
> \--detach-keys string Override the key sequence for detaching a container&#x20;
>
> \-e, --env list Set environment variables&#x20;
>
> \--env-file list Read in a file of environment variables
>
> \-i, --interactive Keep STDIN open even if not attached&#x20;
>
> \--privileged Give extended privileges to the command&#x20;
>
> \-t, --tty Allocate a pseudo-TTY&#x20;
>
> \-u, --user string Username or UID (format: \[:])&#x20;
>
> \-w, --workdir string Working directory inside the container

```
docker exec -it mysqldock bash
```
