# 虚拟机网络配置

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

## 端口转发

端口转发（Forwarded Port）。请求访问主机（你的电脑）上的某个端口，把请求转发到虚拟机上的某个端口。比如你在虚拟机上安装了一个 Web 服务器，默认它会在虚拟机上的 80 端口提供接待服务。你可以配置一个端口转发，当访问主机上的某个端口的时候，把请求转发到虚拟机上的 80 端口。下面通过一个试验你就很容易理解这个端口转发。

### 端口转发配置

打开 Vagrantfile，添加一行配置代码：

```
config.vm.network "forwarded_port", guest: 80, host: 8080
```

你的配置文件里的内容现在应该像这样：

```
Vagrant.configure("2") do |config|  
  config.vm.box = "centos/7"
  config.vm.network "forwarded_port", guest: 80, host: 8080
end
```

让新的配置生效，需要重新启动虚拟机，退出登录的虚拟机，然后再执行重启命令：

```
[vagrant@localhost ~]$ exit
logout
Connection to 127.0.0.1 closed.

 → vagrant reload
```

`vagrant reload` 可以重新启动虚拟机。重启的时候仔细看日志，你会发现：

```
==> default: Forwarding ports...
    default: 80 (guest) => 8080 (host) (adapter 1)
    default: 22 (guest) => 2222 (host) (adapter 1)
```

Vagrant 在配置端口转发的时候，多了一个 `80 (guest) => 8080 (host)`。

### 安装 Web 服务器

配置的端口转发生效以后，重新登录到虚拟机，然后我们要在虚拟机上安装一个 Web 服务器。

```
vagrant ssh
```

登录到虚拟机以后，做的操作都跟 Linux 操作系统相关，因为我们创建的虚拟机的操作系统是 CentOS 7.x，所以避免不了，你需要了解一点关于这种操作系统的使用。有些使用方法是所有 Linux 类型的操作系统共有的，有些是 CentOS 这种操作系统特有的。

Web 服务器对于操作系统来说就是一些软件包，使用命令你可以安装在软件包仓库里包含的软件包。系统自带一些软件包仓库，你也可以安装新的软件包仓库。我们要安装的 Web 服务器叫 NGINX，这个软件包可以在 epel-release 这个软件仓库里找到。先去安装一下这个软件仓库：

```
sudo yum install epel-release -y
```

然后去安装 NGINX 这个 Web 服务器：

```
sudo yum install nginx -y
```

启动安装的这个服务：

```
sudo systemctl start nginx
```

让服务可以开机自启动：

```
sudo systemctl enable nginx
```

查看服务的启动状态：

```
sudo systemctl status nginx
```

 在返回的信息里你会发现：

```
Active: active (running)
```

表示 nginx 这个服务已经正在运行中了。

### 验证设置的端口转发

上面安装的 NGINX 这个 Web 服务器现在已经启动了，它默认会在 80 这个端口提供服务。我们设置端口转发，把对主机的 8080 端口的请求转发到虚拟机的 80 端口上。现在，打开电脑上的浏览器，访问地址：

```
http://localhost:8080
```























在

