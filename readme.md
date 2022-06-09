Arch Linux
---

### boot medium
you can ssh into the boot medium for ~~copy and pasting~~ ease of use
```
systemctl start sshd
passwd
ip addr  
```


### disk config
```
lsblk  # change /dev/sda to whatever you want to use
cgdisk /dev/sda  # creating the partitions
>----->
> [ NEW ]
> (default)
> 512M
> ef00
> boot

> [ NEW ]
> (default)
> 1G  # this is your swap size
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

# formatting the partitions
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
pacstrap /mnt base linux linux-firmware base-devel vi nano man-db  # install required packages

genfstab -U /mnt
genfstab -U /mnt >> /mnt/etc/fstab
arch-chroot /mnt

ln -sf /usr/share/zoneinfo/Canada/Eastern /etc/localtime  # sorry, canada eh!
hwclock --systohc

vi /etc/locale.gen  # keyboard stuffs
>----->
en_US.UTF-8 UTF-8
>-----<
locale-gen
vi /etc/locale.conf
>----->
LANG=en_US.UTF-8
>-----<

vi /etc/hostname  # user@arch:$
>----->
arch
>-----<

mkinitcpio -P
```

### boot loader
```
passwd
pacman -Sy grub efibootmgr

# if you are installing to a removable drive, uncomment --removable. otherwise, exclude.
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB  #--removable
grub-mkconfig -o /boot/grub/grub.cfg

exit
shutdown -r now  # when rebooting make sure you boot onto your fresh install, not back onto the boot medium
```

YOU CAN LOG IN AS ROOT NOW :D
---

### networking
```
systemctl start systemd-networkd systemd-resolved

networkctl list  # enp4s0 is my network interface device name

nano /etc/systemd/network/20-wired.network
>----->
[Match]
Name=enp4s0  # use your network interface device name here
 
[Network]
DHCP=yes
>-----<

systemctl restart systemd-networkd systemd-resolved
systemctl enable systemd-networkd systemd-resolved
```

### users
you cannot install `yay` on root
```
useradd -g users -G wheel,storage,power -m wncry
passwd wncry
visudo  # optional, but highly recommended
>----->
%wheel ALL=(ALL) NOPASSWD: ALL
>-----<
```

### desktop environments
```
# nvidia drivers
pacman -S nvidia nvidia-utils 

# amd drivers
idk lol

pacman -S openssh htop git xorg-server

nano /etc/ssh/sshd_config
>----->
PermitRootLogin yes
>-----<

systemctl restart sshd
systemctl enable sshd

# you will need to restart and login under your user to install yay
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
my personal favourite :)
```
yay -S ly  # display manager
systemctl enable ly

pacman -S feh alacritty ranger
yay -S librewolf  # this will take a long time. best to be run overnight (at least for my crappy hardware lol)

pacman -S ratpoison  # desktop environment + window manager
ls /usr/shar/xsessions/
sudo cp /etc/X11/sessions/ratpoison.desktop /usr/share/xsessions/  # may not need to be done if it alr exists

vim .ratpoisonrc
>----->
exec feh --bg-scale ~/.wallpaper.png  # sets a wallpaper
exec /usr/bin/rpws init 4 -k  # opens four environments
exec ratpoison -c "banish"  # moves mouse to the bottom left corner

set border 3  # window padding

# application execution
bind a exec alacritty  # executed by 'ctrl+t a'
bind l exec librewolf

# window management
bind M-R remove
bind R resize  # executed by 'ctrl+t shift+r'
bind V vsplit
bind H hsplit

# environments
bind 1 exec rpws 1  # executed by 'ctrl+t 1'
bind 2 exec rpws 2
bind 3 exec rpws 3
bind 4 exec rpws 4
>-----<

cat /proc/asound/cards  # optional step if you are having troubles with default audio devices
sudo vim /etc/asound.conf
>----->
defaults.pcm.card 1
defaults.ctl.card 1
```

## gaming (nvidia)
```
sudo ln -sf /run/systemd/resolve/stub-resolv.conf /etc/resolv.conf

nano /etc/pacman.conf
>----->
[multilib]
Include = /etc/pacman.d/mirrorlist
>-----<

pacman -Syu  # requirements for steam gaming on linux
sudo pacman -S --needed nvidia-dkms nvidia-utils lib32-nvidia-utils nvidia-settings vulkan-icd-loader lib32-vulkan-icd-loader lutris steam

# installing wine
yay -S wine-stable proton

sudo pacman -S --needed wine-staging giflib lib32-giflib libpng lib32-libpng libldap lib32-libldap gnutls lib32-gnutls mpg123 lib32-mpg123 openal lib32-openal v4l-utils lib32-v4l-utils libpulse lib32-libpulse libgpg-error lib32-libgpg-error alsa-plugins lib32-alsa-plugins alsa-lib lib32-alsa-lib libjpeg-turbo lib32-libjpeg-turbo sqlite lib32-sqlite libxcomposite lib32-libxcomposite libxinerama lib32-libgcrypt libgcrypt lib32-libxinerama ncurses lib32-ncurses opencl-icd-loader lib32-opencl-icd-loader libxslt lib32-libxslt libva lib32-libva gtk3 lib32-gtk3 gst-plugins-base-libs lib32-gst-plugins-base-libs vulkan-icd-loader lib32-vulkan-icd-loader
```

# YOU JUST INSTALLED ARCH :D
you can say with confidence that 'iusearchbtw'
