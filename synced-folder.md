# 虚拟机同步目录

你在本地为项目创建了台虚拟机，在虚拟机上安装配置了可以运行网站的环境。没直接在自己电脑上搭建这个运行环境，是因为，你的网站最终要运行的服务器使用的操作系统，跟你平时用的电脑上的操作系统不一样，因为大部分服务器的操作系统都是 Linux 类型的。

现在你要让自己开发的网站应用运行在这个搭建在虚拟机上的环境上，但是你还需要一边开发，编辑网站应用，一边测试网站应用的运行。编辑网站应用的文件都是在安装在本地电脑上的编辑器上进行的，而运行的它的环境是在虚拟机上。

也就是你的项目现在需要一个可以同步的目录，使用本地编辑器对网站应用文件进行编辑，编辑的结果直接可以同步到虚拟机的某个目录下面。在虚拟机上对这个同步目录的操作，也会影响到本地主机上的这个目录。

这个可以实时同步的目录有几种类型，选择哪种也得看自己的主机环境。比如 NFS 类型的同步目录的性能更好一些，但它只能在 macOS 或 Linux 类型的主机上使用，Windows 用户无法使用。如果主机是 Windows，可以考虑用 SMB 类型的同步目录。

## 同步目录

Vagrant 默认会设置一个同步目录 ，在主机上这个目录的位置就是项目所在的位置，也就是 Vagrantfile 所在的那个目录。在虚拟机上这个同步目录是在 `/vagrant` 这里。

做个试验，先在项目目录下随便先创建一个文件，比如 hello.txt，可以在命令行下执行：

```
touch hello.txt
```

然后重启动一下虚拟机，观察启动日志，你会发现类似下面的这条信息：

```
==> default: Rsyncing folder: /Users/wanghao/Desktop/awesome-project/ => /vagrant
```

这里的 `/Users/wanghao/Desktop/awesome-project/` 是我的项目在主机上的位置，`=>` 后面的 `/vagrant` 是这个共享目录在虚拟机上的位置。

现在登录到虚拟机，进入到 `/vagrant` 这个目录的下面，查看它下面存储的东西：

```
→ vagrant ssh
Last login: Sat May 13 06:00:36 2017 from 10.0.2.2

[vagrant@localhost ~]$ cd /vagrant
[vagrant@localhost vagrant]$ ls
hello.txt  Vagrantfile
```

在虚拟机上的 `/vagrant` 这个位置里，你会发现有 Vagrantfile 还有一个 hello.txt，这两个文件你都可以在本地的项目目录下面找到。在启动虚拟机的时候，项目目录下的东西都会被同步到虚拟机上的 `/vagrant` 这里。这个共享目录现在是单向的（主机同步到虚拟机），并且并不能实时同步。也就是只能在每次重启虚拟机的时候，才执行一次同步任务。

同步目录，能不能双向并实时同步，要看虚拟机用的镜像还有相关的同步目录的配置。

### 禁用默认的同步目录

在虚拟机的配置文件里，添加一行配置：

```
config.vm.synced_folder ".", "/vagrant", disabled: true
```

`config.vm.synced_folder` 配置的是同步目录，`"."`表示同步目录在当前主机下的位置，一个点表示当前目录，这是一个相对位置，相对的是当前这个配置文件（Vagrantfile）。后面的 `"/vagrant"` 是同步目录在虚拟机上的位置。最后又用了一个 `disabled` 选项，值设置成了 `true`，意思就是禁用这个同步目录。重新启动虚拟机（`vagrant reload`），可以让这个配置生效。

### 添加同步目录

在虚拟机的配置文件里可以添加需要的同步目录。先在存储在本地电脑上的项目下面创建一个目录，名字是 app，把之前创建的 hello.txt 放在这个 app 目录的下面。然后打开 Vagrantfile，添加一行配置：

```
config.vm.synced_folder "./app", "/mnt"
```

这行配置的意思是，让主机上的项目目录下的 `app` ，跟虚拟机上的 `/mnt` 是同步的。现在如果重启虚拟机，会使用默认类型的同步目录，我们用的是 Virtualbox 类型的虚拟机，所以这个同步目录的类型默认就会是 Virtualbox。这种类型的同步需要你在虚拟机上安装 virtualbox guest addition，如果没安装，在启动虚拟机的时候会报错。

> Virtualbox 类型的同步目录性能低下，我们先介绍两种比它更快的同步目录。

## 

## SMB 类型的同步目录

Windows 用户可以使用这种同步目录。

```
SMB shared folders require running Vagrant with administrative
privileges. This is a limitation of Windows, since creating new
network shares requires admin privileges. Please try again in a
console with proper permissions or use another synced folder type.
```

启动时：

```
==> default: Preparing SMB shared folders...
    default: You will be asked for the username and password to use for the SMB
    default: folders shortly. Please use the proper username/password of your
    default: Windows account.
    default:
    default: Username: wanghao
    default: Password (will be hidden):
```

提示

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

## Virtualbox 类型的同步目录

虚拟机软件你用的是 Virtualbox，在虚拟机上安装了 virtualbox guest addition 以后，你可以配置虚拟机使用 Virtualbox 类型的共享目录。这种类型的共享目录存在性能问题，如果网站应用只有少量文件还可以，如果文件数量太多，在这种类型的共享目录上运行的网站会非常慢。

这种类型的共享目录还有个 bug，你在虚拟机上配置的网站运行环境如果使用了 NGINX，你得配置 一下 NGINX，把 sendfile 指令设置成 off，不然你在本地主机上编辑了网站文件以后，在浏览器上刷新以后看不到修改之后的结果，仍然会显示没有编辑之前的样子。

```
sendfile off;
```



