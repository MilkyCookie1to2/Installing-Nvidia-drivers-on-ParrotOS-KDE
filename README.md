# Installing-Nvidia-drivers-on-ParrotOS-KDE

Before we begin, a couple of notes:

***USE AT YOUR OWN RISK***

**This tutorial is for official NVIDIA Driver**

**Step 1:** Verify you have hybrid graphics

```
bash
root@europa:~# lspci | grep -E "VGA|3D"
00:02.0 VGA compatible controller: Intel Corporation UHD Graphics 620 (rev 07)
01:00.0 3D controller: NVIDIA Corporation GP108M [GeForce MX150] (rev a1)
```
