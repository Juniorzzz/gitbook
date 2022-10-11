# 双系统时间问题

Solution 1

```
sudo hwclock -w --localtime
```

Solution 2

```
timedatectl set-local-rtc 1
```

Solution 3

```
修改/etc/adjtime文件中的UTC，为LOCAL。
```
