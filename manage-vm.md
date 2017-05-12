## 管理虚拟机

**任务：**

熟悉使用 Vagrant 管理虚拟机的流程。

## 查看状态

如果你已经登录到了虚拟机上，先退出来。在命令行下看到下面提示符就表示你已经登录到虚拟机上了：

```
[vagrant@localhost ~]$
```

输入命令：

```
exit
```

应该会返回：

```
logout
Connection to 127.0.0.1 closed.
```

现在你的位置应该是在项目的根目录下面，先查看一下虚拟机的状态：

```
vagrant status
```

我这里返回的是：

    Current machine states:

    default                   running (virtualbox)

    The VM is running. To stop this VM, you can run `vagrant halt` to
    shut it down forcefully, or you can run `vagrant suspend` to simply
    suspend the virtual machine. In either case, to restart it again,
    simply run `vagrant up`.

上面告诉你当前虚拟机的状态，`default` 是在项目里创建的这台默认的虚拟机的名字。`running` 表示虚拟机正在运行中。

## 关机与休眠

彻底关闭虚拟机，执行：

```
vagrant halt
```

完成以后，查看虚拟机状态：

```
vagrant status
```

返回的是：

    Current machine states:

    default                   poweroff (virtualbox)

    The VM is powered off. To restart the VM, simply run `vagrant up`

上面显示，`default` 这台虚拟机的状态是 `poweroff`，表示已经关闭。再次启动虚拟机，执行：

```
vagrant up
```

启动以后我们再试一下休眠，执行命令：

```
vagrant suspend
```

查看状态：

     → vagrant status
    Current machine states:

    default                   saved (virtualbox)

    To resume this VM, simply run `vagrant up`.

这次显示的状态是 `saved`，表示休眠。跟关机不同，虚拟机休眠，被唤醒以后，在休眠之前运行的程序仍然会继续运行。

## 销毁虚拟机

你为项目创建了虚拟机，现在不再需要这个项目了，或者想为项目重新创建虚拟机，可以先把之前为项目创建的虚拟机销毁掉。这样再次启动虚拟机以后，Vagrant 会根据项目下的 Vagrantfile 里的配置，为你创建一台全新的虚拟机。

销毁虚拟机，执行：

```
vagrant destroy
```

这会提示你是否要销毁掉指定的虚拟机，输入 Y 再按回车，可以确认销毁。或者在执行销毁命令的时候添加 -f（--force） 选项：

```
vagrant destroy -f
```

销毁之后再查看项目下的虚拟机的状态：

     → vagrant status
    Current machine states:

    default                   not created (virtualbox)

    The environment has not yet been created. Run `vagrant up` to
    create the environment. If a machine is not created, only the
    default provider will be shown. So if a provider is not listed,
    then the machine is not created for that environment.

这次提示 `not created`，表示还未创建。



