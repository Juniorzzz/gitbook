---
description: Pull an image or a repository from a registry
---

# pull

Usage: docker pull \[OPTIONS\] NAME\[:TAG\|@DIGEST\]

> -a, --all-tags Download all tagged images in the repository 
>
> --disable-content-trust Skip image verification \(default true\) 
>
> --platform string Set platform if server is multi-platform capable 
>
> -q, --quiet Suppress verbose output

```text
docker pull [name:tag]
```

```text
docker pull mysql:5.7.33
docker pull mysql:latest
docker pull mysql
```



