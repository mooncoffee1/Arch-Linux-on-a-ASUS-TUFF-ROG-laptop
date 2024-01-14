# Arch-Linux-on-a-ASUS-TUFF-ROG-laptop
how to install and use your's laptop functionallities on arch linux


## Acknowledgements

 - [More NVIDIA documentation](https://wiki.archlinux.org/title/NVIDIA)
 - [Asus Linux](https://asus-linux.org/)
   

## Step 1. Installation

First install Arch Linux the normal way with, or without archinstall. 
Select Nvidia drivers (proprietary). choose Grub as bootloader if u dont want problems. As for Desktop Enviroment, i prefer gnome, choose it for needed extensions.

## Step 2. Install YAY 
i think yay is very needed, u should have it for more packages.

```bash
sudo pacman -S --needed git base-devel
git clone https://aur.archlinux.org/yay.git
cd yay
makepkg -si
```


## Step 3. Asusctl

Follow the best tutorial on asus-linux site, on how to install asusctl, for rgb, power profiles, and gpu switching.
https://asus-linux.org/guides/arch-guide/


## Step 4. Mkinitcpio
open the Mkinitcpio file:
```bash 
    sudo nano /etc/mkinitcpio.conf
```

u need to add the following into the modules.

```bash
  MODULES=(nvidia nvidia_modeset nvidia_uvm nvidia_drm amdgpu)
```
once u did that, save, and then rebuild it 
```bash 
    sudo mkinitcpio -p linux-g14 
```
see i used linux-g14 as kernel to build, u will install it in step 3 on the asus-linux site, if u didnt then delete "-g14" leave just linux.

## Step 5. Kernel Parameters.
u will need some parameters in the kernel or smth. I dont know what does all of them, some are for suspend on gdm, its just to work properly, on my system works, soo should on yours too.

```bash
    sudo nano /etc/default/grub 
```

then where u see ``GRUB_CMDLINE_LINUX_DEFAULT`` , add the follwing in the " ".

```bash 
    loglevel=3 quiet splash vt.global_cursor_default=0 systemd.show_status=auto rd.udev.log_level=3  nvidia-drm.modeset=1 amd_pstate=active amd_iommu=off idle=nomwait amdgpu.gpu_recovery=1
```
if uk what are u doing, then remove what u dont like here, but if your not, then leave them like that.

now u need to update grub, do this:

```bash 
    grub-mkconfig -o /boot/grub/grub.cfg
```
or use grub-customizer, its in aur, for graphical interface.

## Step 6. NVIDIA Problems.

now as always NVIDIA is a pain in the ass, but we can make it to kinda work for us and to have good game experience, and overall experience.

With proprietary drivers that u installed with archinstall, it should work out of the box, but on gnome, please enable these:

```bash 
    sudo systemctl enable nvidia-persistenced.service nvidia-suspend.service nvidia-hibernate.service nvidia-resume.service nvidia-powerd.service 
```

works for me, it should work for u too.

now, run this to create a xorg config file for nvidia.

```bash 
    sudo nvidia-xconfig -a 
```
open the file
```bash
sudo nano /etc/X11/xorg.conf
```
here in the section ``ServerLayout`` add the following

```bash 
    Option         "AllowNVIDIAGPUScreens"
```
it should looks smth like this:

```
    Section "ServerLayout"
    Identifier     "layout"
    Screen      0  "Screen0"
    InputDevice    "Keyboard0" "CoreKeyboard"
    InputDevice    "Mouse0" "CorePointer"
    Option         "AllowNVIDIAGPUScreens"  # here its the needed line.
EndSection

```
now, from interned, its says that this will make your dGPU (nvidia) when idle to go to performance mode P8, and to consume 2 to 5 wats, instead of 15w like how did on my system.

## Step 7. Some optional things.

u will need some gnome extensions, that will make your life better.

install these:

asus gpu switcher extension: 
https://extensions.gnome.org/extension/5344/supergfxctl-gex/

power profile indicator: https://extensions.gnome.org/extension/6679/power-profile-indicator/

profile switcher based on battery or charging. https://extensions.gnome.org/extension/6583/auto-power-profile/


I found a cool udev rule to run applications on dedicated gpu, to not mess with settings and sometimes it dosent ever run on dedicated gpu.

Install ``switcheroo-control``

```bash 
yay -S switcheroo-control
```
then start and enable it 

```bash 
sudo systemctl enable switcheroo-control.service
sudo systemctl start switcheroo-control.service
```
now when u right click on a .desktop app u can see the option "Launch using Discrete Graphics Card"

![image](https://github.com/mooncoffee1/Arch-Linux-on-a-ASUS-TUFF-ROG-laptop/assets/118976333/4e15d7ea-6a54-4426-8a99-943307fb43ae)
