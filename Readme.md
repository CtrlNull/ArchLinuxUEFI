 <img src="https://news-cdn.softpedia.com/images/news2/How-to-Install-Third-Party-Apps-in-Arch-Linux-2.png" width="125" height="100"> 

 # Arch Linux Legacy Install

Note** this is for non-dual boot, GNOME Setup

## WINDOWS Download ISO and mount via rufus or similar
or
## LINUX Download ISO and copy to usb via bash
~~~
$ dd if=archlinux.img of=/dev/[sdX] bs=16M && sync # on linux
~~~

#### Boot from the usb. If the usb fails to boot, make sure that secure boot is disabled in the BIOS configuration.

## Check your internet connection
~~~
$ ping google.com
~~~

## Check if EFI variables
~~~
$ efivar -l
~~~

## Format disk and mount partitions
~~~
$ lsblk // list devices

$ gdisk [/dev/sda] // > x > z > y > y

$ cgdisk [/dev/sda]
~~~
## Use GParted
* Partition table is GPT
* the boot, EF00 HexCode: EF00 partition is FAT32, around 250MB
* the swap, HexCode:8200 partition is swap, [*GB]
* the root, HexCode: partition is ext4, to last
* Write > yes

### Apply file system
~~~
$ mkfs.fat -F32 /dev/sda1
~~~

### Prepare swap 
~~~
$ mkswap /dev/sda2
$ swapon /dev/sda2
~~~

### Make root partion sda4
~~~
$ mkfs.ext4/dev/sda3
~~~

## Mount drives
~~~
$ mount /dev/sda3 /mnt
$ mkdir /mnt/boot
$ mount /dev/sda1 /mnt/boot
~~~

## Backup Mirror List
~~~
$ cp /etc/pacman.d/mirrorlist /etc/pacman.d/mirrorlist.bak
~~~

## Install Arch

* '[example]' is your personal input
* Datetime/region is for my personal location

~~~
$ pacstrap /mnt base base-devel
$ genfstab -U -p /mnt >> /mnt/etc/fstab
$ nano /mnt/etc/fstab // check file and write out

$ arch-chroot /mnt/

$ nano /etc/local.gen
# remove # @ en_US.UTF-8
$ locale-gen
$ echo LANG=en_US.UTF-8 > /etc/locale.conf
$ export LANG=en_US.UTF-8

$ ls /usr/share/zoneinfo/
$ ln -s /usr/share/zoneinfo/America/New_York > /etc/localtime
$ hwclock --systohc --utc

$ echo [myhostname] > /etc/hostname

$ sudo systemctl enable fstrim.timer

# Setup repo access

$ sudo nano /etc/pacman.conf
### add entry > 
[archlinuxfr]
SigLevel = Never
Server = http://repo.archlinux.fr/$arch

uncomment > multilib
###

$ sudo pacman -Sy

$ passwd
$ useradd -m -g users -G wheel,storage,power -s /bin/bash [archie]
$ passwd [archie]

### Modify sudoers file

$ EDITOR=nano visudo
uncomment %wheel
## add >
Defaults rootpw

## check if efivars are mounted
$ mount -t efivars /sys/firware/efi/efivars/

$ bootctl install

$ sudo nano /boot/loader/entries/arch.conf
###
title Arch Linux
linux vmlinuz-linux
initrd /initramfs-linux.img
###

$ echo "options root=PARTUUID=$(blkid -s PARTUUID -o value /dev/sda3) rw" >> boot/loader/entries/arch.conf

$ sudo pacman -S intel-ucode

$ sudo nano /boot/loader/entries/arch.conf
###
initrd /intel-ucode.img
###
~~~

### Setup Internet device and install network manager
~~~
$ ip link

$ sudo systemctl enable dhcpcd@[enp0s2ofou1u3].service

$ sudo pacman -S networkmanager

$ sudo systemctl enable NetworkManager.service 
~~~

### Done with Arch Linux Setup

~~~
$ exit
~~~

## Graphics and audio(Laptop)
~~~

// Packages 

$ sudo pacman -S pulseaudio pulseaudio-alsa // install audio

// install graphics > select option (1) intergrated
$ sudo pacman -S xorg xorg-xinit 
~~~

## Install GNOME & Login manager

~~~
$ echo "exec gnome-session" > ~/.xinitrc // create a file of init for guid

$ sudo pacman -S gnome // install gnome desktop enter > enter >

// install random needed programs

$ startx // starts GUI 

$ sudo pacman -S sddm // install login manager

$ sudo systemctl enable sddm.service // run login manager service

$ reboot
~~~
