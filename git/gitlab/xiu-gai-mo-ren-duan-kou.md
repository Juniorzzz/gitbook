# 修改默认端口

1.修改文件 /var/opt/gitlab/nginx/conf/gitlab-http.conf

> 此方法在运行gitlab-ctrl reconfigure 命令之后会被重置，所以适用不需要修改其他配置，仅需要修改监听端口

```text
server {
  listen *:80;  >>修改监听端口


  server_name localhost;
  server_tokens off; ## Don't show the nginx version number, a security best practice
  ......
  ......
}

```

2.修改文件 /etc/gitlab/gitlab.rb

```text
nginx['listen_port'] = nil   >> 修改监听端口
```

> 此方法需要执行命令 gitlab-ctl reconfigure ， 并且会重置覆盖掉之前的更改配置，慎用！！！ 建议在初始安装时配置

