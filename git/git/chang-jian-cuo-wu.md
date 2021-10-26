# 常见错误

1、clone 时出现缓冲不足问题

```
remote: Counting objects: 369, done.
remote: Compressing objects: 100% (116/116), done.
error: RPC failed; curl 56 OpenSSL SSL_read: SSL_ERROR_SYSCALL, errno 10054
fatal: The remote end hung up unexpectedly
fatal: early EOF
fatal: index-pack failed
```

原因：是因为http方式克隆时缓存不足出现的报错

解决方式：执行命令修改缓存区大小， 或者更换成SSH方式clone

```
git config --global http.postBuffer 2000000000
```

2、Push 报错

先Pull 再Push
