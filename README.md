# Arch Linux Installation

## Pre-Installation

### Connect to a wifi network
```
wifi-menu
```

### Enable NTP
```
timedatectl set-ntp true
```

### Configure your disk
```
gfisk /dev/sda
```

### Format your disk and swap on

```
mkfs.fat -F32 /dev/sda1
mkswap -L "swap" /dev/sda2
swapon /dev/sda2
mkfs.ext4 -L arch /dev/sda3
```

### mount disks
```
mount /dev/sda3 /mnt
mkdir /mnt/boot
mkdir /mnt/boot/EFI
mount /dev/sda1 /mnt/boot/EFI
```

## Installation

### Packs to install

```
pacstrap /mnt base
```

## Mount it after install

### Create the fstab file and start arch to configure it

```
genfstab -p /mnt >> /mnt/etc/fstab
arch-chroot /mnt
```

## Instal packs
```
pacman -S base-devel \
         xf86-input-libinput xf86-video-intel xclip \
         openssh \
         pkgfile bash-completion \
         syslog-ng \
         tlp tlp-rdw thermald \
         tmux htop pass pass-otp \
         ncdu exa \
         rtorrent wget weechat firefox youtube-dl neomutt newsboat \
         id3 moc beets mplayer \
         aspell aspell-en \
         neovim python-neovim \
         gdb valgrind ctags cscope clang clang-tools-extra strace \
         lua \
         git tig \
         jdk9-openjdk \
         python python-requests python-pip \
         gnome-control-center gnome-session gnome-setting-daemon \
         gnome-shell gnome-terminal gnome-tweak-tools \
         gnome-shell-extensions gvfs-mtp gvfs-smb networkmanager-openvpn \
         evince eog \
         xdg-user-dirs \
         gnome-devel-docs devhelp \
         gdm \
         ttf-hack \
         grub efibootmgr intel-ucode

pacman -S libreoffice

```

## Configure it

### Localtime

```
ln -sf /usr/share/zoneinfo/America/Sao_Paulo /etc/localtime
hwclock -systohc
```

### Locale

```
echo "LANG=en_US.UTF-8" /etc/locale.conf
```

```
sudo nvim /etc/locale.gen
```
 * uncomment the line LANG=en_UTF-8

```
locale-gen
```

### Hostname

```
echo "archlinux.xps" > /etc/hostname
```

### Hosts

```
sudo nvim /etc/hosts
```

 * Add the lines
 - 127.0.0.1    localhost.localdomain   localhost
 - ::1      localhost.localdomain   localhost
 - 127.0.1.1    archlinux.xps   archlinux

### Pacman

```
sudo nvim /etc/pacman.conf
```

 - uncomment the line Color
 - add the line ILoveCandy
 - uncomment the line VerbosePkgLists

### Environment

```
sudo nvim /etc/environment
```

 * Add the lines
 - GTK_IM_MODULE=cedilla
 - QT_IM_MODULE=cedilla

### Sound config

```
sudo nvim /etc/modprobe.d/intel-hda.conf
```

 * Add the line
 - options snd_hda_intel index=1,0

### Beep off

```
sudo nvim /etc/inputrc
```

 * Add the lines
 - set bell-style none

### SSH Config

```
sudo nvim /etc/ssh/sshd_config
```
 - set PasswordAuthentification yes
 - PermitEmptyPassowrds no

### Console

```
sudo nvim /etc/vconsole.conf
```
 * Add
 FONT=latarcyrheb-sun32
 KEYMAP=us-acentos

### Logs

```
sudo nvim /etc/systemd/journald.conf
```
 * Add the line
 - SystemMaxUse=50M

### Blacklist

```
sudo nvim /etc/modprobe.d/psmouse-blacklist.conf
```
 * Add the line
 - blacklist psmouse


### Android

```
sudo nvim /etc/udev/rules.d/51-android.rules
```

 * Add the line
 - SUBSYSTEM=="usb", ATTR{idVendor}=="04e8", MODE="0666", SYMLINK+="android_adb" 

