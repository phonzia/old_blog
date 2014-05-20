---
layout: post
title: Linux 之 fstab
---
&emsp;&emsp;今天偶然间瞥了一眼系统监视器，突然发现我的交换分区竟然是0，起初以为是自己内存够大，还没有用到交换空间导致的，但是因为自家系统洁癖症，心里总有些不舒服，就在终端里面查了查，输入swapon -s 竟然是空空如也，这就说明我的swap分区没有激活，我用sudo swapon /dev/sda8重新挂载了一下，这下swapon  -s能看到了。然后我重启了下，发现又变成0了- -。然后我就找到了这个etc下的fstab文件。文件/etc/fstab包含了静态文件系统信息，定义了存储设备和分区整合到整个系统的方式。mount命令会读取这个文件，确定设备和分区的挂载选项。这是archwiki上的解释。起初我以为是uuid的问题，因为有可能是uuid错了，导致系统不能识别这个分区，毕竟这么一长串数字字母，错了个把也是情有可原的，但是我用blkid查了一下，一比对分毫不差。我只好去网上找了两个别人的fstab文件来看了下。 

    <file system> <dir> <type> <options> <dump> <pass>
    tmpfs /tmp tmpfs nodev,nosuid 0 0
    UUID=24f28fc6-717e-4bcd-a5f7-32b959024e26 / ext4 defaults,noatime 0 1
    UUID=03ec5dd3-45c0-4f95-a363-61ff321a09ff /home ext4 defaults,noatime 0 2
    UUID=4209c845-f495-4c43-8a03-5363dd433153 none swap defaults 0 0
  
&emsp;&emsp;跟我自己的一比对，发现我的swap分区里面少了个none，这下我有点明白了，none对应的应该是挂载位置，swap应该是没有挂载位置的（注意挂载位置跟要挂载的分区的区别），我这儿没有none，那么这个位置就被swap填了，后面的defaults什么的也就错乱了，所以系统根本没法识别出我的swap出来。就这样，加个none，再用swapon重新激活一下我的swap分区，重启就再也不是0了。

#####_Ps_:
* ` <file systems>` - 要挂载的分区或存储设备.
*  `<dir>` -`<file systems>`的挂载位置。
* `<type>` - 要挂载设备或是分区的文件系统类型，支持许多种不同的文件系统：ext2, ext3, ext4, reiserfs, xfs, jfs, smbfs, iso9660, vfat, ntfs, swap 及 auto。 设置成auto类型，mount 命令会猜测使用的文件系统类型，对 CDROM 和 DVD 等移动设备是非常有用的。
* `<options>` - 挂载时使用的参数
* `<dump>` dump 工具通过它决定何时作备份. dump 会检查其内容，并用数字来决定是否对这个文件系统进行备份。 允许的数字是 0 和 1 。0 表示忽略， 1 则进行备份。大部分的用户是没有安装 dump 的 ，对他们而言 `<dump>` 应设为 0。
* `<pass>` fsck 读取 `<pass>` 的数值来决定需要检查的文件系统的检查顺序。允许的数字是0, 1, 和2。 根目录应当获得最高的优先权 1, 其它所有需要被检查的设备设置为 2. 0 表示设备不会被 fsck 所检查。
