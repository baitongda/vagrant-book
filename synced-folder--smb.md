# SMB 类型的同步目录

Windows 用户可以使用这种同步目录。

```
SMB shared folders require running Vagrant with administrative
privileges. This is a limitation of Windows, since creating new
network shares requires admin privileges. Please try again in a
console with proper permissions or use another synced folder type.
```

启动时：

```
==> default: Preparing SMB shared folders...
    default: You will be asked for the username and password to use for the SMB
    default: folders shortly. Please use the proper username/password of your
    default: Windows account.
    default:
    default: Username: wanghao
    default: Password (will be hidden):
```

提示

    ==> default: Mounting SMB shared folders...
        default: C:/Users/wanghao/Desktop/awesome-project/app => /mnt
    Failed to mount folders in Linux guest. This is usually because
    the "vboxsf" file system is not available. Please verify that
    the guest additions are properly installed in the guest and
    can work properly. The command attempted was:

    mount -t cifs -o uid=`id -u vagrant`,gid=`getent group vagrant | cut -d: -f3`,sec=ntlm,credentials=/etc/smb_creds_4c8a431d482a44ef786a48835ea0c1b8 //172.16.109.128/4c8a431d482a44ef786a48835ea0c1b8 /mnt
    mount -t cifs -o uid=`id -u vagrant`,gid=`id -g vagrant`,sec=ntlm,credentials=/etc/smb_creds_4c8a431d482a44ef786a48835ea0c1b8 //172.16.109.128/4c8a431d482a44ef786a48835ea0c1b8 /mnt

    The error output from the last command was:

    mount: wrong fs type, bad option, bad superblock on //172.16.109.128/4c8a431d482a44ef786a48835ea0c1b8,
           missing codepage or helper program, or other error
           (for several filesystems (e.g. nfs, cifs) you might
           need a /sbin/mount.<type> helper program)

           In some cases useful info is found in syslog - try
           dmesg | tail or so.



