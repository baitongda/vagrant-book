# 端口转发

端口转发（Forwarded Port）。请求访问主机（你的电脑）上的某个端口，把请求转发到虚拟机上的某个端口。比如你在虚拟机上安装了一个 Web 服务器，默认它会在虚拟机上的 80 端口提供接待服务。你可以配置一个端口转发，当访问主机上的某个端口的时候，把请求转发到虚拟机上的 80 端口。下面通过一个试验你就很容易理解这个端口转发。

## 端口转发配置

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

## 安装 Web 服务器

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

## 验证设置的端口转发

上面安装的 NGINX 这个 Web 服务器现在已经启动了，它默认会在 80 这个端口提供服务。我们设置端口转发，把对主机的 8080 端口的请求转发到虚拟机的 80 端口上。现在，打开电脑上的浏览器，访问地址：

```
http://localhost:8080
```

你会看到 NGINX 的欢迎界面， 在浏览器上访问的这个 localhost 表示的就是你当前用的这台电脑本身，冒号右边是一个具体的端口号，对这个地址的请求就相当于是在请求虚拟机上的 80 端口，在虚拟机上安装的 NGINX 会接受请求并返回请求需要的内容。

