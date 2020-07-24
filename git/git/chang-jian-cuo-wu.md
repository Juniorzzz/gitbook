# 常见错误

1、clone 时出现缓冲不足问题

```text
error: RPC failed; curl 56 GnuTLS recv error (-54): Error in the pull function.
```

原因：是因为http方式克隆时缓存不足出现的报错

解决方式：执行命令修改缓存区大小， 或者更换成SSH方式clone

```text
git config --global http.postBuffer 2000000000
```

