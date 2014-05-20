---
layout: post
title: filesystem升级问题
---
&emsp;&emsp;就在昨天，很多arch使用者又迎来了新一轮的滚挂高发期，很不幸的是我也成为了其中的一员，按我以前的心态怎么也得把系统重做了，但是因为最近强迫症似乎变得没那么严重了，再加上实在舍不得我这个完美的arch，就没有干傻事，但是系统还是要用的，于是我便开始了艰难的修复征程。。。
此次arch升级主要是完成了/bin,/sbin,/usr/sbin的精简工作，并把其下所有的文件移动到了/usr/bin下，简单来说以后/bin,/sbin,/usr/sbin都是ln -s指向/usr/bin下了，这个就清爽多了。。。以后所有的可执行文件都在那一个下面了，完成这次整合之后分区一下子变得清爽多了，但是如果更新前没做好足够的准备的话，直接就跪了。。。如果看了官网的新闻的话，基本不会挂的，但也有特殊情况，比如我这样的。。更新到一半，学校网络中心断网了，然后由于散热问题，机器太热直接关机了，然后就发生了这个“你懂得”惨剧，然后开机的时候就会出现
       
    ERROR:Root device mounted successfully,but /sbin/init does not exist.
    Bailing out,you are on your own.Good luck.
    sh:can't access tty; job control turned off
    [rootfs /]#

&emsp;&emsp;连fallback也不能用，更别提各种最基本的指令了，然后就是各种论坛求助，国内外论坛的逛，刚开始修改内核参数，我把init=/usr/lib/systemd/systemd添加到了GRUB_CMDLINE_LINUX_DEFAULT="quiet"中，然后chroot刷新grub，开机，还是不行，不过能进fallback了，我本来想手动的添加软链接，把他们都ln -s到/usr/bin下，但是还是没这个勇气，就怕把系统搞得乱七八糟彻底没救，然后我就想到用chroot再更新一次试试，算是死马当活马医了，然后中午的时候就赖在强哥他们寝室用他们寝室的宽带进chroot重新

    pacman -Syu --ignore filesystem,bash
    pacman -S bash
    pacman -Su
    
&emsp;&emsp;然后重启。。。竟然OK了。。。果然是天无绝人之路啊，就这样，我的arch又变得这么完美了。。。有关chroot的问题下次再开一篇文章详细说说吧，这玩意儿实在是系统挂了之后修复的神器啊。。。

