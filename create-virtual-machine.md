# 创建虚拟机

**任务：**

创建一台 Linux 系统的虚拟机，Linux 系统的类型选择的是 CentOS 7.x 。

## 创建项目

每个项目都在各自的文件夹里，你可以为每个项目单独去创建虚拟机。打开命令行工具，先为项目创建一个文件夹：

```
cd ~/desktop
mkdir awesome-project
cd awesome-project
```

上面执行的命令就是在命令行下面，先进入到当前登录用户的桌面（desktop）上，在这个位置上创建了一个叫 awesome-project 的目录，又进入到了这个目录的下面。

然后去创建一个 Vagrant 虚拟机的配置文件，可以再执行一下：

```
vagrant init centos/7
```

上面用了一个 `vagrant init` 命令，它可以为你创建一个配置文件，后面的 `centos/7` 是虚拟机要用的一个系统镜像（box）的名字。

用编辑器打开项目目录 awesome-project ，你会看到有个 Vagrant 配置文件：`Vagrantfile`，它里面的配置选项稍后再解释。

## 启动虚拟机

回到命令行，进入到项目所在目录（根目录下有 Vagrantfile 的地方），执行命令启动项目下的虚拟机：

```
vagrant up
```

第一次启动虚拟机需要漫长的等待，主要是因为在你的电脑上还没有你想在项目虚拟机上使用的系统镜像（box），Vagrant 得先在它的云服务器上为你下载需要的系统镜像。下载过程的快慢取决于你的网络，在国内下载国外服务器上的资源，速度会受到限制。

### 启动时都发生了什么

在执行 vagrant up 的时候，命令行会返回一些日志，这些日志里会出现一些有用的东西。你需要了解一下虚拟机启动时都发生了什么。

```
Bringing machine 'default' up with 'virtualbox' provider...
```

machine 是虚拟机，default 是项目里的虚拟机的名字，你可以为一个项目创建多台虚拟机。如果没有特别设置，只有一台虚拟机，那么这台虚拟机的名字就是 'default'。'virtualbox' provider ，表示用的是 Virtualbox 类型的虚拟机，因为之前我们在电脑上安装的虚拟机软件就是 Virtualbox。

#### 查找安装镜像

```
==> default: Box 'centos/7' could not be found. Attempting to find and install...
```

centos/7 是我想在项目里用的虚拟机的系统镜像的名字。上面提示没找到这个系统镜像，Vagrant 决定到自己的云服务器上找一下，找到以后会自动把它安装在电脑上。这个动作只需要执行一次，下回在其它的项目里使用同样的系统镜像，我们电脑上已经有了，就不需要先到云上去下载这个镜像了，Vagrant 会直接为项目复制一份这个镜像。

```
==> default: Loading metadata for box 'centos/7'
    default: URL: https://atlas.hashicorp.com/centos/7
==> default: Adding box 'centos/7' (v1704.01) for provider: virtualbox
    default: Downloading: https://atlas.hashicorp.com/centos/boxes/7/versions/1704.01/providers/virtualbox.box
==> default: Successfully added box 'centos/7' (v1704.01) for 'virtualbox'!
```

Vagrant 到它的云服务 atlas 上去查找 centos/7 这个系统镜像，找到以后就去下载它，这个镜像文件（.box）大概 400 多兆，所以需要下载一会儿，镜像可能会提供不同的版本，这里给我们下载的是适合用在 Virtualbox 上的镜像文件。

```
https://atlas.hashicorp.com/centos/boxes/7/versions/1704.01/providers/virtualbox.box
```

上面这个地址是需要用的这个 box 的直接下载地址，你可以复制这个地址，然后用一些下载工具去下载这个 box，这样会快很多。下面完成以后，可以再手工用命令，把这个 box 安装到电脑上，这样以后项目就可以使用你手工下载并安装的这个 box 了。

注意：`1704.01` 是镜像的版本号，镜像的维护者升级了镜像以后，这个版本号会变，也就是对应的镜像的下载地址也是会变化的。

#### 导入镜像文件

项目需要的镜像已经在电脑上存在了，第一次启动虚拟机的时候 Vagrant 还需要为项目去导入这个镜像。就是基于下载并安装在电脑上的镜像，复制一份，把它放在一个特定的地方。也就是你为不同的项目创建的虚拟机，都是完全独立的，相互没有任何影响。

```
==> default: Importing base box 'centos/7'...
```

上面提示正在导入 box 文件：`'centos/7'`

#### 启动配置虚拟机

接下来 Vagrant 就开始去配置虚拟机了。具体输出的日志信息，跟你所做的配置与使用的镜像有关。在做具体配置的时候再去解释相关的日志。

**第一次启动全部的日志：**

```
Bringing machine 'default' up with 'virtualbox' provider...
==> default: Box 'centos/7' could not be found. Attempting to find and install...
    default: Box Provider: virtualbox
    default: Box Version: >= 0
==> default: Loading metadata for box 'centos/7'
    default: URL: https://atlas.hashicorp.com/centos/7
==> default: Adding box 'centos/7' (v1704.01) for provider: virtualbox
    default: Downloading: https://atlas.hashicorp.com/centos/boxes/7/versions/1704.01/providers/virtualbox.box
==> default: Successfully added box 'centos/7' (v1704.01) for 'virtualbox'!
==> default: Importing base box 'centos/7'...
==> default: Matching MAC address for NAT networking...
==> default: Checking if box 'centos/7' is up to date...
==> default: Setting the name of the VM: awesome-project_default_1494589415407_40513
==> default: Fixed port collision for 22 => 2222. Now on port 2200.
==> default: Clearing any previously set network interfaces...
==> default: Preparing network interfaces based on configuration...
    default: Adapter 1: nat
==> default: Forwarding ports...
    default: 22 (guest) => 2200 (host) (adapter 1)
==> default: Booting VM...
==> default: Waiting for machine to boot. This may take a few minutes...
    default: SSH address: 127.0.0.1:2200
    default: SSH username: vagrant
    default: SSH auth method: private key
    default: 
    default: Vagrant insecure key detected. Vagrant will automatically replace
    default: this with a newly generated keypair for better security.
    default: 
    default: Inserting generated public key within guest...
    default: Removing insecure key from the guest if it's present...
    default: Key inserted! Disconnecting and reconnecting using new SSH key...
==> default: Machine booted and ready!
==> default: Checking for guest additions in VM...
    default: No guest additions were detected on the base box for this VM! Guest
    default: additions are required for forwarded ports, shared folders, host only
    default: networking, and more. If SSH fails on this machine, please install
    default: the guest additions and repackage the box to continue.
    default: 
    default: This is not an error message; everything may continue to work properly,
    default: in which case you may ignore this message.
==> default: Rsyncing folder: /Users/xiaoxue/Desktop/awesome-project/ => /vagrant
```

## 连接虚拟机

虚拟机启动以后，你就可以连接到它，执行命令：

```
vagrant ssh
```

进入以后，你在命令行里做的所有的操作就相当于是在这台虚拟机上进行的。有点像是你连接到了一台服务器，只不过这台服务器是在你自己电脑上的一台虚拟机。

连接成功以后，会返回提示符：

```
[vagrant@localhost ~]$
```



