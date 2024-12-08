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

    - tl;dr `# pacman -Syu mkinitcpio-systemd-tool mkinitcpio-netconf tinyssh`

    - mkinitcpio-systemd-tool

    - tinyssh: as optional dependency of systemd-tool, who create ssh listener daemon during initialization.

    - mkinitcpio-netconf: network stack support for initcpio

2. (Tiny)SSH Key Configuration

    Put your ssh public key (the `authorized_keys` file) to `/etc/mkinitcpio-systemd-tool/config/authorized_keys` as per [README](https://github.com/random-archer/mkinitcpio-systemd-tool) says

3. mkinitcpio.conf

    Arch default to use `mkinitcpio` to generate initramfs, and its configuration file is `/etc/mkinitcpio.conf`, with alternative drop-in in `/etc/mkinitcpio.conf.d/<user_defined_name>.conf`

    ```shell
    /etc/mkinitcpio.conf.d/user.conf
    ---
    HOOKS=(base systemd systemd-tool autodetect microcode modconf kms keyboard sd-vconsole block sd-encrypt netconf filesystems fsck)
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

    - require `sd-encrypt` for LUKS operation.

    - require `netconf` to gain dhcp support

4. Include necessary driver for your ethernet device

    ```shell
    /etc/mkinitcpio.conf.d/eth.conf
    ---
    MODULES+=(r8169) # For Realtek Semiconductor Co., Ltd. RTL8125 2.5GbE Controller
    ```

5. (Optional) Include driver of your wireless device

    ```shell
    /etc/mkinitcpio.conf.d/wifi.conf
    ---
    MODULES+=(iwlmvm iwldvm iwlwifi) # Intel Wireless chip for example
    ```

5. Generate the initramfs

    `# mkinitcpio -P`

    example output

        ==> Building image from preset: /etc/mkinitcpio.d/linux.preset: 'default'
        ==> Using default configuration file: '/etc/mkinitcpio.conf'
        -> -k /boot/vmlinuz-linux -g /boot/initramfs-linux.img
        ==> Using drop-in configuration file: 'intel-wifi.conf'
        ==> Using drop-in configuration file: 'user.conf'
        ==> Starting build: '6.12.1-arch1-1'
        -> Running build hook: [base]
        -> Running build hook: [systemd]
        -> Running build hook: [systemd-tool]
        ==> WARNING: Possibly missing firmware for module: 'qat_420xx'
            replacing initramfs unit file: /usr/lib/systemd/system/initrd-shell.service
        ==> WARNING: Possibly missing firmware for module: 'softing_cs'
        ==> WARNING: Possibly missing firmware for module: 'bnx2x'
        ==> WARNING: Possibly missing firmware for module: 'bna'
        ==> WARNING: Possibly missing firmware for module: 'liquidio'
        ==> WARNING: Possibly missing firmware for module: 'mlxsw_spectrum'
        ==> WARNING: Possibly missing firmware for module: 'nfp'
        ==> WARNING: Possibly missing firmware for module: 'qed'
        ==> WARNING: Possibly missing firmware for module: 'adf7242'
        ==> WARNING: Possibly missing firmware for module: 'cfg80211'
        ==> WARNING: Possibly missing firmware for module: 'wcn36xx'
        ==> WARNING: Possibly missing firmware for module: 'at76c50x_usb'
        ==> WARNING: Possibly missing firmware for module: 'b43'
        ==> WARNING: Possibly missing firmware for module: 'b43legacy'
        ==> WARNING: Possibly missing firmware for module: 'ipw2100'
        ==> WARNING: Possibly missing firmware for module: 'ipw2200'
        ==> WARNING: Possibly missing firmware for module: 'p54pci'
        ==> WARNING: Possibly missing firmware for module: 'p54spi'
        ==> WARNING: Possibly missing firmware for module: 'p54usb'
        ==> WARNING: Possibly missing firmware for module: 'libertas_sdio'
        ==> WARNING: Possibly missing firmware for module: 'libertas_spi'
        ==> WARNING: Possibly missing firmware for module: 'usb8xxx'
        ==> WARNING: Possibly missing firmware for module: 'mwifiex_pcie'
        ==> WARNING: Possibly missing firmware for module: 'mwifiex_sdio'
        ==> WARNING: Possibly missing firmware for module: 'mwifiex_usb'
        ==> WARNING: Possibly missing firmware for module: 'mwl8k'
        ==> WARNING: Possibly missing firmware for module: 'mt7603e'
        ==> WARNING: Possibly missing firmware for module: 'plfxlc'
        ==> WARNING: Possibly missing firmware for module: 'rtl8723ae'
        ==> WARNING: Possibly missing firmware for module: 'rsi_sdio'
        ==> WARNING: Possibly missing firmware for module: 'rsi_usb'
        ==> WARNING: Possibly missing firmware for module: 'zd1211rw'
            remove existing /etc/tinyssh/sshkeydir
            convert openssh to tinysshd host key ed25519
        -> Running build hook: [autodetect]
        -> Running build hook: [microcode]
        -> Running build hook: [modconf]
        -> Running build hook: [kms]
        -> Running build hook: [keyboard]
        -> Running build hook: [sd-vconsole]
        -> Running build hook: [block]
        -> Running build hook: [sd-encrypt]
        -> Running build hook: [netconf]
        Created symlink '/tmp/mkinitcpio.2yvObK/root/etc/systemd/system/dbus-org.freedesktop.network1.service' → '/usr/lib/systemd/system/systemd-networkd.service'.
        Created symlink '/tmp/mkinitcpio.2yvObK/root/etc/systemd/system/multi-user.target.wants/systemd-networkd.service' → '/usr/lib/systemd/system/systemd-networkd.service'.
        Unit /tmp/mkinitcpio.2yvObK/root/usr/lib/systemd/system/systemd-networkd.service is added as a dependency to a non-existent unit multi-user.target.
        Created symlink '/tmp/mkinitcpio.2yvObK/root/etc/systemd/system/sockets.target.wants/systemd-networkd.socket' → '/usr/lib/systemd/system/systemd-networkd.socket'.
        Failed to enable auxiliary unit systemd-network-generator.service, ignoring.
        Failed to enable auxiliary unit systemd-networkd-wait-online.service, ignoring.
        Systemd is enabled, but there are no .initramfs files under /etc/systemd/network; exit
        -> Running build hook: [filesystems]
        -> Running build hook: [fsck]
        ==> Generating module dependencies
        ==> Creating zstd-compressed initcpio image: '/boot/initramfs-linux.img'
        -> Early uncompressed CPIO image generation successful
        ==> Initcpio image generation successful

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
ssh root@<host-domain-or-ip>

🔐  secret>

Connection to host-domain-or-ip closed by remote host.

Connection to host-domain-or-ip closed.

$ ssh <username>@<host-domain-or-ip> # Just how you get into the server

<regular ssh session>
```