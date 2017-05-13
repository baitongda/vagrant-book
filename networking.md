# 虚拟机网络配置

**任务：**

学会配置虚拟机的几种不同类型的网络：端口转发，私有网络，公有网络。

## 虚拟机网络

配置虚拟机的网络，主要就是要把在虚拟机上安装的服务公开，比如我们在虚拟机上安装了一个 Web 服务器，你想在虚拟机之外访问安装在这个虚拟机上的 Web 访问器，就需要配置一下虚拟机的某种类型的网络，可以是端口转发，私有网络或公有网络。

启动虚拟机以后，你的虚拟机就可以使用 nat 这种上网方式，也就是如果你的电脑能上网，在你的虚拟机的内部也是可以上网的。做个试验：

打开命令行，进入到一开始我们创建的项目，然后启动虚拟机：

```
cd ~/desktop/awesome-project
vagrant up
```

在启动虚拟机返回的日志里，你会看到网络相关的配置：

```
==> default: Clearing any previously set forwarded ports...
==> default: Clearing any previously set network interfaces...
==> default: Preparing network interfaces based on configuration...
    default: Adapter 1: nat
==> default: Forwarding ports...
    default: 22 (guest) => 2222 (host) (adapter 1)
```

一开始会清理之前配置的端口转发与网卡的配置。然后会根据我们在 Vagrantfile 里做的网络配置重新配置虚拟机内部的网络。Adapter 1 表示网卡 1，类型是 nat 。最后又设置了一个转发的端口（Forwarding ports），把主机（host）上的 2222 端口转发到虚拟机（guest）上的 22 端口。这个配置是 Vagrant 自动配置的，从主机使用 SSH 连接到虚拟机的时候需要用到这个端口转发 22 \(guest\) =&gt; 2222 \(host\) 的网络配置。

连接到虚拟机，执行：

```
vagrant ssh
```

现在命令行的提示符会像这样：

```
[vagrant@localhost ~]$
```

这就表示你已经进入到虚拟机的内部了，在里面的操作都相当于是在虚拟机内部去做的。

做个试验，验证一下你的虚拟机是否可以访问某个网站，执行命令：

```
ping ninghao.net
```

如果返回下面类似形式的信息，说明是可以连接到指定的网站的：

```
PING ninghao.net (116.62.90.68) 56(84) bytes of data.
64 bytes from 116.62.90.68 (116.62.90.68): icmp_seq=1 ttl=63 time=22.8 ms
64 bytes from 116.62.90.68 (116.62.90.68): icmp_seq=2 ttl=63 time=22.5 ms
```

`ping` 命令会往你指定的主机上发送一些数据包，主机一般收到这种数据包以后会返回给你一些信息，你的虚拟机能收到这个回馈，就证明虚拟机能够连接到这台主机。不过要注意，有些服务器可能会禁止接收 `ping` 命令发送的数据包，这样虽然你能连接到它，但是不会收到服务器的回馈。大部分的操作系统都可以使用这个 `ping` 命令。

