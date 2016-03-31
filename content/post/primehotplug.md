+++
title = "How To: pass GPU to VM and back without X restart"
description = ""
tags = [
    "vfio",
    "prime",
    "intel",
    "radeon"
]
date = "2016-03-31"
comments = true
+++

TLDR: If you use FOSS drivers with DRI3 you now can do this:

* Use GPU on Linux for any 3D applications.
* Pass it into VM and do anything with it.
* Once VM is shutdown use GPU on host again.

Everything without reboot and X server restart.
<!--more-->
Long story:
------------------
I'm was using GPU passthrough with VFIO for two years now and happy with it. Still for me as Linux nerd that like to play around with FOSS drivers there was huge downside that once GPU used in VM it's was impossible to use it on host before reboot.

One year ago I did several attempts to make it work on Ubuntu 14.04 with 3.19 kernel, but failed. Radeon kernel module load and unload didn't end up well after few retries leaving me with locked host. I tested many different ideas, but none work and best I could achieve is getting GPU work after X server restart. Other VFIO users reported the same.

Now I'm finally switch to Kubuntu 16.04 and after few days managed to get it working with stock everything and [ACS kernel patch](https://gist.github.com/cspicer/5e09d9ef382aa816435c). It's hard to say what exactly make it work, but suppose it's DRI3. My current setup looks that way:

* Host boot with UEFI. Used Legacy BOIS / CSM before.
* Host monitors connected to iGPU. One connected to dGPU as well for usage in VM.
* GPUs attached to radeon and intel kernel drivers. No blacklisting.
* Radeon and Intel DDX drivers is set use DRI3.

```
/etc/X11/xorg.conf.d/10-intel.conf:
Section "Device"
    Identifier "Intel Graphics"
    Driver "intel"
    Option "DRI" "3"
EndSection

/etc/X11/xorg.conf.d/20-radeon.conf:
Section "Device"
    Identifier "Radeon"
    Driver "radeon"
    Option "DRI3" "1"
EndSection
```

* DRI_PRIME=1 to use dGPU on host:

```
DRI_PRIME=1 glxinfo | grep OpenGL
```

* To use in VM dGPU unbind from Radeon and bind to VFIO:

```
echo "1002 6719" > /sys/bus/pci/drivers/vfio-pci/new_id
echo "0000:01:00.0" > /sys/bus/pci/devices/0000:01:00.0/driver/unbind
echo "0000:01:00.0" > /sys/bus/pci/drivers/vfio-pci/bind
echo "1002 6719" > /sys/bus/pci/drivers/vfio-pci/remove_id

echo "1002 aa80" > /sys/bus/pci/drivers/vfio-pci/new_id
echo "0000:01:00.1" > /sys/bus/pci/devices/0000:01:00.1/driver/unbind
echo "0000:01:00.1" > /sys/bus/pci/drivers/vfio-pci/bind
echo "1002 aa80" > /sys/bus/pci/drivers/vfio-pci/remove_id
```

* After VM shutdown dGPU pass it back to Radeon.
Most reliable way I found is device removal and PCI rescan:

```
echo 1 > /sys/bus/pci/devices/0000:01:00.0/remove
echo 1 > /sys/bus/pci/devices/0000:01:00.1/remove
echo 1 > /sys/bus/pci/rescan
```

So far I did several VM restarts switching GPU between host and VM and it's looks solid. I didn't tested everything, but Unigine Valley on both Windows VM and Linux as well as few games work stable. Hope others will be able to reproduce it with different hardware. 

If you interested to see what hardware I use as well as QEMU configuration [you can find it on gist](https://gist.github.com/ArseniyShestakov/dc152d080c65ebaa6781). Since I just finish major upgrade VM still using BIOS, but, but once everything settle down I'll switch to use EFI/OVMF. For general information about GPU passthrough you can check [Alex Williamson blog](http://vfio.blogspot.com/) and [vfio-users mailing list](https://www.redhat.com/mailman/listinfo/vfio-users).