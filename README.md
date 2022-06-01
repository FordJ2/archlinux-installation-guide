Arch Linux
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
cgdisk /dev/sda
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
>-----<

lsblk
mkfs.fat -F32 /dev/sda1
mkswap /dev/sda2
mkfs.ext4 /dev/sda3

mount /dev/sda3 /mnt
swapon /dev/sda2
mkdir -p /mnt/boot
mount /dev/sda1 /mnt/boot
```

### boot config
```
pacstrap /mnt base linux linux-firmware base-devel vi nano man-db

genfstab -U /mnt
genfstab -U /mnt >> /mnt/etc/fstab
arch-chroot /mnt

ln -sf /usr/share/zoneinfo/Canada/Eastern /etc/localtime
hwclock --systohc
vi /etc/locale.gen
>----->
en_US.UTF-8 UTF-8
>-----<
locale-gen

vi /etc/locale.conf
>----->
LANG=en_US.UTF-8
>-----<

vi /etc/hostname
>----->
arch
>-----<

mkinitcpio -P
```

### boot loader
```
passwd
pacman -Sy grub efibootmgr

grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB  # --removable
grub-mkconfig -o /boot/grub/grub.cfg

exit
shutdown -r now

# LOGGED IN WOOOOOOOOOOOOOOO
```

### networking
```
systemctl start systemd-networkd systemd-resolved

networkctl list

nano /etc/systemd/network/20-wired.network
>----->
[Match]
Name=enp4s0
 
[Network]
DHCP=yes
>-----<

systemctl restart systemd-networkd systemd-resolved
systemctl enable systemd-networkd systemd-resolved
```

### users
```
useradd -g users -G wheel,storage,power -m wncry
passwd wncry
visudo
>----->
%wheel ALL=(ALL) NOPASSWD: ALL
>-----<
```

### desktop environments
```
pacman -S openssh htop git nvidia nvidia-utils cuda xorg-server

nano /etc/ssh/sshd_config
>----->
PermitRootLogin yes
>-----<

systemctl restart sshd
systemctl enable sshd

git clone https://aur.archlinux.org/yay-bin.git
cd yay-bin
makepkg -si
```

#### ---- gnome
```
pacman -S gnome gnome-tweaks
systemctl enable gdm 
```

#### ---- plasma
```
pacman -S plasma-meta #kde-applications
systemctl enable sddm
```

#### ---- xfce
```
pacman -S xfce xfce4-goodies lightdm lightdm-gtk-greeter
systemctl enable lightdm
```

#### ---- mate
```
pacman -S mate mate-extra lightdm lightdm-gtk-greeter
systemctl enable lightdm
```

#### ---- ratpoison
```
yay -S ly
systemctl enable ly

pacman -S ratpoison xcompmgr feh dmenu alacritty ranger
yay -S librewolf

sudo cp /etc/X11/sessions/ratpoison.desktop /usr/share/xsessions/
vim .ratpoisonrc
>----->
exec feh --bg-scale ~/.black.png
exec /usr/bin/rpws init 4 -k
exec ratpoison -c "banish"

set border 3

bind a exec alacritty
bind l exec librewolf
bind M-D exec dmenu_run

bind R resize
bind V vsplit
bind H hsplit

bind 1 exec rpws 1
bind 2 exec rpws 2
bind 3 exec rpws 3
bind 4 exec rpws 4
>-----<

cat /proc/asound/cards
sudo vim /etc/asound.conf
>----->
defaults.pcm.card 1
defaults.ctl.card 1
```

## gaming

```
sudo ln -sf /run/systemd/resolve/stub-resolv.conf /etc/resolv.conf

nano /etc/pacman.conf
>----->
[multilib]
Include = /etc/pacman.d/mirrorlist
>-----<

pacman -Syu
sudo pacman -S --needed nvidia-dkms nvidia-utils lib32-nvidia-utils nvidia-settings vulkan-icd-loader lib32-vulkan-icd-loader lutris steam

yay -S wine-stable proton librewolf-bin	#(beard time lessgooo)

sudo pacman -S --needed wine-staging giflib lib32-giflib libpng lib32-libpng libldap lib32-libldap gnutls lib32-gnutls mpg123 lib32-mpg123 openal lib32-openal v4l-utils lib32-v4l-utils libpulse lib32-libpulse libgpg-error lib32-libgpg-error alsa-plugins lib32-alsa-plugins alsa-lib lib32-alsa-lib libjpeg-turbo lib32-libjpeg-turbo sqlite lib32-sqlite libxcomposite lib32-libxcomposite libxinerama lib32-libgcrypt libgcrypt lib32-libxinerama ncurses lib32-ncurses opencl-icd-loader lib32-opencl-icd-loader libxslt lib32-libxslt libva lib32-libva gtk3 lib32-gtk3 gst-plugins-base-libs lib32-gst-plugins-base-libs vulkan-icd-loader lib32-vulkan-icd-loader
```

