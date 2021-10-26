# 内存占用过大

修改配置文件/etc/gitlab/gitlab.rb

1、减少进程数

> 默认是被注释掉的，官方建议该值是CPU核心数加一，可以提高服务器的响应速度，如果内存只有4G，或者服务器上有其它业务，就不要改了，以免内存不足。另外，这个参数最小值是2，设为1，服务器可能会卡死。
>
>

```
unicorn['work_processes'] = 2
```

2、减少数据库缓存

> 默认为256MB，可适当改小

```
postgresql['shared_buffers'] = "256MB"
```

3、减少数据库并发数

> 默认为8，可适当改小

```
postgresql['max_worker_processes'] = 8
```

4、减少sidekiq并发数

> 默认是25，可适当改小

```
sidekiq['concurrency'] = 25
```

5、启用Swap分区

修改配置之后需要重新配置gitlab 并重启

```
gitlab-ctl reconfigure
gitlab-ctl restart
```

配置数值参考官方文档

> [https://docs.gitlab.com/ee/install/requirements.html](https://docs.gitlab.com/ee/install/requirements.html)

![](<../../.gitbook/assets/image (3).png>)

![](<../../.gitbook/assets/批注 2020-07-15 095909.png>)
