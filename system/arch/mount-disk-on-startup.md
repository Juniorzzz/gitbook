# mount disk on startup

check disk uuid&#x20;

```
lsblk -o +UUID,fstype
```

Editor the file&#x20;

```
sudo vim /etc/fstab

UUID=XXXXXXXXXX    ntfs-3g    /mnt/xxxx    default    0 0
```





