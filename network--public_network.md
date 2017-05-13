# 公有网络

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

## 配置公有网络

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

## 验证公有网络配置

因为虚拟机上安装了 NGINX 这个 Web 服务器，验证配置的公有网络是否有效，你可以直接在浏览器上访问虚拟机在公有网络上的 IP 地址，如果能看到 NGINX 的欢迎界面，说明配置已经生效了。你可以再试一下用网络内的其它设备访问一下同样的 IP 地址，比如在你的手机或平板的浏览器上访问虚拟机的公有网络 IP 地址。

```
http://192.168.31.225
```

注意，你的路由器为虚拟机分配的这个公有网络的 IP 地址跟我的很可能是不一样的。

