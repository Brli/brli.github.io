---
layout: post
title:  "From Virtualbox to QEMU"
date:   2019-05-02 16:10:00 +0800
---

![BSoD after update]({{ site.url }}/assets/virtualbox-windows7.png)

After recent update of Virtualbox to version `6.0.6-1`, my VM becomes unaccessible for no reason.

Out of curious, I turn to QEMU/KVM solution so as to assure stable access to Windows(R) machine as per school course required.

After brief reading of well-written [Arch wiki article about QEMU](https://wiki.archlinux.org/index.php/QEMU), I'm ready to make my hand dirty.

# Migrating From Virtualbox Hard Disk

I misinterpret the concept of QEMU, to think that it works like Virtualbox where we have a file describing the machine, and a harddisk file for storage.

Even though, I tried to boot to the vdi files where Windows7 is in...only to know it failed.

# Libvirt Usage

Well, it looks like Virtualbox UI, but in short, I can't make it work without efforts.

Thus, this idea is thrown away.


# Start From Scratch

Prepared the ISO from campus' info center, I start the journey in CLI.

1. First, I create a qcow2

    ```bash
    $ qemu-img create -f qcow2 Windows7.qcow2 40G
    ```

2. Boot to it

    ```bash
    $ qemu-system-x86_64 -m 4G -drive file=/path/to/qcow2 -cdrom /path/to/windows7.iso -vga qxl
    ```

3. Go through Windows installer wizard, and reboot.

Now we have a basic VM up and running.

# Virtio Boost

I heard of virtio a while ago, but don't know what it is until recently.

First, we need the driver disk for Windows guest. Fedora/RedHat has kindly provided it and we have it in AUR

```bash
$ yay -S virtio-win
```

The ISO is installed to `/usr/share/virtio/virtio-win.iso`, which you can easily verify via

```bash
$ pacman -Ql virtio-win
```

Follow the virtio paragraph of ArchWiki and things should work.

## Hard Disk

Archwiki has a thoroughly written [paragraph for this topic](https://wiki.archlinux.org/index.php/QEMU#Preparing_a_Windows_guest) on both fresh new virtual disk, and how to migrate from exist Windows installation to install the driver.

__Noted that, as of virtio-win-0.1.164.2-1, the storage driver for w7 amd64 is broken__

## Other Hardware - Graphic Card, Network Card

Please follow Arch Wiki

__Note that the `-netdev` is deprecated, use `-nic` instead__

## A Hard Attempt on iGVT

After settle down on QXL, I read [iGVT-g](https://www.reddit.com/r/VFIO/comments/8h352p/guide_running_windows_via_qemukvm_and_intel_gvtg/)

And after quite a few times of reinstallation/reboot, which costs me nearly one week, I failed to make it work on my i5-8250U laptop.

It all ended up `Unknown error` or `???? Bad Address`

So, I fallback to use qxl, and bye-bye, Aero effect.

## Wrap Up A Shell Script

1. Create a simple script under my home, rather `~/.bin` or `~/.local/bin`, etc. with content below

    ```(bash)
    #!/bin/bash

    qemu-system-x86_64 -m 4G \
                    -enable-kvm \
                    -nic user,model=virtio-net-pci \
                    -machine q35,accel=kvm \
                    -drive file=/path/to/your/windows.qcow2,format=qcow2,index=0,media=disk,if=virtio,aio=native,cache.direct=on,l2-cache-size=4M \
                    -cpu host,hv_relaxed,hv_spinlocks=0x1fff,hv_vapic,hv_time \
                    -usb -device usb-tablet \
                    -vga virtio -display sdl,gl=on "$@"
    ```

2. Enable [IOMMU](https://wiki.archlinux.org/index.php/QEMU#Enabling_IOMMU_(Intel_VT-d/AMD-Vi)_support)

    ```(bash)
    qemu-system-x86_64 ...
                -device intel-iommu \
                ...
    ```

3. With the fact that the window (X11 WM) is still grabbing keyboard input, a `Alt+F4` would accidently close the VM and causing a force poweroff. I turn to [SPICE](https://wiki.archlinux.org/index.php/QEMU#SPICE) solution

    ```(bash)
    qemu-system-x86_64 ...
                        -device virtio-serial-pci \
                        -device virtserialport,chardev=spicechannel0,name=com.redhat.spice.0 \
                        -chardev spicevmc,id=spicechannel0,name=vdagent \
                        -spice unix,addr=/path/to/store/spice.socket,disable-ticketing
                        ...
    ```

4. Install `virt-viewer` and daemonize the process

    ```(bash)
    qemu-system-x86_64 ...
                    -daemonize \
                    ...
    ```

    and can be connected via

    ```
    $ remote-viewer spice+unix:///path/to/spice.socket
    ```

5. Add some more pins to it

    ```(bash)
    export QEMU_AUDIO_DRV=pa
    export QEMU_PA_SINK=$(pacmd list-sinks | grep -e 'name:' | cut -d ' ' -f 2| sed -e 's,<,,' -e 's,>,,')
    export QEMU_PA_SOURCE=input

    qemu-system-x86_64 ...
                    -name a_cool_name \
                    -pidfile /path/to/vm.pid \
                    -rtc base=localtime \
                    -soundhw hda \
                    ...
    ```

Now it sings!

__Noted that I'm using straight forward sed lines, I believe there is better way to wrap it up__
