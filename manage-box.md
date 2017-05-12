# 管理镜像

**任务：**

熟悉虚拟机镜像的管理流程。

## 镜像

box 就是 Vagrant 里用的系统镜像。镜像有点像是把安装好的操作系统打了个包，生成一个文件。Vagrant 里的 box 有不同的类型，可以用在不同的 Provider 上，比如有适合在 Virtualbox 上用的 box，有适合在 VMware 上用的 box。

Vagrant 在它的云服务上提供下载这些镜像，有的镜像是 CentOS 系统，有些是 Ubuntu 系统，有些可能是用户自己修改之后的某类操作系统。比如有些镜像里面已经安装好了网站的运行环境，用这种镜像创建的虚拟机，启动以后就会拥有一个配置好的网站运行环境了。

## 搜索镜像

在 Atlas 上面可以搜索你需要的镜像，你也可以把自己做好的镜像推送给 Atlas，这样所有人都可以使用你做的镜像了。

**Atlas**

[https://atlas.hashicorp.com/boxes/search](https://atlas.hashicorp.com/boxes/search)

**下面是 centos/7 这个镜像的地址：**

[https://atlas.hashicorp.com/centos/boxes/7](https://atlas.hashicorp.com/centos/boxes/7)

这个镜像的名字是 centos/7，centos 是用户在 Atlas 上的用户名，直接是操作系统的名字，所以这个用户应该就是 CentOS 官方用户。斜线后面的 7 是这个镜像的拥有者为镜像起的名字，如果是官方用户提供的镜像，一般会用它表示镜像里的操作系统的版本。

打开镜像的页面，你会看到镜像的介绍，可用的版本，还有镜像适用的 Provider 都有什么，这个 centos/7 镜像有几个不同的类型：

* vmware\_desktop
* virtualbox
* libvirt

## 安装镜像

在项目下面的 Vagrantfile 里面，会有一条配置指定项目的虚拟机所使用的镜像的名字，启动虚拟机的时候，如果 Vagrant 没在你的电脑上发现指定名字的镜像，它就会自动为你到云服务器上去搜索，下载并安装这个镜像。

你可以查看已经安装在电脑上的镜像列表，执行命令：

```
vagrant box list
```

我这里会返回：

```
centos/7            (virtualbox, 1704.01)
```

表示我的电脑上有一个名字是 centos/7 的镜像，这样如果为项目创建的虚拟机使用这个镜像的话，第一次启动虚拟机的时候，Vagrant 就不会去下载它了，直接会在电脑上复制一份这个镜像。

使用命令可以安装新的镜像，比如你在 Atlas 上看到一个名字是 ubuntu/trusty64 的镜像，看名字应该知道这个镜像的操作系统是 Ubuntu，系统的版本是 trusty64（14.04 LTS）。安装这个镜像可以执行：

```
vagrant box add ubuntu/trusty64
```

Vagrant 会先去给你下载这个镜像：

```
==> box: Loading metadata for box 'ubuntu/trusty64'
    box: URL: https://atlas.hashicorp.com/ubuntu/trusty64
==> box: Adding box 'ubuntu/trusty64' (v20170509.0.0) for provider: virtualbox
    box: Downloading: https://atlas.hashicorp.com/ubuntu/boxes/trusty64/versions/20170509.0.0/providers/virtualbox.box
    box: Progress: 6% (Rate: 169k/s, Estimated time remaining: 0:49:14)
```

上面的 Downloading 后面的东西就是这个镜像文件的直接的下载地址。如果你嫌直接用 Vagrant 在命令行下面下载比较慢，你可以复制这个镜像的下载地址，然后使用下载工具去下载这个镜像文件。

终止正在执行的命令，按一下 `ctrl + C` 。

下面是我要添加的镜像的直接下载地址，可以把它放在下载工具里去下载：

```
https://atlas.hashicorp.com/ubuntu/boxes/trusty64/versions/20170509.0.0/providers/virtualbox.box
```

下载任务完成以后，你会得到一个 .box 文件，我这就是 virtualbox.box，有了这个 box 文件，就可以手工执行命令去把它安装在电脑上，这样它就会出现在可用的镜像列表里了。执行命令：

```
vagrant box add ubuntu/trusty64 ~/downloads/virtualbox.box
```

ubuntu/trusty64 是我给要安装的镜像起的名字，这个名字可以随便定义。后面的 ~/downloads/virtualbox.box 是镜像文件在本地电脑上的具体位置，你要根据自己的实际情况去修改这个镜像文件的路径。

返回的内容：

```
==> box: Box file was not detected as metadata. Adding it directly...
==> box: Adding box 'ubuntu/trusty64' (v0) for provider: 
    box: Unpacking necessary files from: file:///Users/xiaoxue/downloads/virtualbox.box
==> box: Successfully added box 'ubuntu/trusty64' (v0) for 'virtualbox'!
```

完成以后，再查看一下可用的镜像列表：

```
vagrant box list
```

现在会出现：

```
centos/7            (virtualbox, 1704.01)
ubuntu/trusty64     (virtualbox, 0)
```

多了一个刚才手工安装的 ubuntu/trusty64 。

## 升级镜像

升级前先检查一下镜像是否有可用的升级，执行：

```
vagrant box outdated
```

返回：

```
Checking if box 'centos/7' is up to date...
```

执行升级：

```
vagrant box update
```

返回：

```
==> default: Checking for updates to 'centos/7'
    default: Latest installed version: 1704.01
    default: Version constraints: 
    default: Provider: virtualbox
==> default: Box 'centos/7' (v1704.01) is running the latest version.
```

## 删除镜像



