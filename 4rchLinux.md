# 4rch L1nux Installation Guide

EFI Installation of Arch Linux
**NOTE: This is compressed guide of the [4rch 1inux installation guide](https://wiki.archlinux.org/title/Installation_guide)**

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

### Changing the partition filesystem
Type t, then 1, then 1
Type t, then 2, then 19

### Write the table
	Type w

### Make the file system
```bash
mkfs.fat -F32 /dev/sda1
mkswap /dev/sda2
swapon /dev/sda2
mkfs.ext4 /dev/sda3
```

### Mount the big partition
```bash
mount /dev/sda3 /mnt
```

### Install the base packages
```bash
pacstrap /mnt base linux linux-firmware
```

### Generate the filesystem
```bash
genfstab -U /mnt >> /mnt/etc/fstab
```
```bash
arch-chroot /mnt
```
### set timezone
```bash
ls /usr/share/zoneinfo
ls /usr/share/zoneinfo/region
ln -sf /usr/share/zoneinfo/region/city /etc/localtime
```

### install the important/necessary packages
```bash
pacman -S nano networkmanager git sudo grub efibootmgr dosfstools os-prober mtools
```

### set hardware clock
```bash
hwclock --systohc
```

### set locale clock
```bash
nano /etc/locale.gen

locale-gen
```
### set hostname
```bash
nano /etc/hostname
```

### set host file
```bash
nano /etc/host
```
add the following lines

```bash
127.0.0.1 	localhost
::1  		localhost
127.0.1.1	<hostname>.localdomain		<hostname>
```

### create users and password

passwd, type the new passwd

useradd -m <mark>username</mark>

passwd <mark>username</mark>

need to <mark>username</mark> to the wheel group to give him sudo previlages
```bash
usermod -aG wheel,audio,video,optical,storage <username>
```

editing the sudo file
```bash
EDITOR=nano visudo
```
uncomment %wheel ALL=(ALL) ALL

### make EFI directory in boot directory
```bash
mkdir /boot/EFI
mount /dev/sda1 /boot/EFI
```

grub proper install
```bash
grub-install --target=x86_64-efi --bootloader-it=grub_uefi --recheck
grub-mkconfig -o /boot/frub/grub.cfg
```

Enable network manager
```bash
systemctl enable NetworkManager
```

Exit out of chroot
```bash
exit
```
	
### unmount
```bash
umount -l /mnt
```

### Reboot the system
```bash
reboot now / shutdown now 
```

### login as the <mark>username</mark>
