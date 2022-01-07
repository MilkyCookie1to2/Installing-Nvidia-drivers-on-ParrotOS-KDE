# Installing-Nvidia-drivers-on-ParrotOS-KDE

Before we begin, a couple of notes:

**[Orginal source, but for Kali](https://github.com/europa502/Installing-Nvidia-drivers-on-Kali-Linux)**

***USE AT YOUR OWN RISK***

**This tutorial is for official NVIDIA Driver**

**Step 1:** Verify you have hybrid graphics

```
┌──(root💀milk)-[~]
└─# lspci | grep -E "VGA|3D"
01:00.0 VGA compatible controller: NVIDIA Corporation TU117M [GeForce GTX 1650 Mobile / Max-Q] (rev a1)
05:00.0 VGA compatible controller: Advanced Micro Devices, Inc. [AMD/ATI] Picasso (rev c1)
```
**Step 2:** Disable nouveau

```
echo -e "blacklist nouveau\noptions nouveau modeset=0\nalias nouveau off" > /etc/modprobe.d/blacklist-nouveau.conf
update-initramfs -u && reboot
```

**Step 3:** System will reboot and nouveau should be disabled. Verify if nouveau is disabled:

```
lsmod | grep -i nouveau
```
If shows nothing,means nouveau successfully disabled.

**Step 4:** Install nvidia driver 

I should install driver from [Nvidia's website](https://www.nvidia.ru/Download/index.aspx)

After downloading press **Ctrl+Alt+F3**
Enter your login and password

Enter command
```
sudo init 3
```
Enter password
Then again enter your login and password

Run installation
```
sudo sh ./NVIDIA-Linux-x86_64-470.94.run
```
**Step 5:** Now we have to find bus id of our nvidia card:

```
nvidia-xconfig --query-gpu-info | grep 'BusID : ' | cut -d ' ' -f6
```

it should show something like this:

```
PCI:1:0:0
```

This is our Bus ID.

**Step 6:** Now we generate /etc/X11/xorg.conf file with this bus ID according to [Nvidia's guide](http://us.download.nvidia.com/XFree86/Linux-x86_64/470.94/README/randr14.html):

```
Section "ServerLayout"
    Identifier "layout"
    Screen 0 "nvidia"
    Inactive "intel"
EndSection

Section "Device"
    Identifier "nvidia"
    Driver "nvidia"
    BusID "**PCI:1:0:0**"
EndSection

Section "Screen"
    Identifier "nvidia"
    Device "nvidia"
    Option "AllowEmptyInitialConfiguration"
EndSection

Section "Device"
    Identifier "intel"
    Driver "modesetting"
EndSection

Section "Screen"
    Identifier "intel"
    Device "intel"
EndSection
```

**REPLACE** the string inside ** ** with your Bus ID and save it to /etc/X11/xorg.conf

**Step 7:** Now we have to create some scripts according to our display manager. Since i'm using ParrotOS KDE which is LightDM, first i created file: /etc/xdg/autostart/optimus.desktop with the following content:

```
[Desktop Entry]
Type=Application
Name=Optimus
Exec=sh -c "xrandr --setprovideroutputsource modesetting NVIDIA-0; xrandr --auto"
NoDisplay=true
X-KDE-Autostart-Phase=DisplayServer
```

