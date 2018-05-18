 <img src="https://news-cdn.softpedia.com/images/news2/How-to-Install-Third-Party-Apps-in-Arch-Linux-2.png" width="125" height="100"> 

 # Arch Linux Legacy Install

Note** this is for non-dual boot, GNOME Setup

## WINDOWS Download ISO and mount via rufus or similar
or
## LINUX Download ISO and copy to usb via bash
~~~
$ dd if=archlinux.img of=/dev/[sdX] bs=16M && sync # on linux
~~~

### Boot from the usb. If the usb fails to boot, make sure that secure boot is disabled in the BIOS configuration.

## Format disk and mount partitions
~~
$ lsblk // list devices
$ gdisk [/dev/sda] > x > z > y > y
$ cgdisk [/dev/sda]
~~
Use GParted
* Partition table is GPT
* the EFI partition is FAT32, around 250MB
* the swap partition is swap, [*GB]
* the root partition is ext4, to last

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
$ pacstrap /mnt base
$ genfstab -U /mnt >> /mnt/etc/fstab
$ arch-chroot /mnt/
$ LANG=C perl -i -pe 's/#(en_US.UTF)/$1/' /etc/locale.gen
$ locale-gen
$ localectl set-locale LANG="en_US.UTF-8"
$ echo [myhostname] > /etc/hostname
$ pacman -S dialog wpa_supplicant refind-efi
$ cp /usr/share/refind/refind_x64.efi /esp/EFI/Boot/bootx64.efi
$ cp -r /usr/share/refind/drivers_x64/ /esp/EFI/Boot/
$ echo 'extra_kernel_version_strings linux,linux-hardened,linux-lts,linux-zen,linux-git;' > /esp/EFI/Boot/refind.conf
$ echo 'fold_linux_kernels false' >> /esp/EFI/Boot/refind.conf
$ passwd
$ useradd -m -G wheel -s /bin/bash [archie]
$ passwd [archie]
$ perl -i -pe 's/#(%wheel ALL=(ALL) ALL)/$1/' /etc/sudoers
$ exit

## Boot Loader and Packages 

~~~
// Boot Loader

$ pacman -S grub // Setup GRUB bootloader

$ grub-install /dev/sda

$ grub-mkconfig -o /boot/grub/grub.cfg // configures grub loader

$ exit

$ unmount

$ reboot

// Packages 

$ sudo pacman -S pulseaudio pulseaudio-alsa // install audio

// install graphics > select option (1) intergrated
$ sudo pacman -S xorg xorg-xinit 
~~~




## Install GNOME & Login manager

~~~
$ echo "exec gnome-session" > ~/.xinitrc // create a file of init for guid

$ sudo pacman -S gnome // install gnome desktop

// install random needed programs

$ startx // starts GUI 

$ sudo pacman -S sddm // install login manager

$ sudo systemctl enable sddm.service // run login manager service

$ reboot
~~~
