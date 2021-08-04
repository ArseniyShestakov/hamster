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

I've been using a GPU passthrough with VFIO for two years now and I'm happy with it. Still, for me as a Linux nerd that likes to play around with FOSS drivers there was the huge downside that once the GPU was used in the VM it was impossible to use it on the host again without rebooting.

One year ago I made several attempts at making this work on Ubuntu 14.04 with the 3.19 kernel, but failed. Loading and unloading the Radeon kernel module a few times would leave me with a locked host. I tested many different ideas, but none worked and the best I could achieve was getting the GPU to work after an X server restart, other VFIO users reported similar results.

Now I've finally switched to Kubuntu 16.04 and after a few days I managed to get it working with stock everything and the [ACS kernel patch](https://gist.github.com/cspicer/5e09d9ef382aa816435c). It's hard to say what exactly made it work, but I suppose it is DRI3. My current setup looks like this:

* Host boot with UEFI. Used Legacy BIOS / CSM before.
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

So far I've restarted the VM several times, switching the GPU between host and VM and it looks solid. I haven't tested everything, but Unigine Valley on both Windows VM and Linux as well as a few games are stable. I hope others will be able to reproduce it with different hardware.

If you're interested in seeing what hardware I use as well as my QEMU configuration you can find it on [you can find it on gist](https://gist.github.com/ArseniyShestakov/dc152d080c65ebaa6781). Since I just finished a major upgrade the VM is still using BIOS, but once everything settles down I'll switch to using EFI/OVMF. For general information about GPU passthroughs you can check [Alex Williamson blog](http://vfio.blogspot.com/) and the [vfio-users mailing list](https://www.redhat.com/mailman/listinfo/vfio-users).

UPD1: Text patch by TGiFallen applied.