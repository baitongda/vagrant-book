# 虚拟机网络配置

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

你会看到 NGINX 的欢迎界面， 在浏览器上访问的这个 localhost 表示的就是你当前用的这台电脑本身，冒号右边是一个具体的端口号，对这个地址的请求就相当于是在请求虚拟机上的 80 端口，在虚拟机上安装的 NGINX 会接受请求并返回请求需要的内容。

## 私有网络

在主机与虚拟机之间创建一个私有网络，或者叫专有网络，通过这个网络主机与虚拟机之间可以相互连接。也就是你可以在主机上通过虚拟机在这个私有网络上的 IP 地址访问到虚拟机。在你的主机应该还存在另外一个网络，一般这个网络就是你的主机用来上网的路由器创建的。在这个网络上可能存在其它的上网设备，比如其它的电脑，你的手机，平板等等。这些上网设备不能访问在你主机上创建的私有网络上的虚拟机。

### 私有网络配置

打开虚拟机的配置文件 Vagrantfile，添加下面这行配置：

```
config.vm.network "private_network", ip: "192.168.33.10"
```

vm.network 配置的是虚拟机的网络，private\_network 指的就是私有网络，这个网络上只有两个设备，你的主机，还有在这台主机上创建的使用了这个网络的虚拟机。ip 的后面是为虚拟机分配的在这个私有网络上的 IP 地址，配置生效以后，在主机上，你可以通过这个 IP 地址访问到虚拟机。

重新启动虚拟机，让新做的网络配置生效，执行：

```
vagrant reload
```

注意启动时返回的日志，你会发现：

```
default: Adapter 2: hostonly
```

虚拟机上多了一个网卡：hostonly。连接到虚拟机，再查看一下虚拟机上的网络配置：

```
vagrant ssh
ip addr
```

返回了：

```
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN qlen 1
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
    link/ether 52:54:00:ba:51:2b brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 brd 10.0.2.255 scope global dynamic eth0
       valid_lft 85696sec preferred_lft 85696sec
    inet6 fe80::5054:ff:feba:512b/64 scope link 
       valid_lft forever preferred_lft forever
3: eth1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
    link/ether 08:00:27:2c:69:14 brd ff:ff:ff:ff:ff:ff
    inet 192.168.33.10/24 brd 192.168.33.255 scope global eth1
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fe2c:6914/64 scope link 
       valid_lft forever preferred_lft forever
```

在上面第三个网卡 eth1 里，你会发现：

```
inet 192.168.33.10/24 brd 192.168.33.255 scope global eth1
```

`192.168.33.10` 就是我们为虚拟机配置的它在私有网络上的 IP 地址。

### 验证私有网络配置

在主机上，通过虚拟机在私有网络上的 IP 地址，你可以访问到这台虚拟机。打开浏览器，访问下面这个地址，你会看到一个 NGINX 的欢迎界面，这个界面的内容是安装在虚拟机上的 NGINX 提供的。

```
https://192.168.33.10
```

## 公有网络

配置虚拟机的公有类型的网络，可以让虚拟机属于跟你的主机在同一个网络内，这个网络一般是上网用的路由器创建的一个局域网。公有网络的好处是，同一网络的其它的设备可以直接通过虚拟机在公有网络上的 IP 地址访问到这台虚拟机。配置了虚拟机的公有网络，创建这个网络的路由器会为你的虚拟机分配一个 IP 地址。

配置公有网络，你得先清楚自己电脑所处的网络环境，一般来说，你的电脑会属于一台家庭路由器创建的网络。这个网络的 IP 地址段，取决于你的路由器的配置，通常都是 192.168.x.x 这种形式，你可以先查看一下路由器（实物，比如小米路由器）的 IP 地址段的配置，也可以查看一下自己电脑正在使用的 IP 地址。

Windows，在命令行下执行：

```
ipconfig
```

macOS，在命令行下执行：

```
ifconfig
```

仔细查看命令返回的信息，你应该可以找到自己的电脑在网络上的 IP 地址。我的是 192.168.31.140，这样我为虚拟机配置了公有网络以后，它的 IP 地址也应该是 192.168.31.x 这种形式。

### 配置公有网络

在虚拟机的配置文件 Vagrantfile 里，添加一行配置：

```
config.vm.network "public_network"
```

重新启动虚拟机会让这个新做的公有网络的配置生效，执行：

```
vagrant reload
```

这回启动虚拟机的时候，让我选择网卡：

```
==> default: Available bridged network interfaces:
1) en1: Wi-Fi (AirPort)
2) en2: Thunderbolt 1
3) en3: Thunderbolt 2
4) en0: 以太网
5) bridge0
6) p2p0
7) awdl0
==> default: When choosing an interface, it is usually the one that is
==> default: being used to connect to the internet.
    default: Which interface should the network bridge to?
```

上面出现的这个列表是我的主机上可用的网卡，有些是真的，有些是虚拟出来的，在你那里看到的这个列表可能会不太一样。我的主机用的是 Wi-Fi 无线上网，所以我要选择 1 ，输入 `1`，按一下回车。

现在我的这台虚拟机配置了三种网络：

```
==> default: Preparing network interfaces based on configuration...
    default: Adapter 1: nat
    default: Adapter 2: hostonly
    default: Adapter 3: bridged
```

其中的 `default: Adapter 3: bridged` 是配置了虚拟机的公有网络以后生成的。

启动以后再次登录到虚拟机，然后执行：

```
ip addr
```

又会出现新的网络配置，这次会出现：

```
192.168.31.225/24
```

`192.168.31.225` 就是虚拟机在公有网络上的一个 IP 地址，因为我知道我的主机在这个网络上的 IP 地址也是 `192.168.31.x` 这种形式，所以我可以判断 `192.168.31.225` 是虚拟机在这个网络上的 IP 地址。你看到的 IP 地址可能跟我的不太一样，你需要根据路由器的 IP 地址段的配置去判断哪个才是虚拟机在公有网络上的 IP 地址。

### 验证公有网络配置

因为虚拟机上安装了 NGINX 这个 Web 服务器，验证配置的公有网络是否有效，你可以直接在浏览器上访问虚拟机在公有网络上的 IP 地址，如果能看到 NGINX 的欢迎界面，说明配置已经生效了。你可以再试一下用网络内的其它设备访问一下同样的 IP 地址，比如在你的手机或平板的浏览器上访问虚拟机的公有网络 IP 地址。

```
http://192.168.31.225
```

注意，你的路由器为虚拟机分配的这个公有网络的 IP 地址跟我的很可能是不一样的。

