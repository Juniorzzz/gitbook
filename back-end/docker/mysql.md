# Mysql

## Run

```text
docker run mysql
docker run mysql:latest
docker run mysql:5.7.33
```

```text
docker run -p 3306:3306 --name mysql -e MYSQL_ROOT_PASSWORD=123456 -d mysql:8.0.23
```

> -p Publish a container's port\(s\) to the host

> --name Assign a name to the container

> -e Set environment variables

> -d Run container in background and print container ID

```text
docker run -t -i ubuntu:15.10 /bin/bash
```

> -t Allocate a pseudo-TTy
>
> -i Keep STDIN open even if not attached

