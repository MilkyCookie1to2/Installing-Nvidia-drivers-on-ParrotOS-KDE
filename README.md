# Installing-Nvidia-drivers-on-ParrotOS-KDE

Before we begin, a couple of notes:

**[Orginal source, but for Kali](https://github.com/europa502/Installing-Nvidia-drivers-on-Kali-Linux)**

***USE AT YOUR OWN RISK***

**This tutorial is for official NVIDIA Driver**

**Step 1:** Verify you have hybrid graphics

```
root@europa:~# lspci | grep -E "VGA|3D"
00:02.0 VGA compatible controller: Intel Corporation UHD Graphics 620 (rev 07)
01:00.0 3D controller: NVIDIA Corporation GP108M [GeForce MX150] (rev a1)
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

Run unstallation
```
$ sudo sh ./NVIDIA-Linux-x86_64-470.94.run
```

