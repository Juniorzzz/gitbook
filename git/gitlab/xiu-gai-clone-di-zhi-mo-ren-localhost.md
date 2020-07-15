# 修改Clone 地址默认localhost

修改文件  /opt/gitlab/embedded/service/gitlab-rails/config/gitlab.yml

```text
## GitLab settings
  gitlab:
    ## Web server settings (note: host is the FQDN, do not include http://)
    host: localhost         >> 修改为主机的IP即可
    port: 80                >> 修改为监听端口
    https: false
```

执行gitlab-ctl reconfigure命令， 此文件会被重置，需要重新配置

