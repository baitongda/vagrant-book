# 虚拟机配置文件

理解虚拟机的配置文件：Vagrantfile。

## Vagrantfile

在项目的目录下面有个 Vagrantfile 文件，它是我们执行 vagrant init 命令创建的一个虚拟机的配置文件。在这个配置文件里可以包含项目使用的虚拟机的相关配置。比如我在执行 vagrant init 命令的时候加了一个 centos/7 ，指定了虚拟机使用的镜像的文件，这样创建的这个配置文件，实际的内容会是这样的：

```
Vagrant.configure("2") do |config|
  config.vm.box = "centos/7"
end
```

虽然你看到的 Vagrantfile 文件里的内容很长，但大部分都是注释内容，里面介绍了可用的配置选项还有相关的解释。去掉这些注释，实际有效的配置代码一共就上面这三行。你可以自己创建一个 Vagrantfile，加上上面这几行代码，这个配置文件你也可以分享给别人。有了这个配置文件，执行 `vagrant up` 以后，就会按照配置文件里的规定去创建虚拟机。

```
Vagrant.configure("2") do |config|
  # 配置内容放在这里
end
```

配置的主体内容可以放在上面的这个代码块里。

```
config.vm.box = "centos/7"
```

上面这行配置指定了虚拟机使用的镜像是 `centos/7` 。

