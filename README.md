# Installing-Nvidia-drivers-on-ParrotOS-KDE

Before we begin, a couple of notes:

**[Orginal source, but for Kali](https://github.com/europa502/Installing-Nvidia-drivers-on-Kali-Linux)**

***USE AT YOUR OWN RISK***

**This tutorial is for official NVIDIA Driver**

**Step 1:** Verify you have hybrid graphics

```bash
root@milk:~# lspci | grep -E "VGA|3D"
01:00.0 VGA compatible controller: NVIDIA Corporation TU117M [GeForce GTX 1650 Mobile / Max-Q] (rev a1)
05:00.0 VGA compatible controller: Advanced Micro Devices, Inc. [AMD/ATI] Picasso (rev c1)
```
**Step 2:** Disable nouveau

```bash
echo -e "blacklist nouveau\noptions nouveau modeset=0\nalias nouveau off" > /etc/modprobe.d/blacklist-nouveau.conf
update-initramfs -u && reboot
```

**Step 3:** System will reboot and nouveau should be disabled. Verify if nouveau is disabled:

```bash
lsmod | grep -i nouveau
```
If shows nothing,means nouveau successfully disabled.

**Step 4:** Install nvidia driver 

I should install driver from [Nvidia's website](https://www.nvidia.ru/Download/index.aspx)

After downloading press **Ctrl+Alt+F3**
Enter your login and password

Enter command
```bash
sudo init 3
```
Enter password

Then again enter your login and password

Run installation
```bash
sudo sh ./NVIDIA-Linux-x86_64-470.94.run
```
**Step 5:** Now we have to find bus id of our nvidia card:

```bash
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

**Step 7:** Now we have to create some scripts according to our display manager. Since i'm using ParrotOS KDE which is LightDM: 

**1.** Create file: /etc/xdg/autostart/optimus.desktop with the following content:

```
[Desktop Entry]
Type=Application
Name=Optimus
Exec=sh -c "xrandr --setprovideroutputsource modesetting NVIDIA-0; xrandr --auto"
NoDisplay=true
X-KDE-Autostart-Phase=DisplayServer
```

**2.** Create script with any name and location and following content:

```bash
#!/bin/bash
xrandr --setprovideroutputsource modesetting NVIDIA-0
xrandr --auto
```
And set script executable permission

```bash
chmod +x <scriptname>
```

**3.** Edit **/etc/lightdm/lightdm.conf**, remove **#** before **display-setup-script=** and append path to script
```bash
...
display-setup-script=<path to script>
...
```

**Step 8:** Now reboot and you should be using Nvidia Driver. Verify if everything is ok: install mesa-utils if not previously installed.

```
apt-get install mesa-utils
```

```bash
root@milk:~# glxinfo | grep -i "direct rendering"
direct rendering: Yes
```

**Step 9:** Now you can install the cuda toolkits and drivers. The package nvidia-cuda-toolkit has been deprecated. You can install hashcat-nvidia package as an alternative.

```bash
apt install -y ocl-icd-libopencl1 nvidia-driver hashcat-nvidia
```

**Step 10:** Now that our system should be ready to go, we need to verify the drivers have been loaded correctly. We can quickly verify this by running the nvidia-smi tool.

```
root@milk:~# nvidia-smi
Fri Jan  7 18:45:44 2022       
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 470.94       Driver Version: 470.94       CUDA Version: 11.4     |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|                               |                      |               MIG M. |
|===============================+======================+======================|
|   0  NVIDIA GeForce ...  Off  | 00000000:01:00.0 Off |                  N/A |
| N/A   44C    P8     3W /  N/A |    151MiB /  3911MiB |      3%      Default |
|                               |                      |                  N/A |
+-------------------------------+----------------------+----------------------+
                                                                               
+-----------------------------------------------------------------------------+
| Processes:                                                                  |
|  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
|        ID   ID                                                   Usage      |
|=============================================================================|
|    0   N/A  N/A       893      G   /usr/lib/xorg/Xorg                 83MiB |
|    0   N/A  N/A      1288      G   /usr/bin/kwin_x11                  31MiB |
|    0   N/A  N/A      1346      G   /usr/bin/plasmashell               33MiB |
+-----------------------------------------------------------------------------+
```
***HAPPY END FOR RAIK199X***
