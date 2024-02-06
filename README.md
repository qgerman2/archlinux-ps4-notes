## very rough Arch linux install notes
### for baikal ps4 slim cuh-2115a firmware 9.00

updated 6/2/2024

download list (most of these files are available in this repo):
- mira and linux payloads obtained from sleirsgoevy 900-host repo
- latest kernel from ps4linux.com
- initramfs.cpio.gz from psxitarch
- latest arch iso from https://archlinux.org/download/

flash arch iso onto a usb, boot in a pc
plug your usb/hdd where to install

check disks with fdisk -l

create new mbr partition table with fdisk

prepare partitions with cfdisk
- 1 50mb fat32 partition, type EFI or W95 FAT 32
- 2 30gb+ ext4 root filesystem partition, type Linux
- 3 optional fat32 to copy files from a windows pc, like games, type W95 FAT32

format them
```
mkfs.vfat -F 32 /dev/sdb1
mkfs.ext4 /dev/sdb2
mkfs.vfat -F 32 /dev/sdb3
```

label the root partition so that arch boots straight away with psxitarch initramfs
```
e2label /dev/sdb2 psxitarch
```

mount root partition and install arch
```
mount /dev/sdb2 /mnt
pacstrap /mnt base base-devel
```

chroot and change root password
```
arch-chroot /mnt
passwd
```
set up locale, keymap and timezone according to the arch install wiki

https://wiki.archlinux.org/title/installation_guide

update pacman database and install important stuff
```
pacman -Sy
pacman -S nano networkmanager iwd sddm
```

set networkmanager to work with iwd https://wiki.archlinux.org/title/NetworkManager#Using_iwd_as_the_Wi-Fi_backend
```
nano /etc/NetworkManager/conf.d/wifi_backend.conf
```
```
[device]
wifi.backend=iwd
```

enable networkmanager iwd
```
systemctl enable networkmanager
systemctl enable iwd
```
exit

copy the kernel bzImage and initramfs image onto the first partition

bootargs.txt fixes xorg video output for 5+ kernels.

vram.txt defines how much vram gb to assign out of the consoles 8 gb, the rest will be regular ram.

from here you should be good to go on your ps4, take the usb/ssd onto the console and launch the mira and linux payloads

** the linux payload doesnt seem to work with the latest psfree + exfathax jailbreaking methods as time of writing, stick to regular webkit exploits like xhost pro **

use iwd to connect to a wifi network

``
iwctl
station wlan0 show
station wlan0 scan networks
station wlan0 get-networks
station wlan0 connect "network name"
``

install plasma-desktop
install the video drivers https://github.com/Ps3itaTeam/ps4linux-video-drivers

allow users to use sudo
```
EDITOR=nano visudo
```
uncomment allow wheel users without password

create a user
```
useradd -m asd
```
add to wheel group
```
gpasswd -a asd wheel
```
give it a password
```
passwd asd
```
exit and log into the new user

install yay https://github.com/Jguer/yay

install downgrade with yay and downgrade both llvm-libs and lib32-llvm-libs to version 14.

enable sddm and log into x11 plasma

``
sudo systemctl enable ssdm
sudo systemctl start sddm
``

install meta-utils and vulkan-tools

- test xorg graphics with glxgears
- test vulkan with vkcube