### Grub

```
sudo nvim /etc/defaults/grub
```

 - GRUB_TIMEOUT=0
 - GRUB_GFXMODE=1024x768
 - GRUB_CMDLINE_LINUX_DEFAULT="quiet loglevel=3 rd.systemd.show_status=auto rd.udev.log-priority=3" 

### Buld initramfs and install Grub

```
mkinitcpio -p linux
grub-mkconfig -o /boot/grub/grub.cfg
grub-install --recheck /dev/sda
```

### Services

```
systemctl enable gdm
systemctl enable NetworkManager
systemctl enable ufw
systemctl enable thermald
systemctl enable tlp
systemctl enable tlp-sleep
systemctl enable bluetooth
systemctl disable man-db.service
systemctl mask systemd-rfkill
systemctl mask systemd-rfkill.socket
systemctl mask geoclue
```

### Reboot

```
exit
reboot
```

## As root

### Bluetoot

```
nvim /etc/bluetooth/audio.conf
```
 - Add
 * Enable=Source,Sink,Media,Socket

 ```
nvim /etc/bluetooth/main.conf
 ```

 - Add
 * AutoEnable=true

 ```
 nvim /etc/pulse/default.pa
 ```
 - Add
 * load-module module-switch-on-connect

### GDM, Sound and bluetooth sound

```
mkdir -p ~gdm/.config/systemd/user
ln -s /dev/null ~gdm/.config/systemd/user/pulseaudio.socket
```

### ufw config
```
sudo ufw enable
sudo ufw default allow outgoing
sudo ufw default deny incoming
```

### Adding an user

```
useradd -m -g users -G wheel -s /bin/sh <username>
passwd <username>
echo '<username> ALL=(ALL) ALL' > /etc/sudoers.d/<username>
```

### Root Access

 * [INFO] If you want to disable the root access try this next command

```
passwd -l root
```

 * [INFO] If you want to bring it back try this next command

```
sudo passwd root
```
### Python modules
```
pip install pip-autoremove
```

## As user

### Neovim plugin manager

```
curl -fLo ~/.local/share/nvim/site/autoload/plug.vim --create-dirs https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

### Gnome config

 * Keyboard: English (US, intl, with dead keys)

### Weechat notification system

```
mkdir ~/.weechat
mkdir ~/.weechat/python
mkdir ~/.weechat/python/autoload
mkdir ~/.weechat/perl
mkdir ~/.weechat/perl/autoload
cd ~/.weechat/python
curl -O https://weechat.org/files/scripts/colorize_nicks.py
cd ~/.weechat/python/autoload
ln -sf ../colorize_nicks.py .
cd ~/.weechat/perl
curl -O https://weechat.org/files/scripts/buddylist.pl
curl -O https://weechat.org/files/scripts/colorize_lines.pl
cd ~/.weechat/perl/autoload
ln -s ../buddylist.pl .
ln -sf ../colorize_lines .
```

### Others

```
git clone git://gitlab.com/tmendes/cfgfiles.git $HOME/.cfg
git clone git://gitlab.com/tmendes/scripts.git $HOME/.local/share/scrips
git clone git://gitlab.com/tmendes/labs.git $HOME/Documents/devel/labs
```

### Bash config

```
mkdir ~/.bash
curl https://raw.githubusercontent.com/riobard/bash-powerline/master/bash-powerline.sh > ~/.bash/bash-powerline.sh
git clone https://github.com/magicmonty/bash-git-prompt.git ~/.bash/.bash-git-prompt --depth=1
```

### Fonts

sudo pacman -S awesome-terminal-fonts

### Gnome extensions and themes


#### Gnome Extensions
* https://micheleg.github.io/dash-to-dock/
* https://github.com/eonpatapon/gnome-shell-extension-caffeine
* https://github.com/RaphaelRochet/arch-update

#### Gnome Theme (Vimix Laptop Beryl)
* https://www.gnome-look.org/p/1013698/

### Icon Theme

sudo pacman -S papirus-icon-theme
