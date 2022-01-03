## very rough Arch linux install notes
### for baikal ps4 slim cuh-2115a firmware 9.00

download list:
    mira https://www.psxhax.com/threads/poobs4-ps4-9-00-jailbreak-exploit-via-chendochap-with-updated-payloads.10492/
    linux loader payloads, baikal kernel 4.19.93 and usb initramfs from https://github.com/Nazky/PS4Linux-Payloads
    latest arch iso from https://archlinux.org/download/

flash arch iso onto a usb, boot in a pc
plug your usb/hdd where to install

check disks with fdisk -l, prepare partitions with cfdisk
1 50mb fat32 partition, type EFI
2 30gb+ ext4 root filesystem partition, type Linux
3 optional fat32 to copy files from a windows pc, like games, type FAT32

format them
mkfs.vfat -F 32 /dev/sdb1
mkfs.ext4 /dev/sdb2
mkfs.vfat -F 32 /dev/sdb3

label the root partition so that arch boots straight away
e2label /dev/sdb2 psxitarch

mount root partition and install arch
mount /dev/sdb2
pacstrap /mnt base base-devel

chroot and change root password
arch-chroot /mnt
passwd
set up locale, keymap and timezone according to the arch install wiki

update pacman database and install important stuff
pacman -Sy
pacman -S nano networkmanager iwd

set networkmanager to work with iwd https://wiki.archlinux.org/title/NetworkManager#Using_iwd_as_the_Wi-Fi_backend
nano /etc/NetworkManager/conf.d/wifi_backend.conf
[device]
wifi.backend=iwd

enable networkmanager iwd
systemctl enable networkmanager
systemctl enable iwd

exit

copy the kernel bzImage and initramfz image onto the first partition

from where you should be good to go on your ps4, take the usb/ssd onto the console and launch the mira and linux payloads

use iwd to connect to the a wifi network
install plasma-desktop
install the video drivers https://github.com/Ps3itaTeam/ps4linux-video-drivers

allow users to use sudo
EDITOR=nano visudo
uncomment allow wheel users without password

create a user
useradd -m asd
add to wheel group
gpasswd -a asd wheel
give it a password
passwd asd
exit and log into the new user

install yay https://github.com/Jguer/yay
yay downgrade and install old xorg https://github.com/hippie68/psxitarch-how-to/issues/12
reboot
write a short sh file to launch plasma wayland https://wiki.archlinux.org/title/KDE#Starting_Plasma
nano plasma-wayland.sh
XDG_SESSION_TYPE=wayland dbus-run-session startplasma-wayland
run the script and hope for the best

install meta-utils and vulkan-tools
test xorg graphics with glxgears
test vulkan with vkcube
