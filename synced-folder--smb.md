# SMB 类型的同步目录

Windows 用户可以使用 SMB 这种类型的同步目录。文档上说使用这种类型的共享目录需要在虚拟机上安装 smbfs 或 cifs，Vagrant 应该会自动为你安装好。 不过我测试的结果是并没有安装好挂载 SMB 类型同步目录需要的这些东西。

我用的镜像是 centos/7，在虚拟机里没有搜索到 smbfs，但找到了 cifs-utils ，所以先登录到虚拟机，然后手工安装一下这个 cifs-utils，在虚拟机内部执行：

```
sudo yum install cifs-utils -y
```

然后修改虚拟机的配置文件，像这样配置一个 SMB 类型的同步目录：

```
config.vm.synced_folder "app", "/mnt", type: "smb"
```

挂载 SMB 同步目录需要你输入登录到 Windows 系统的用户名与密码，你也可以在配置同步目录的时候，指定登录用的用户名与密码，像这样：

```
config.vm.synced_folder "app", "/mnt", type: "smb", smb_username: "wanghao", smb_password: "密码"
```

用了两个选项，`smb_username`_ 后面的值是你登录 Windows 主机的时候用的用户名，_`smb_password` 是登录用的密码。重启虚拟机，配置的同步目录会生效。

## 验证同步目录

在启动的时候，观察日志，你会发现：

```
==> default: Mounting SMB shared folders...
    default: C:/Users/wanghao/Desktop/awesome-project/app => /mnt
```

显示正在挂载 SMB 类型的共享目录，这个目录在主机上的位置是：`C:/Users/wanghao/Desktop/awesome-project/app`，在虚拟机上的位置是 `/mnt` 。

登录到虚拟机，进入到 `/mnt`，创建一个文件，退出虚拟机，再到项目下的 app 目录查看一下：

```
vagrant ssh

#虚拟机
cd /mnt
touch hello.txt
exit

#主机
cd app
ls
```

你应该会在主机的项目下的 app 里看到在虚拟机上创建的 hello.txt，在主机上用编辑器打开这个文件，编辑一下文件里的内容，然后登录到虚拟机去查看编辑之后的结果。

## 问题

1 - 在 Windows 上的命令行工具必须要用管理员的身份去运行，不然会遇到下面这个错误：

```
SMB shared folders require running Vagrant with administrative
privileges. This is a limitation of Windows, since creating new
network shares requires admin privileges. Please try again in a
console with proper permissions or use another synced folder type.
```

2 - Vagrant 1.9.4 设置 SMB 同步目录，会遇到上面这个错误，不管你是否使用的是管理员身份运行的命令行工具。

3 - 降级到 Vagrant 1.9.3，启动主机时遇到问题，解决这个问题我手工编辑了一个文件里的输入错误。相关链接：[https://github.com/mitchellh/vagrant/issues/8404](https://github.com/mitchellh/vagrant/issues/8404)

4 - 如果不手工在虚拟机上安装 `cifs-utils`，启动虚拟机时会遇到下面的错误。

    ==> default: Mounting SMB shared folders...
        default: C:/Users/wanghao/Desktop/awesome-project/app => /mnt
    Failed to mount folders in Linux guest. This is usually because
    the "vboxsf" file system is not available. Please verify that
    the guest additions are properly installed in the guest and
    can work properly. The command attempted was:

    mount -t cifs -o uid=`id -u vagrant`,gid=`getent group vagrant | cut -d: -f3`,sec=ntlm,credentials=/etc/smb_creds_4c8a431d482a44ef786a48835ea0c1b8 //172.16.109.128/4c8a431d482a44ef786a48835ea0c1b8 /mnt
    mount -t cifs -o uid=`id -u vagrant`,gid=`id -g vagrant`,sec=ntlm,credentials=/etc/smb_creds_4c8a431d482a44ef786a48835ea0c1b8 //172.16.109.128/4c8a431d482a44ef786a48835ea0c1b8 /mnt

    The error output from the last command was:

    mount: wrong fs type, bad option, bad superblock on //172.16.109.128/4c8a431d482a44ef786a48835ea0c1b8,
           missing codepage or helper program, or other error
           (for several filesystems (e.g. nfs, cifs) you might
           need a /sbin/mount.<type> helper program)

           In some cases useful info is found in syslog - try
           dmesg | tail or so.



