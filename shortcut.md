Arch Linux shortcut
---

### boot medium
```
systemctl start sshd
passwd
ip addr
```

### disk config
```
lsblk

cgdisk /dev/sdb && mkfs.fat -F32 /dev/sdb1 && mkswap /dev/sdb2 && mkfs.ext4 /dev/sdb3 && mount /dev/sdb3 /mnt && swapon /dev/sdb2 && mkdir -p /mnt/boot && mount /dev/sdb1 /mnt/boot && pacstrap /mnt base linux linux-firmware base-devel vi nano man-db && genfstab -U /mnt && genfstab -U /mnt >> /mnt/etc/fstab && arch-chroot /mnt

>----->
> [ NEW ]
> (default)
> 512M
> ef00
> boot

> [ NEW ]
> (default)
> 1G
> 8200
> swap

> [ NEW ]
> (default)
> (default)
> (default)
> rootfs

> [ WRITE ]
> [ QUIT  ]
#>-----<
```
### boot config + boot loader
```
ln -sf /usr/share/zoneinfo/Canada/Eastern /etc/localtime && hwclock --systohc && echo en_US.UTF-8 UTF-8 > /etc/locale.gen && echo LANG=en_US.UTF-8 > /etc/locale.conf && locale-gen && echo 'archlinux' > /etc/hostname && mkinitcpio -P && pacman -Syy grub efibootmgr && grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB --removable && grub-mkconfig -o /boot/grub/grub.cfg && passwd && exit

shutdown -r now
```

### resume at [networking](https://github.com/wncry/arch-linux-installation-guide/blob/main/README.md#networking)

