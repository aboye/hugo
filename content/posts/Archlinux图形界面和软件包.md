---
title: "Archlinux图形界面和软件包"
date: 2021-02-20T18:20:21+08:00
---

### 显卡驱动的安装

![img](/Users/logon/Documents/hugo/static/images/arch21.png)

参照这个表格，安装相应的包，比如你是`intel`的集成显卡（绝大多数人的情况），执行：

```
sudo pacman -S xf86-video-intel
```

提示：`Nvidia`的独显驱动如非必要，建议只装集成显卡的驱动（省电，如果同时装也会默认使用集成显卡），不容易出现冲突问题。相反，如果集成显卡驱动有问题无法装上，可以装独显驱动，具体的版本请到下面的链接查询：

> https://wiki.archlinux.org/index.php/Xorg#Driver_installation

### 安装Xorg

`Xorg`是`Linux`下的一个著名的开源图形服务，我们的桌面环境需要`Xorg`的支持。

执行如下命令安装`Xorg`及相关组件：

```
sudo pacman -S xorg
```

### 安装桌面环境

`Linux`下有很多著名的桌面环境如`Xfce`、`KDE(Plasma)`、`Gnome`、`Unity`、`Deepin`等等，它们的外观、操作、设计理念等各方面都有所不同， 在它们之间的比较与选择网上有很多的资料可以去查。

在这里我们选择笔者使用的`Xfce`和非常流行的`KDE(Plasma)`作为示范，当然你也可以把它们全部装上换着用……因为`Linux`的模块化，这样完全没有问题。

更多桌面环境的安装指南请见下面的链接：

> https://wiki.archlinux.org/index.php/Desktop_environment#List_of_desktop_environments

#### 安装Xfce

直接安装软件包组（包含了很多软件包）即可：

```
sudo pacman -S xfce4 xfce4-goodies
```

#### 安装KDE(Plasma)

直接安装软件包组（包含了很多软件包）即可：

```
sudo pacman -S plasma kde-applications
```

### 安装桌面管理器

安装好了桌面环境包以后，我们需要安装一个图形化的桌面管理器来帮助我们登录并且选择我们使用的桌面环境，这里我推荐使用`sddm`。

#### 安装sddm

执行：

```
sudo pacman -S sddm
```

#### 设置开机启动sddm服务

这里就要介绍一下`Arch`下用于管理系统服务的命令`systemctl`了，服务的作用就是字面意思，为我们提供特定的服务，比如`sddm`就为我们提供了启动`xorg`与管理桌面环境的服务。

命令的使用并不复杂：

```
sudo systemctl start   服务名 （启动一项服务）
sudo systemctl stop    服务名 （停止一项服务）
sudo systemctl enable  服务名 （开机启动一项服务）
sudo systemctl disable 服务名 （取消开机启动一项服务）
```

所以这里我们就执行下面命令来设置开机启动`sddm`：

```
sudo systemctl enable sddm
```

### 提前配置网络

到现在我们已经安装好了桌面环境，但是还有一件事情需要我们提前设置一下。由于我们之前使用的一直都是`netctl`这个自带的网络服务，而桌面环境使用的是`NetworkManager`这个网络服务，所以我们需要禁用`netctl`并启用`NetworkManager`：

```
sudo systemctl disable netctl
sudo systemctl enable NetworkManager （注意大小写）
```

同时你可能需要安装工具栏工具来显示网络设置图标（某些桌面环境已经装了，但是为了保险可以再装一下）：

```
sudo pacman -S network-manager-applet
```

这样开机以后我们就可以在图形界面下配置我们的网络啦。

### 连接网络

现在我们是在新安装的系统上进行操作，所以我们要重新联网，我们在之前安装系统时已经提前装好了相关的包。所以现在只要跟之前一样：

- 如果你是有线网并且路由器支持DHCP的话插上网线后先执行以下命令获取IP地址：

  ```
  dhcpcd
  ```

- 无线网：

  ```
  wifi-menu
  ```

  按界面提示进行操作就可以了。

  如果`wifi-menu`无法联网

  ```
  sudo systemctl start wpa_supplicant.service
  ```

  使用`nmcli`查看Wifi列表：

  ```
  nmcli dev wifi list
  ```

  连接Wifi（注意要带引号）：

  ```
  nmcli device wifi connect "你的Wifi名称" password "你的Wifi密码"
  ```

同样可以使用`ping`命令来测试是否正常联网。



**安装谷歌浏览器**

~~~
# vim /etc/pacman.conf
[archlinuxcn]   #添加
SigLevel = Optional TrustAll
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinuxcn/$arch
# pacman -Sy
# pacman -S archlinuxcn-keyring
# pacman -S google-chrome
~~~

命令行界面—>图形界面

执行startx命令

图形界面—>命令行界面

Ctrl+Alt+F2

