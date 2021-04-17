#### Macbook pro 2015安装archlinux

制作启动U盘，重启电脑，按着option键不放，进入

arch需要全程联网下载组件，安卓手机USB连接电脑，启动手机USB共享网络

~ ip link

~ dhcpcd enp0s25(这个取决当前显示的网卡)

~ ping baidu.com 只要返回数据就行，可能会断断续续的，不用管

分区

fdisk -l

cgdisk

delete旧分区，New新

注：<Enter> 表示按回车键，而不是输入这几个字符！ 

保留 128M 的空间 First sector (xxxxxxx-xxxxxxxx, default = xxxxxxxx): +128M<Enter>   #Macbook需要保留128M未用空间？ +128M意思分出128M free空间

Boot 分区大小 Size in sectors or {KMGTP} (default = xxxxxxxx): 260M<Enter> 

分区 ID，保持默认即可 Hex code or GUID (L to show codes, Enter = xxxx): <Enter> 

分区名 Enter new partition name, or <Enter> to use the current name: boot<Enter>

**除去128M free空间，另需要3个分区，分别是 efi（ef00）、swap(8200)、root(8300),别弄错了！！！**

**格式化分区**

```
# mkfs.ext4 /dev/sda3  #也就是root partition
# mkfs.fat -F32 /dev/sda1 #efi
# mkswap /dev/sda2 #swap
```

**挂载分区**

```
# mount /dev/sda3 /mnt
# mkdir /mnt/efi
# mount /dev/sda1 /mnt/efi
# swapon /dev/sda2
```

安装

~ vim /etc/pacman.d/mirrorlist

Server = https://mirrors.163.com/archlinux/$repo/os/$arch

安装arch基础包

~ pacstrap /mnt base linux linux-firmware

配置系统

~ genfstab -U /mnt >> /mnt/etc/fstab

~ arch-chroot /mnt

~ pacman -S base-devel linux-headers intel-ucode vim tree man linux-lts linux-lts-headers

~ ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime

~ hwclock --systohc

~ vim /etc/locale.gen 

去除 en_US.UTF-8 和 zh_CN.UTF-8 的注释即可 

~ vim /etc/locale.gen 

~ locale-gen 

~ echo LANG=en_US.UTF-8 > /etc/locale.conf 

~ cat /etc/hosts   

127.0.01 localhost 

::1            localhost

~ pacman -S networkmanager

~ systemctl enable NetworkManager

启动 Arch Linux 后，使用以下命令搜寻附近的无线网络 

~ nmcli device wifi list 

使用以下命令连接网络

 ~ nmcli device wifi connect <WiFi 名称> password <WiFI 密码> 

如果是隐藏网络，可以使用以下命令进行连接 

~ nmcli device wifi connect <WiFi 名称> password <WiFI 密码> hidden yes 

可以通过下面的命令查看所有连接

 ~ nmcli connection 

你也可以使用字符界面进行网络连接，输入 nmtui 即可

##### 创建用户！重要，不然重启电脑进不去

~ passwd 

~ useradd --create-home --groups wheel,audio,video,optical,storage --shell /bin/bash <username> ~ passwd <username> 

~ visudo 

去除下面这一行的注释 

%wheel ALL=(ALL) ALL

配置启动引导

uefi用这个 

pacman -S grub efibootmgr intel-ucode os-prober  

intel的CPU安装intel-ucode，amd的CPU安装amd-ucode。如果不太确定自己的cpu型号，可以安装一个neofetch，然后使用neofetch进行查看。 

可选项 

pacman -S neofetch

neofetch # 查看cpu和显卡信息

mkdir /boot/grub                               #创建引导目录 

grub-mkconfig > /boot/grub/grub.cfg            #创建引导配置文件 

uname -m                                       #查看架构 x86_64 

grub-install --target=x86_64-efi --efi-directory=/efi     #安装



启动arch linux

exit

umount -a  # 错误不用管

reboot

安装chromium浏览器

sudo pacman -S chromium

安装中文（解决中文乱码）、中文输入法

sudo pacman -S wqy-zenhei ttf-fireflysung

reboot

vim /etc/pacman.conf 

[archlinuxcn]

SigLevel = Optional TrustAll

Server    = https://mirrors.tuna.tsinghua.edu.cn/archlinuxcn/$arch

**记得安装完成后，把这几行注释掉！！！否则影响其他软件的安装**

sudo pacman -S fcitx fcitx-im  

sudo pacman -S fcitx-configtool   #图形配置工具 

sudo pacman -S sunpinyin    #输入法

安装 typora
yay -S typora
