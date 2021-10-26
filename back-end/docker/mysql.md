# Mysql

## Run

```
docker run mysql
docker run mysql:latest
docker run mysql:5.7.33
```

```
docker run -p 3306:3306 --name mysql -e MYSQL_ROOT_PASSWORD=123456 -d mysql:8.0.23
```

> \-p Publish a container's port(s) to the host&#x20;

> \--name Assign a name to the container&#x20;

> \-e Set environment variables

> \-d Run container in background and print container ID

```
docker run -p 3306:3306 --name mysql \
-v /usr/local/docker/mysql/conf:/etc/mysql \
-v /usr/local/docker/mysql/logs:/var/log/mysql \
-v /usr/local/docker/mysql/data:/var/lib/mysql \
-e MYSQL_ROOT_PASSWORD=123456 \
-d mysql:8.0.23
```

> \-v host : container
