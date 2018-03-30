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

### Fix pacman.conf

```
/etc/pacman.conf

---------------------

#add to bottom of file:

[archlinuxfr]
    SigLevel = Never
    Server = http://repo.archlinux.fr/$arch

#Move the best mirror to the top (the one at your country?)

---------------------

#uncomment multilib
#uncomment color
```

### Packs to install

```
pacstrap /mnt base
```

### Create the fstab file and start arch to configure it

```
genfstab -p /mnt >> /mnt/etc/fstab
arch-chroot /mnt
```

## Instal packs

```
pacman -S base-devel \
         wpa_supplicant dialog networkmanager networkmanager-openvpn \
         xf86-input-libinput xf86-video-intel xclip \
         xdg-user-dirs \
         openssh \
         bash-completion \
         grub efibootmgr intel-ucode \
         lm_sensors acpi \
         tlp tlp-rdw \
         ufw \
         sudo \
         tmux htop pass pass-otp exa \
         rtorrent wget weechat neomutt w3m \
	     calcurse abook python-httplib2 \
         aspell \
         neovim python-neovim \
         gdb valgrind ctags cscope clang strace bc \
         git tig \
         meson ninja \
         rust \
         lua \
         jdk8-openjdk \
         android-udev \
         python python-requests python-pip \
         nodejs mongodb yarn \
         rubygems \
         the_silver_searcher \
         docker docker-compose \
         gtk2-2 expac \
         gdm \
         gnome-control-center gnome-session gnome-setting-daemon \
         gnome-shell gnome-terminal gnome-tweak gnome-keyring \
         gnome-shell-extensions gvfs-mtp gvfs-smb \
         nautilus \
         eog evince \
         firefox chromium \
         flatpak \
         guvcview \
         mpv youtube-dl \
         homebank anki \
         papirus-icon-theme arc-gtk-theme \
         awesome-terminal-fonts noto-fonts-emoji ttf-hack ttf-symbola \
         powerline-fonts\
         libreoffice
```

## Configure it

### Timezone

```
ln -sf /usr/share/zoneinfo/America/Sao_Paulo /etc/localtime
hwclock --systohc
```

### Locale

```
/etc/locale.conf

---------------------

LANG=en_US.UTF-8
```

```
/etc/locale.gen

---------------------

LANG=en_US.UTF-8
```

```
locale-gen
```

### Network

```
echo "rabbit_of_caerbannog" > /etc/hostname
```

```
/etc/hosts

---------------------

127.0.0.1	localhost
::1         localhost
127.0.1.1	rabbit_of_caerbannog.local rabbit_of_caerbannog

```

### Environment

```
/etc/environment

---------------------

GTK_IM_MODULE=cedilla
QT_IM_MODULE=cedilla
```

### Sound config

```
/etc/modprobe.d/intel-hda.conf

---------------------

options snd_hda_intel index=1,0
```

### Beep off

```
/etc/inputrc

---------------------

set bell-style none
```


### Console

```
/etc/vconsole.conf

---------------------

FONT=latarcyrheb-sun32
KEYMAP=us-acentos
```


### Blacklist (Dell XPS)

```
/etc/modprobe.d/psmouse-blacklist.conf

---------------------

blacklist psmouse
```

### Grub

```
/etc/defaults/grub

---------------------

GRUB_TIMEOUT=0
GRUB_GFXMODE=1024x768
GRUB_CMDLINE_LINUX_DEFAULT="quiet"

```

### Buld initramfs and install Grub

```
mkinitcpio -p linux
grub-mkconfig -o /boot/grub/grub.cfg
grub-install --recheck /dev/sda
```

### Services

```
systemctl enable gdm
systemctl enable ufw
systemctl enable tlp
systemctl enable tlp-sleep
systemctl enable bluetooth
systemctl enable networkmanager
systemctl enable wpa_supplicant.service
systemctl disable dhcpcd@.service
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

### Bluetooth

### GDM, Sound and bluetooth sound

```
mkdir -p ~gdm/.config/systemd/user
ln -s /dev/null ~gdm/.config/systemd/user/pulseaudio.socket
```

### ufw config

```
ufw enable
ufw default allow outgoing
ufw default deny incoming
```

### Adding an user

```
useradd -m -g users -s /bin/sh <username>
passwd <username>
echo '<username> ALL=(ALL) ALL' > /etc/sudoers.d/<username>
gpasswd -a username network,wheel,vboxusers
```

### Root Access

* [INFO] If you want to disable the root access try this next command

```
passwd -l root
```

* [INFO] If you want to bring it back try this next command

```
passwd root
```

### Python modules

```
pip install pip-autoremove
```

## As user

### Neovim plugin manager

```
curl -fLo ~/.local/share/nvim/site/autoload/plug.vim --create-dirs \
    https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

### Gnome config

 * Keyboard: English (US, intl, with dead keys)

### Bash config

```
mkdir -p ~/.usr/share/bash
git clone https://github.com/nojhan/liquidprompt.git ~/.usr/share/bash/liquidprompt
```

### Gnome extensions and themes

### GIMP and the XPS HIDPI Display (Dell XPS)

* https://github.com/jedireza/gimp-hidpi

### Others (You don't have to follow the next instructions)

```
# Projects

git clone git@gitlab.com:tmendes/archlinux.git
git clone git@gitlab.com:tmendes/markdown-cv.git
git clone git@gitlab.com:tmendes/scripts.git
git clone git@gitlab.com:tmendes/wlNomad.git
git clone git@gitlab.com:tmendes/data_structure.git
git clone git@gitlab.com:tmendes/FoodScaleDroid.git
git clone git@gitlab.com:tmendes/vcfConverter.git
git clone git@gitlab.com:tmendes/38lbackpack
git clone https://gitlab.com/tmendes/bgstats
git clone git@gitlab.com:tmendes/BirthDayDroid.git
git clone git@gitlab.com:tmendes/FoodRestrictions.git
git clone git@gitlab.com:tmendes/invaders.love.git
git clone git@gitlab.com:tmendes/tosdrpy.git
git clone git@gitlab.com:tmendes/DadosD.git

# Labs

git clone git@gitlab.com:tmendes/unicamp.git
git clone git@gitlab.com:tmendes/labs.git
git clone git@gitlab.com:tmendes/cpplabs.git

# Config
git clone git@gitlab.com:tmendes/dotfiles.git ~/.dotfiles

```

### Firefox

To get rid of Pocket, Firefox users should head to about:config and set the
extensions.pocket.enabled preference to false.
