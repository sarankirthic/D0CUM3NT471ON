# 4rch L1nux Installation Guide

EFI Installation of Arch Linux

#### NOTE: This is basically the [4rch 1inux installation guide](https://wiki.archlinux.org/title/Installation_guide) but in a more simpler manner.

### Network connection
To ensure the network interface is listed and enabled
```bash
ip link
```
To check the connection
```bash
ping google.com
```

### Setting up the system clock

To set the system clock
```bash
timedatectl set-ntp true
```
To check the service status of the clock
```bash
timedatectl status
```
To list out all the timezones
```bash
timedatectl list-timezones
```
To set the selected timezone
```bash
timedatectl set-timezone
```

### Partitioning the disks

To list all the block devices
```bash
fdisk -l
```

fdisk /dev/the_disk
Type g ( to create a GPT partition table )
EFI  - Type n, then 1, press ENTER, then type +550M
SWAP - Type n, then 2, press ENTER, type +2G
Norm - Type n, then 3, press ENTER, press ENTER

- Changing the partition filesystem
	- Type t, then 1, then 1
	- Type t, then 2, then 19

- Write the table
	- Type w

- Make the file system
	- mkfs.fat -F32 /dev/sda1
	- mkswap /dev/sda2
	- swapon /dev/sda2
	- mkfs.ext4 /dev/sda3

- Mount the big partition
	- mount /dev/sda3 /mnt

- Install the base packages
	- pacstrap /mnt base linux linux-firmware

- Generate the filesystem
	- genfstab -U /mnt >> /mnt/etc/fstab

- arch-chroot /mnt
- set timezone
	- ls /usr/share/zoneinfo
	- ls /usr/share/zoneinfo/region
	- ln -sf /usr/share/zoneinfo/region/city /etc/localtime

- install the important/necessary packages
	- pacman -S nano networkmanager git sudo grub efibootmgr dosfstools os-prober mtools

- set hardware clock
	- hwclock --systohc

- set locale clock
	- nano /etc/locale.gen
	- locale-gen

- set hostname
	- nano /etc/hostname
	- set the hostname

- set host file
	- nano /etc/host
	- add the following lines

```bash
	127.0.0.1 	localhost
	::1  		localhost
	127.0.1.1	<hostname>.localdomain		<hostname>
```

- create users and password
	- passwd, tyoe the new passwd
	- useradd -m <username>
	- passwd <username>

- need to "<username>" to the wheel group to give him sudo previlages
	- usermod -aG wheel,audio,video,optical,storage <username>

- editing the sudo file
	- EDITOR=nano visudo
	- uncomment %wheel ALL=(ALL) ALL

- make EFI directory in boot directory
	- mkdir /boot/EFI
	- mount /dev/sda1 /boot/EFI

- grub proper install
	- grub-install --target=x86_64-efi --bootloader-it=grub_uefi --recheck
	- grub-mkconfig -o /boot/frub/grub.cfg

- pacman -S 

- Enable 
	- systemctl enable NetworkManager

- Exit out of chroot
	- Exit

- unmount
	- umount -l /mnt

- Reboot the system

- login as the <usernane>

- 
