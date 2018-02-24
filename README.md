# Arch Linux Installation

## Pre-Installation

### Connect to a wifi network

```
wifi-menu
```

> I've updated my wifi-card to make it more compatible with my Linux machinie

> https://www.amazon.com/Intel-8260-IEEE-802-11ac-Adapter/dp/B0197W86IE/ref=sr_1_1?s=electronics&ie=UTF8&qid=1498679996&sr=1-1&keywords=intel+8260

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
mkdir -p /mnt/boot/EFI
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

### Add yaourt to pacman

```
sudo vim /etc/pacman.conf

#add to bottom of file:

[archlinuxfr]
    SigLevel = Never
    Server = http://repo.archlinux.fr/$arch

#uncomment multilib
#uncomment color
```

```
pacman -S reflactor
#Select the 200 most recently synchronized HTTP or HTTPS mirrors, sort them by download speed, and overwrite the file /etc/pacman.d/mirrorlist:
sudo reflector --latest 200 --protocol http --protocol https --sort rate --save /etc/pacman.d/mirrorlist
sudo pacman -Syu
```

```
pacman -S base-devel \
         xf86-input-libinput xf86-video-intel xclip \
         xdg-user-dirs \
         openssh \
         pkgfile bash-completion \
         grub efibootmgr intel-ucode \
         tlp tlp-rdw \
         tmux htop pass pass-otp \
         ncdu exa \
         rtorrent wget weechat neomutt w3m \
         aspell \
         neovim python-neovim \
         gdb valgrind gtest ctags cscope clang clang-tools-extra strace \
         git tig \
         lua \
         jdk8-openjdk \
         python python-requests python-pip \
         nodejs mongodb eslint typescript \
         the_silver_searcher graphviz \
         docker \
         gdm \
         gnome-control-center gnome-session gnome-setting-daemon \
         gnome-shell gnome-terminal gnome-tweak-tools \
         gnome-shell-extensions gvfs-mtp gvfs-smb networkmanager-openvpn \
         eog evince \
         firefox chromium \
         flatpak \
         guvcview \
         mpv youtube-dl \
         papirus-icon-theme arc-gtk-theme \
         awesome-terminal-fonts noto-fonts-emoji ttf-hack
```

## Configure it

### Environment

```
sudo nvim /etc/environment
```

 * Add the lines

> GTK_IM_MODULE=cedilla
> QT_IM_MODULE=cedilla

### Sound config

```
sudo nvim /etc/modprobe.d/intel-hda.conf
```

 * Add the line

> options snd_hda_intel index=1,0

### Beep off

```
sudo nvim /etc/inputrc
```

 * Add the line

> set bell-style none

### SSH Config

```
sudo nvim /etc/ssh/sshd_config
```
 * Find and set this

> set PasswordAuthentification yes
> PermitEmptyPassowrds no

### Console

```
sudo nvim /etc/vconsole.conf
```
 * Add the lines

> FONT=latarcyrheb-sun32
> KEYMAP=us-acentos

### Logs

```
sudo nvim /etc/systemd/journald.conf
```
 * Add the line

> SystemMaxUse=50M

### Blacklist (Dell XPS)

```
sudo nvim /etc/modprobe.d/psmouse-blacklist.conf
```
 * Add the line

> blacklist psmouse


### Android (Galaxy S4)

```
sudo nvim /etc/udev/rules.d/51-android.rules
```

 * Add the line

> SUBSYSTEM=="usb", ATTR{idVendor}=="04e8", MODE="0666", SYMLINK+="android_adb"

### Grub

```
sudo nvim /etc/defaults/grub
```

* Find and edit

> GRUB_TIMEOUT=0
> GRUB_GFXMODE=1024x768

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
 * Add the line

> Enable=Source,Sink,Media,Socket


 ```
nvim /etc/bluetooth/main.conf
 ```

 * Add the line

> AutoEnable=true

 ```
 nvim /etc/pulse/default.pa
 ```
 * Add the line

 > load-module module-switch-on-connect

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
useradd -m -g users -G wheel,vboxusers -s /bin/sh <username>
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

### Install Apps using flatpak

```
flatpak install --from https://flathub.org/repo/appstream/org.libreoffice.LibreOffice.flatpakref
```

### Neovim plugin manager

```
curl -fLo ~/.local/share/nvim/site/autoload/plug.vim --create-dirs https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

### Gnome config

 * Keyboard: English (US, intl, with dead keys)

### Weechat notification system

```
mkdir -p ~/.weechat/python/autoload
mkdir -p ~/.weechat/perl/autoload

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

### Bash config

```
mkdir -p ~/.usr/bash
git clone https://github.com/nojhan/liquidprompt.git ~/.usr/bash/liquidprompt
```

### Gnome extensions and themes

#### Gnome Extensions

* https://micheleg.github.io/dash-to-dock/
* https://github.com/eonpatapon/gnome-shell-extension-caffeine
* https://github.com/RaphaelRochet/arch-update
* https://github.com/passingthru67/workspaces-to-dock

### GIMP and the XPS HIDPI Display (Dell XPS)

* https://github.com/jedireza/gimp-hidpi

### Others (You don't have to follow the next instructions)

```
mkdir -p ~/Documents/devel/gitlab/
mkdir -p ~/.local/share/

cd ~/Documents/devel/gitlab

git clone git@gitlab.com:tmendes/archlinux.git
git clone git@gitlab.com:tmendes/invaders.love.git
git clone git@gitlab.com:tmendes/tosdrpy.git
git clone git@gitlab.com:tmendes/unicamp.git
git clone git@gitlab.com:tmendes/labs.git
git clone git@gitlab.com:tmendes/38lbackpack
git clone git@gitlab.com:tmendes/FoodScaleDroid.git
git clone git@gitlab.com:tmendes/FoodRestrictions.git
git clone git@gitlab.com:tmendes/DadosD.git
git clone git@gitlab.com:tmendes/BirthDayDroid.git
git clone git@gitlab.com:tmendes/scripts.git

git clone git@gitlab.com:tmendes/dotfiles.git ~/.dotfiles

```

### Firefox

To get rid of Pocket, Firefox users should head to about:config and set the
extensions.pocket.enabled preference to false.
