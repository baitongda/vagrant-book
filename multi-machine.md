# 定义多台虚拟机

在 Vagrant 配置文件（Vagrantfile）里，可以定义多台虚拟机，每台虚拟机都有自己的名字，各自独立的配置。比如你可以单独设置每台虚拟机的 IP 地址。你也可以单独控制某台虚拟机。

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



