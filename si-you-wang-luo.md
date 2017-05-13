# 私有网络

在主机与虚拟机之间创建一个私有网络，或者叫专有网络，通过这个网络主机与虚拟机之间可以相互连接。也就是你可以在主机上通过虚拟机在这个私有网络上的 IP 地址访问到虚拟机。在你的主机应该还存在另外一个网络，一般这个网络就是你的主机用来上网的路由器创建的。在这个网络上可能存在其它的上网设备，比如其它的电脑，你的手机，平板等等。这些上网设备不能访问在你主机上创建的私有网络上的虚拟机。

## 私有网络配置

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

## 验证私有网络配置

在主机上，通过虚拟机在私有网络上的 IP 地址，你可以访问到这台虚拟机。打开浏览器，访问下面这个地址，你会看到一个 NGINX 的欢迎界面，这个界面的内容是安装在虚拟机上的 NGINX 提供的。

```
https://192.168.33.10
```



