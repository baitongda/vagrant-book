# 定义多台虚拟机

你的网站最终可能要用多台服务器提供的服务，模拟这种环境你可以在本地项目里定义多台虚拟机。在 Vagrant 配置文件（Vagrantfile）里，可以定义多台虚拟机，每台虚拟机都有自己的名字，各自独立的配置。比如你可以单独设置每台虚拟机的 IP 地址。你也可以单独控制某台虚拟机。

下面这个配置定义了两台虚拟机，`web` 与 `db`：

```ruby
Vagrant.configure("2") do |config|
  config.vm.box = "centos/7"

  config.vm.define "web" do |web|
    web.vm.network "private_network", ip: "192.168.33.11"
  end

  config.vm.define "db" do |db|
    db.vm.network "private_network", ip: "192.168.33.12"
  end
end
```

在 Vagrantfile 里定义一台机器，就是一个代码块：

```ruby
config.vm.define "web" do |web|
  web.vm.network "private_network", ip: "192.168.33.11"
end
```

上面定义了一个叫 `web` 的机器，do 后面的 `web` 是个参数，关于这台机器的配置你都得使用这个参数的名字开头，`web.vm.network` 配置了 `web` 这台机器的网络。想去配置这台机器的同步目录，应该用 `web.vm.synced_folder` 。像这样：

```ruby
config.vm.define "web" do |web|
  web.vm.network "private_network", ip: "192.168.33.11"
  web.vm.synced_folder "./app/web", "/mnt/web" 
end
```

> 如果你想在之前创建的 `awesome-project` 里测试多机配置，先把之前创建的虚拟机使用 `vagrant destroy` 销毁掉，然后添加新的多机配置，再去启动。

多台共用的一些配置可以放在定义虚拟机的代码块之外，比如：

```ruby
config.vm.box = "centos/7"
```

这样定义的两台虚拟机都会使用同样的镜像。

## 启动

执行 `vagrant up`，可以同时启动定义的所有的虚拟机。

**启动时的日志像这样：**

```
Bringing machine 'web' up with 'virtualbox' provider...
Bringing machine 'db' up with 'virtualbox' provider...
==> web: Importing base box 'centos/7'...
==> web: Matching MAC address for NAT networking...
...
==> db: Importing base box 'centos/7'...
==> db: Matching MAC address for NAT networking...
```

也可以单独启动某台机器，在执行的 `vagrant` 命令的最后加上机器的名字就行了，像这样：

```
vagrant reload web
```



