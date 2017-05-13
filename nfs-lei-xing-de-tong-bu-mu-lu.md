# NFS 类型的同步目录

macOS 平台用户可以使用 NFS 类型的共享目录，Windows 用户无法使用这种类型的共享目录，下面会介绍适合在 Windows 上用的共享目录。像这样修改共享目录的配置：

```
config.vm.synced_folder "./app", "/mnt", type: "nfs"
```

加了一个 `type` 属性，值设置成了 `nfs`，表示这是一个 NFS 类型的共享目录。重新启动虚拟机，会出现跟下面类似的日志，说明 Vagrant 正在配置 NFS 类型的共享目录。启动时可能会要求你输入当前登录到电脑上的用户的密码。

```
==> default: Exporting NFS shared folders...
==> default: Preparing to edit /etc/exports. Administrator privileges will be required...
Password:
==> default: Mounting NFS shared folders...
```

## 验证同步目录

启动以后登录到虚拟机，进入到共享目录在虚拟机上的位置，查看里面的内容：

```
→ vagrant ssh
Last login: Sat May 13 06:56:19 2017 from 10.0.2.2
[vagrant@localhost ~]$ cd /mnt
[vagrant@localhost mnt]$ ls
hello.txt
```

你在虚拟机上的 `/mnt` 下面，会看到一个 `hello.txt` 文件，这个文件在主机上的位置是项目下的 `app` 这个目录里面。在虚拟机上的 `/mnt` 下面，创建一个新的文件，可以执行：

```
touch hola.txt
```

然后你到主机项目下的 `app` 目录里看一下，这里应该会出现在虚拟机的 `/mnt` 目录下创建的 `hola.txt` 这个文件。你在主机上直接把 `app` 下面的 `hola.txt` 删除掉，再到虚拟机上的 `/mnt` 目录下面查看一下，之前的 `hola.txt` 也会不见了。

