# 同步目录

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

## 禁用默认的同步目录

在虚拟机的配置文件里，添加一行配置：

```
config.vm.synced_folder ".", "/vagrant", disabled: true
```

`config.vm.synced_folder` 配置的是同步目录，`"."`表示同步目录在当前主机下的位置，一个点表示当前目录，这是一个相对位置，相对的是当前这个配置文件（Vagrantfile）。后面的 `"/vagrant"` 是同步目录在虚拟机上的位置。最后又用了一个 `disabled` 选项，值设置成了 `true`，意思就是禁用这个同步目录。重新启动虚拟机（`vagrant reload`），可以让这个配置生效。

## 添加同步目录

在虚拟机的配置文件里可以添加需要的同步目录。先在存储在本地电脑上的项目下面创建一个目录，名字是 app，把之前创建的 hello.txt 放在这个 app 目录的下面。然后打开 Vagrantfile，添加一行配置：

```
config.vm.synced_folder "./app", "/mnt"
```

这行配置的意思是，让主机上的项目目录下的 `app` ，跟虚拟机上的 `/mnt` 是同步的。现在如果重启虚拟机，会使用默认类型的同步目录，我们用的是 Virtualbox 类型的虚拟机，所以这个同步目录的类型默认就会是 Virtualbox。这种类型的同步需要你在虚拟机上安装 virtualbox guest addition，如果没安装，在启动虚拟机的时候会报错。

> Virtualbox 类型的同步目录性能低下，我们先介绍两种比它更快的同步目录。



