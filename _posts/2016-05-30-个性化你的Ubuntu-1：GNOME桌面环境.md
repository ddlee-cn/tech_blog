---
title: 个性化你的Ubuntu-1：GNOME桌面环境
layout: post
categories: [Linux]
abbrlink: c0bafe01
date: 2016-05-30 19:12:10
excerpt: 有关Linux桌面环境Gnome的介绍。
---
## 我与Ubuntu
我最初是Windows98用户，再到Windows2003,Windows XP,Windows 7,上了大学后用Windows 8.1,Windows 10（想不到竟然能列这么长；我从没用过Windows Vista,不知道那是什么东西），我很喜欢8.1和10的开始屏幕和动态磁贴。非常偶然的机会，我在CS50的课程中接触了GNU/Linux，才知道，原来在MS　Windows和Mac OSＸ之外，还有一个GNU/Linux。换完SSD，学会了装操作系统，我便踏上了折腾GNU/Linux的不归路。

曾经被一个软院的同学安利Red Hat系的Fedora（尽管他现在已经投入了MacBook的怀抱）,普及各种内核之类的知识。然而，我只想安静的用它上上网，进行科学计算，并没有深入到考虑系统底层的需求层次。我还是安心地用Ubuntu吧。我也推荐第一次尝试GNU/Linux系统的小白从Ubuntu开始，相信我,askubuntu.com和stackoverflow.com会解决你的大部分问题的。


## 个性化你的Ubuntu（一）：GNOME桌面环境
相信不少读者都是从Microsoft Windows转到GNU/Linux阵营的,早就习惯了用户图形界面。但是，配合桌面环境、主题和一些插件和软件，Ubuntu照样可以很酷炫。

### 什么是GNOME
![GNOME Logo]({{ site.baseurl }}/user-imgs/Ubuntu-1/Gnomelogo.png)
（大脚丫为什么这么大。。。）

GNOME(pronounced /ɡˈnoʊm/ or /ˈnoʊm/) 最初是GNU Network Object Model Environment的缩写，但这一缩写已不再沿用（更多历史情况请参见[这里](https://mail.gnome.org/archives/marketing-list/2010-April/msg00050.html)）。

我们所说的GNOME，通常指的是由[The GNOME Project](https://www.gnome.org/about/)开发的运行于Linux之上的桌面环境。

我们每天面对的，并不是全部的Microft Windows/OS X/Linux系统，而是系统提供给我们的人机接口，而桌面环境，则是统一在同一图形用户接口（GUI）之下的一揽子软件（X Window Manager, File manager, Terminal emulator, Text editor, Image viewer, E-mail client等）。

![操作系统提供用户图形界面给用户作为人机接口]({{ site.baseurl }}/user-imgs/Ubuntu-1/OS&GUI.jpg)
[来源](http://www.slideshare.net/sherif_mosa/operating-systems-basics-26277922)

Ubuntu自带的桌面环境是[Unity](https://unity.ubuntu.com/)（图形外壳）,其他流行的桌面环境还有[KDE](http://www.kde.org/),[Xfce](http://www.xfce.org/)。但我们要谈的是GNOME。

### 什么是X window system
要谈Unix-like系统上的图形界面，就不得不提X Window System。那么，什么是X?
> The X Window System, commonly referred to merely as X, is a highly configurable, cross-platform, complete and free client-server system for managing graphical user interfaces (GUIs) on single computers and on networks of computers.

> (X窗口系统，通常简称为X，是用于管理在单个计算机和计算机网络上运行的图形用户界面（GUI）一个高度可配置的，跨平台，完整的，自由的客户端-服务器系统。）

> 来源：[LINFO](http://www.linfo.org/x.html)

我们试着通过X能够干什么来理解一下这句话。

X是一组规则、一套方法。它提供了从硬件（键鼠）接受用户输入、创建图形窗口、画出直线、位图等基本的图形功能（图形引擎）。

X实现了客户端-服务器的机制。通过划分Server和Client，X既能在本地计算机上运行，也能在计算计算机网络中运行。

X与操作系统独立。X可以理解为运行在操作系统之上的一套软件。如果不需要GUI，完全可以不用安装X。而在Microsoft Windows和OS X中，图形引擎是操作系统的一部分。

X Window System的结构如图。
![X Window System]({{ site.baseurl }}/user-imgs/Ubuntu-1/X-window-system.png)

### GNOME & X
GNOME和X Window System是什么关系？
桌面环境可以理解为一系列X client的集合，其中最重要的组件是X Window Manager。由于X Window System的client-server机制，各client之间是相对独立的，这时，需要一个特殊的client管理其他client，将他们统一在一个框架之下，这就是X Window Manager。
![Window Manager]({{ site.baseurl }}/user-imgs/Ubuntu-1/Window_Manager.png)
[来源](http://www.slideshare.net/RBandes/x-window-system)

而GNOME另一个重要的组成部分是GNOME shell，它是一个图形外壳程序，也就是我们要面对的接口。


跟GNOME相关的其他组件、库、概念
- GTK+：GIMP Widget toolkits，GNOME基于的[GUI工具箱](https://en.wikipedia.org/wiki/Widget_toolkit)。KDE则基于Qt。
- Display Manager:图形用户登陆管理器，为用户提供登陆界面，与session manager通信，开启新的session。GNOME使用的是GDM。
- Metacity：GNOME 2使用的window manager，GNOME 3使用的是Mutter。KDE使用的是KWin。
- Wayland:与X Window System对应，也是一种窗口系统

### 我现在的桌面
![我的桌面]({{ site.baseurl }}/user-imgs/Ubuntu-1/Gnome1.jpg)

我不喜欢双击桌面图标来启动程序，更多用的是Dock和全局搜索，所以，桌面上“什么都没有”。

桌面的壁纸是电影 *飞屋环游记* 的海报，使用了Numix系列的主题和图标。

下方Dock使用的程序是Cairo-Dock，桌面右方运行的程序是Conky，用来监测系统运行情况和提供天气信息，上方的Topbar里添加了许多GNOME的扩展应用。


接下来的两篇文章将介绍Gnome的安装与扩展推荐，欢迎继续阅读，撒花。

@[ddlee](http://ddlee.cc)
