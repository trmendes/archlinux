## Pre-Installation

### Connect to a wifi network

```
# wifi-menu
```

> I've updated my wifi-card to make it more compatible with my Linux machinie

> https://www.amazon.com/Intel-8260-IEEE-802-11ac-Adapter/dp/B0197W86IE/ref=sr_1_1?s=electronics&ie=UTF8&qid=1498679996&sr=1-1&keywords=intel+8260

### Enable NTP

```
# timedatectl set-ntp true
```

### Configure your disk

```
# gfisk /dev/sda
```

### Format your disk and swap on

```
# mkfs.fat -F32 /dev/sda1
# mkswap -L "swap" /dev/sda2
# swapon /dev/sda2
# mkfs.ext4 -L arch /dev/sda3
```

### mount disks

```
# mount /dev/sda3 /mnt
# mkdir -p /mnt/boot/EFI
# mount /dev/sda1 /mnt/boot/EFI
```

## Installation

### Fix pacman.conf

```
/etc/pacman.conf
---------------------
# Move the best mirror to the top (the one at your country?)
# sed -i 's/#multilib/multilib/' /etc/pacman.conf
# sed -i 's/#color/color/' /etc/pacman.conf
# add ILoveCandy
```

### Packs to install

```
# pacstrap /mnt base base-devel
```

### Create the fstab file and start arch to configure it

```
# genfstab -p /mnt >> /mnt/etc/fstab
# arch-chroot /mnt
```

## Instal packs

### Pacman

```
# pacman -S networkmanager networkmanager-openvpn \
         xorg-server-xwayland \
         xf86-input-libinput xf86-video-intel \
         xdg-user-dirs \
         grub efibootmgr \
         intel-ucode \
         pkgfile \
         bind-tools \
         lm_sensors acpi acpid tlp tlp-rdw \
         udevil \
         ntfs-3g \
         bluez \
         bash-completion \
         openssh \
         ufw sudo \
         git tig \
         gcc scdoc gdb \
         meson ninja cmake \
         rust \
         lua \
         jdk8-openjdk \
         python python-requests python-pip flake8 mypy \
         nodejs \
         hugo \
         android-udev \
         noto-fonts-emoji ttf-hack ttf-dejavu awesome-terminal-fonts \
         ttf-font-awesome ttf-ionicons \
         neovim python-neovim \
         aspell aspell-pt aspell-en aspell-it aspell-es \
         pulseaudio pulseaudio-bluetooth pulseaudio-jack pulsemixer pacutils \
         pulseaudio-alsa alsa-utils \
         youtube-dl beets \
         subdl moc \
         syncthing \
         repgrip fd \
         htop pass pass-otp exa bat expac tldr \
         ranger ffmpegthumbnailer highlight odt2txt poppler \
         wl-clipboard \
         kitty \
         dunst rofi \
         imagemagick \
         blueman \
         weechat python2-websocket-client python2-six \
         transmission-gtk \
         firefox thunderbird \
         libreoffice-fresh \
         mpv imv mupdf \
         wireshark-gtk wifite \
         mono freerdp remmina ansible chromium \
         sway swaybg swaylock swayidle grim slurp
```

### AUR

```
git clone https://aur.archlinux.org/yay.git
cd yay
makepkg -si

yay -S light
yay -S djmount
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
echo LANG=en_US.UTF-8 > /etc/locale.conf
```

```
/etc/locale.gen
---------------------
sed -i "s/#en_US.UTF-8/en_US.UTF-8/" /etc/locale.gen
```

```
# locale-gen
```

### Network

```
# echo "amora" > /etc/hostname
```

```
/etc/hosts
---------------------
127.0.0.1	localhost
::1             localhost
127.0.1.1	caramujo.local

```

### Powerbutton to suspend

```
sudo nvim /etc/systemd/logind.conf
---------------------
HandlePowerKey=suspend
```

### Webcam config

```
/etc/modprobe.d/ucvideo.conf
---------------------
options uvcvideo nodrop=1
options uvcvideo timeout=5000
options uvcvideo quirks=128
```

### Libdir config

```
/etc/ld.so.conf.d/usrlocal.conf
---------------------
/usr/local/lib
```

### Sound config

```
/etc/modprobe.d/intel-hda.conf
---------------------
options snd_hda_intel index=1,0
```

### Bluetooth config

```
/etc/bluetooth/audio.conf
---------------------
AutoEnable=false
```

```
/etc/bluetooth/audio.con
---------------------
Enable=Source,Sink,Media,Socket

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

### Switch off your touchScreen \o/ \o/

```
/etc/udev/rules.d/80-touchscreen.rules
---------------------
SUBSYSTEM=="usb", ATTRS{idVendor}=="04f3", ATTRS{idProduct}=="20d0", ATTR{authorized}="0"
```

### Suspend the system when battery drops to 10%

```
/etc/udev/rules.d/99-lowbat.rules
---------------------
SUBSYSTEM=="power_supply", ATTR{status}=="Discharging", ATTR{capacity}=="[0-10]", RUN+="/usr/bin/systemctl hibernate"
```

### Blacklist (Dell XPS)

```
/etc/modprobe.d/psmouse-blacklist.conf
---------------------
blacklist psmouse
```

### Journalfs

```
/etc/systemd/journald.conf
---------------------
Storage=volatile
SystemMaxUse=10M
RuntimeMaxUse=10M
```

### Grub

```
/etc/defaults/grub
---------------------
GRUB_TIMEOUT=0
GRUB_GFXMODE=1024x768

```

### Buld initramfs and install Grub

```
# mkinitcpio -p linux
# grub-mkconfig -o /boot/grub/grub.cfg
# grub-install --recheck /dev/sda
```

### ufw config

```
# ufw enable
# ufw default allow outgoing
# ufw default deny incoming
# ufw allow Transmission
# ufw allow syncthing
```

### Adding an user

```
# useradd -m -g users -s /bin/sh <username>
# passwd <username>
# echo '<username> ALL=(ALL) ALL' > /etc/sudoers.d/<username>
# gpasswd -a username network,wheel,storage,video,libvirt,systemd-journal
```

### Services

```
# systemctl enable thermald
# systemclt enable acpid
# systemctl enable ufw
# systemctl enable tlp
# systemctl enable tlp-sleep
# systemctl enable dnsmasq
# systemctl enable dnscrypt-proxy.service
# systemctl enable networkmanager

# systemctl disable dhcpcd@.service
# systemctl disable man-db.service

# systemctl mask lvm2-monitor.service
# systemctl mask systemd-rfkill
# systemctl mask systemd-rfkill.socket
# systemctl mask geoclue
```

### Root Access

* [INFO] If you want to disable the root access try this next command

```
# passwd -l root
```

* [INFO] If you want to bring it back try this next command

```
# passwd root
```

### Python modules

```
# pip install pip-autoremove
```

## Do this as a user

### Neovim plugin manager

```
# curl -fLo ~/.local/share/nvim/site/autoload/plug.vim --create-dirs \
    https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

### Android Studio

* Android Studio – No space left on device

> Solution – temporarily increase the size of the /tmp partition

```
# sudo mount -o remount,size=8G,noatime /tmp;
```
### Transmission-Gtk blocklist

```
http://john.bitsurge.net/public/biglist.p2p.gz
```
