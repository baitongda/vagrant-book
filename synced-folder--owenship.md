## 同步目录的用户权限

macOS

```
config.vm.synced_folder "./app", "/mnt", type: "nfs"
```

Windows

```
config.vm.synced_folder "./app", "/mnt", type: "smb"
```

启动以后再连接到虚拟机，然后进入到同步目录在虚拟机上的位置，查看一下目录的内容：

```
cd /mnt
ls -la
```

NFS 目录

```
drwxr-xr-x.  3 501  games   102   May 13 06:56 mnt
```

SMB 目录

```
drwxr-xr-x.  2 vagrant vagrant    0 May 13 12:33 mnt
```



