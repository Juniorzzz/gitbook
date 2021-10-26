---
description: Create a new image from a container's changes
---

# commit

Usage: docker commit \[OPTIONS] CONTAINER \[REPOSITORY\[:TAG]]

> \-a, --author string Author (e.g., "John Hannibal Smith [hannibal@a-team.com](mailto:hannibal@a-team.com)")
>
> &#x20;\-c, --change list Apply Dockerfile instruction to the created image&#x20;
>
> \-m, --message string Commit message&#x20;
>
> \-p, --pause Pause container during commit (default true)

```
docker commit [container id] [new image name]
```
