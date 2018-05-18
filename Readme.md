 <img src="https://news-cdn.softpedia.com/images/news2/How-to-Install-Third-Party-Apps-in-Arch-Linux-2.png" width="125" height="100"> 

 # Arch Linux :penguin: Legacy Install

Note** this is for non-dual boot, GNOME Setup

## WINDOWS Download ISO and mount via rufus or similar
or
## LINUX Download ISO and copy to usb via bash
~~~
$ dd if=archlinux.img of=/dev/[sdX] bs=16M && sync # on linux
~~~

### Boot from the usb. If the usb fails to boot, make sure that secure boot is disabled in the BIOS configuration.

### Format disk and mount partitions

Use GParted
* Partition table is GPT
* the EFI partition is FAT32, around 250MB
* the root partition is ext4, around 35GB
* the home partition is ext4, rest of the disk

~~~
$ mount /dev/sda5 /mnt
$ mkdir /mnt/esp
$ mount /dev/sda3 /mnt/esp
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

~~~