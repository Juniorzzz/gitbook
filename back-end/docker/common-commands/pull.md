---
description: Pull an image or a repository from a registry
---

# pull

Usage: docker pull \[OPTIONS] NAME\[:TAG|@DIGEST]

> \-a, --all-tags Download all tagged images in the repository&#x20;
>
> \--disable-content-trust Skip image verification (default true)&#x20;
>
> \--platform string Set platform if server is multi-platform capable&#x20;
>
> \-q, --quiet Suppress verbose output

```
docker pull [name:tag]
```

```
docker pull mysql:5.7.33
docker pull mysql:latest
docker pull mysql
```

