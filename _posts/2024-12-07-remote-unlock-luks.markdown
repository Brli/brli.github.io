---
layout: post
title:  "[HowTo] Unlock LUKS using systemd-tool and tinyssh"
date:   2024-12-07 21:00:00 +0800
tag: systemd initramfs systemd-boot
---

# Kudos

[@NixCraft](https://www.cyberciti.biz/security/how-to-unlock-luks-using-dropbear-ssh-keys-remotely-in-linux/) for the intro

[People@ArchWiki](https://wiki.archlinux.org/title/Dm-crypt/Specialties#Remote_unlocking_of_root_(or_other)_partition) for the detailed manuals

# Brief Information

Arch Wiki has a table of [Common Hooks](https://wiki.archlinux.org/title/Mkinitcpio#Common_hooks) for `mkinitcpio`, a tool to generate initramfs for Linux.

The above two articles have revealed a possibility to work out a remote mechanism to unlock LUKS without approaching to the box.

Check the three links out, they explains better than mine.

The following paragraphs would be just my side note that tells the differences.

# Files and Steps

## Mkinitcpio

1. Dependencies

    - tl;dr `# pacman -Syu mkinitcpio-systemd-tool tinyssh`

    - mkinitcpio-systemd-tool

    - tinyssh: as optional dependency of systemd-tool, who create ssh listener daemon during initialization.

2. (Tiny)SSH Key Configuration

    Put your ssh public key (the `authorized_keys` file) to `/etc/mkinitcpio-systemd-tool/config/authorized_keys` as per [README](https://github.com/random-archer/mkinitcpio-systemd-tool) says

3. mkinitcpio.conf

    Arch default to use `mkinitcpio` to generate initramfs, and its configuration file is `/etc/mkinitcpio.conf`, with alternative drop-in in `/etc/mkinitcpio.conf.d/<user_defined_name>.conf`

    ```shell
    /etc/mkinitcpio.conf.d/user.conf
    ---
    HOOKS=(base systemd systemd-tool autodetect microcode modconf kms keyboard sd-vconsole block sd-encrypt filesystems fsck)
    ```

    compare to the original line:

    ```shell
    /etc/mkinitcpio.conf
    ---
    ...
    HOOKS=(base udev autodetect microcode modconf kms keyboard keymap consolefont block filesystems fsck)
    ...
    ```

    As ArchWiki had mentioned,

    - switch `udev` to `systemd` hook

    - include the newly installed `systemd-tool` hook, in charge of tinyssh and network stack

    - switch `consolefont` to `sd-vconsole`.

    - required `sd-encrypt` for LUKS operation.

4. (Optional)

    - include driver of your wireless card into `MODULES=()`

    ```shell
    /etc/mkinitcpio.conf.d/wifi.conf
    ---
    MODULES+=(iwlwifi) # Intel Wireless chip for example
    ```

5. Generate the initramfs

    `# mkinitcpio -P`

## Bootloader

1. Check how dm-crypt required for systemd-based unlock, following is an example for `systemd-boot`

    ```
    /boot/loader/entries/arch.conf
    ---
    title   Arch Linux
    linux   /vmlinuz-linux
    initrd  /initramfs-linux.img
    options rd.luks.name="YOUR-DISK-UUID"=root rd.luks.options=discard root=/dev/mapper/root
    ```

## Unlock remotely

Now, you can remotely connect to the box even if it is just powering up:

```
$ ssh root@<ip-to-host>
secret>
$ ssh <user>@<ip-to-host>
<regular ssh session>