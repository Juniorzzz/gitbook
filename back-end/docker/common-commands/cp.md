---
description: Copy files/folders between a container and the local filesystem
---

# cp

Usage: docker cp \[OPTIONS\] CONTAINER:SRC\_PATH DEST\_PATH\|- docker cp \[OPTIONS\] SRC\_PATH\|- CONTAINER:DEST\_PATH

Use '-' as the source to read a tar archive from stdin and extract it to a directory destination in a container. Use '-' as the destination to stream a tar archive of a container source to stdout.

> -a, --archive Archive mode \(copy all uid/gid information\)
>
>  -L, --follow-link Always follow symbol link in SRC\_PATH

```text
docker cp e/xxxx/file [container name]:/var
```



