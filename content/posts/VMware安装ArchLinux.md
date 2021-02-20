---
title: "VMware安装ArchLinux"
date: 2021-02-19T19:03:23+08:00
typora-root-url: ../../static
---

#### 安装准备 Arch 用不了独显?

**镜像下载**

[download](https://archlinux.org/download/)

**确认启动模式**

~~~
# ls /sys/firmware/efi/efivars
~~~

如果命令后显示目录并且没有错误输出，则是UEFI启动模式。否则是BIOS启动模式。

**连接网络**（实机mac wifi-menu连接？）

确定你的网卡接口开启

~~~
# ip link
# ping baidu.com
~~~

**更新系统时钟**

~~~
# timedatectl set-ntp true
~~~

**磁盘分区**

查看当前硬盘分区情况

~~~
# fdisk -l
~~~

BIOS MBR和UEFI GPT分区有所不同，如下。

| Mount point |        Partition        | [Partition type](https://en.wikipedia.org/wiki/Partition_type) |     Suggested size      |
| :---------: | :---------------------: | :----------------------------------------------------------: | :---------------------: |
|  `[SWAP]`   | `/dev/*swap_partition*` |                          Linux swap                          |    More than 512 MiB    |
|   `/mnt`    | `/dev/*root_partition*` |                            Linux                             | Remainder of the device |

|        Mount point        |           Partition           | [Partition type](https://en.wikipedia.org/wiki/GUID_Partition_Table#Partition_type_GUIDs) |     Suggested size      |
| :-----------------------: | :---------------------------: | :----------------------------------------------------------: | :---------------------: |
| `/mnt/boot` or `/mnt/efi` | `/dev/*efi_system_partition*` | [EFI system partition](https://wiki.archlinux.org/index.php/EFI_system_partition) |    At least 260 MiB     |
|         `[SWAP]`          |    `/dev/*swap_partition*`    |                          Linux swap                          |    More than 512 MiB    |
|          `/mnt`           |    `/dev/*root_partition*`    |                    Linux x86-64 root (/)                     | Remainder of the device |

GPT使用gdisk/cgdisk命令，fdisk/cfdisk只支持MBR，如果强行使用fdisk/cfdisk的话，可能会丢失数据。

~~~
# cgdisk #选择New，新建分区
默认扇区->输入+260M设置分区大小->输入/dev/efi_system_partition，一定要输入名称，否则一会无法识别->输入8300或ef00或8200设置分区类型，参考https://wiki.archlinux.org/index.php/GPT_fdisk
~~~



**格式化分区**

~~~
# mkfs.ext4 /dev/root_partition
# mkfs.fat -F32 /dev/efi_system_partition #如不格式化，会报错
# mkswap /dev/swap_partition
~~~

**挂载分区**

~~~
# mount /dev/root_partition /mnt
# mkdir /mnt/efi
# mount /dev/efi_system_partition /mnt/efi
# swapon /dev/swap_partition
~~~

**安装**

更换国内源

~~~
# vim /etc/pacman.d/mirrorlist # 在Server关键字的上层添加
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinux/$repo/os/$arch 
~~~

安装archlinux基础包，例如linux核心和硬件固件

~~~
# pacstrap /mnt base linux linux-firmware
~~~

**配置系统**

~~~
# genfstab -U /mnt >> /mnt/etc/fstab
# arch-chroot /mnt
# ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime   #设置时区为上海
# hwclock --systohc
# vim /etc/locale.gen   #然后移除需要的地区的注释，即前面的#号。我去除的下面4个前面的#号
en_US.UTF-8
zh_CN.UTF-8
zh_HK.UTF-8
zh_TW.UTF-8
locale-gen    #本地化的程序与库若要本地化文本，都依赖Locale，其明确规定地域、货币、时区日期
               的格式、字符排列方式和其他本地化标准等等。
# vim /etc/locale.conf  #创建locale.conf并编辑LANG这一变量。
写入LANG=en_US.UTF-8   #将系统locale设置为en_US.UTF-8，系统的Log就会用英文显示，更容易
                       判断和处理问题。不推荐在此设置任何中文 locale，会导致 TTY 乱码。

~~~

**安装引导程序**

用于启动系统

~~~
# uefi用这个
pacman -S grub efibootmgr intel-ucode os-prober  #?
intel的CPU安装intel-ucode，amd的CPU安装amd-ucode。如果不太确定自己的cpu型号，可以安装一个neofetch，然后使用neofetch进行查看。

# 可选项
pacman -S neofetch
neofetch # 查看cpu和显卡信息

# 必选项
pacman -S intel-ucode
pacman -S amd-ucode # 根据实际情况安装 

mkdir /boot/grub                               #创建引导目录
grub-mkconfig > /boot/grub/grub.cfg            #创建引导配置文件
uname -m                                       #查看架构 x86_64
grub-install --target=x86_64-efi --efi-directory=/efi     #安装
-----------------------------------------------------------------
# bios用这个
# pacman -S grub
# grub-install --target=i386-pc /dev/sdX #注意这里的sdx应该为硬盘（例如/dev/sda），而不是形如/dev/sda1这样的分区。
# grub-mkconfig -o /boot/grub/grub.cfg
~~~

配置完毕后

~~~
pacman -S vim ntfs-3g dialog wpa_supplicant networkmanager netctl dhcpcd zsh
setfont /usr/share/kbd/consolefonts/LatGrkCyr-12x22.psfu.gz      #设置较大的字体
exit      #安装完毕后退出新系统。
umount /mnt #卸载挂载的硬盘
reboot
~~~

## 安装完成后

联网，没有互联网，啥也干不了：

```bash
NetworkManager # 打开NetworkManager
nmcli device wifi list # 搜索网络，一下子没出来可以过几秒再试试
nmcli device wifi connect SSID password password 
# 上面的SSID和password分别换成自己家网络的wifi名和密码
# 显然取一个不带中文的wifi名还是蛮重要的
systemctl enable NetworkManager # 开机自启动NetworkManager，不用手动联网了
```

连接上互联网以后，我们需要为自己创建一个普通用户账号，一直使用`root`不太好。`username`可以改成自己喜欢的用户名，`-m`表示创建用户的家目录`~`，`-G`表示把用户放进`wheel`这个组。 

```bash
useradd -m -G wheel username
```

创建完用户以后，设置一下用户的密码：

```bash
passwd username
```

为了让普通用户能够暂时获得管理权限，我们需要安装`sudo`包：

```text
pacman -S sudo
```

安装完毕以后，使用vim打开`/etc/sudoers`，找到`wheel ALL=(ALL) ALL`这一行取消注释，然后使用`:wq!`强制改写该文件，使得`wheel`这个组内的用户都可以使用`sudo`。

使用`su username`（username换成自己刚刚设置好的名字）换成普通用户登录系统，我们就可以正式开始使用archlinux系统了。





https://wiki.archlinux.org/index.php/Installation_guide

