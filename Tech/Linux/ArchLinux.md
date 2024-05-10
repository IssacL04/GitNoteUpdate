### #准备工作

*一个Archlinux系统引导U盘

*电脑禁用secure boot（在BIOS中禁用）

*设置启动顺序为U盘优先

### 1.联网

```shell
iwctl #进入iwctl网络配置
device list #设备列表
station wlan0 scan #搜索网络
station wlan0 get-networks #获取网络列表
station wlan0 connect xxx #链接
ping baidu.com #连通性测试

timedatectl set-timezone Asia/Shanghai #设定时区
```

### 2.磁盘分区

```shell
cfdisk #进入分区工具
分区列表:
EFI 512MB
SWAP 2G/4G
LINUX FILESYSTEM ...
fdisk -l #显示分区情况
```

### 3.下载

```shell
nano /etc/pacman.d/mirrorlist
第一项改为https://mirrors.ustc.edu.cn/archlinux/$repo/os/$arch
pacman -Syy #下载系统组件
pacstrap /mnt base base-devel linux linux-headers linux-firmware grub micro nano networkmanager dhcpcd openssh git efibootmgr man-db man-pages ntfs-3g firefox noto-fonts-cjk links
```

### 4.挂载

```shell
mkdir /boot/efi
mkdir /mnt/boot/efi
mount /dev/sda1 /mnt/boot/efi
genfstab -U /mnt >> /mnt/etc/fstab
arch-chroot /mnt #进入root
uname -a #检查fstab文件
free -m #SWAP是否生效
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime #修改时区
hwclock --systohc #修改时间
nano /etc/locale.gen #修改语言
#将zh_CN,en_US(UTF-8)前面的注释去掉即可
locale-gen #生成语言编码配置
nano /etc/locale.conf
->LANG=zh_CN.UTF-8
locale #验证
nano /etc/hostname #修改主机名
passwd root #root密码
grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id-GRUB
frub-mkconfig -o /boot/grub/grub.cfg
#安装grub引导
systemctl enable NetworkManager dhcpcd sshd#启动网络服务
useradd -m -G wheel "username" #添加用户
passwd "username" #修改用户密码
nano /etc/sudoers
->
%wheel ALL=(ALL:ALL)ALL
%wheel ALL=(ALL:ALL) NOPASSWD:ALL

pacman -Syunano /etc/pacman.conf
->
#去除[multilib],[community]的注释
[arculinuxcn]
Server = https://mirrors.ustc.edu.cn/archlinuxcn/$arch

pacman -Syy
pacman -S archlinuxcn-keyring
pacman -S paru #安装包助手
pacman -S micro
micro /etc/paru.con
->#去除BottomUp注释
```

### 5.安装可视化桌面环境（KDE）

*.GNOME狗都不用！

```shell
#显卡驱动
pacman -S xf-86-video-intel vulkan-intel mesa
#声卡驱动
pacman -S alsa-utils pulseaudio pulseaudio-bluetooth cups
#显示服务
pacman -S xorg
#英文字体
pacman -S ttf-dejavu ttf-droid ttf-hack ttf-font-awesome otf-font-awesome ttf-lato ttf-liberation ttf-linux-libertine ttf-opensans ttf-roboto ttf-ubuntu-font-family
#中文字体
pacman -S ttf-hannom noto-fonts noto-fonts-extra noto-fonts-emoji noto-fonts-cjk adobe-source-code-pro-fonts adobe-source-sans-fonts adobe-source-serif-fonts adobe-source-han-sans-cn-fonts adobe-source-han-sans-hk-fonts adobe-source-han-sans-tw-fonts adobe-source-han-serif-cn-fonts wqy-zenhei wqy-microhei
#打开字体引擎
micro /etc/profile.d/freetype2.sh
->#取消注释：export FREETYPE_PROPERTIES="truetype:interpreter-version=40"
```

```shell
pacman -S plasma sddm konsole dolphin kate ark okular spectacle#下载KDE组件
systemctl enable sddm#sddm登陆服务
```

```shell
#中文输入法
sudo pacman -S fcitx5-im 
sudo pacman -S fcitx5-chinese-addons  fcitx5-rime
```

