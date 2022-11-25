---
layout: post
title:  "Host Misskey instance on Arch Linux"
date:   2022-11-22 19:40:00 +0800
tag: arch linux misskey container
---

The [Misskey Hub](https://misskey-hub.net/en/) provides a guide to [manually set up](https://misskey-hub.net/en/docs/install/manual.html) a self-host instance.

But the guide is for Debian-based distros.

The following guide is for [Arch Linux](https://archlinux.org) users

# Environment setup and discussion

I'm not a fan of ruin my daily driver, thus the built-in systemd-nspawn container is chosen.

You can safely skip this paragraph if you prefer other way of setting up the base OS around.

1. > #RTFM
   >
   > `man systemd-nspawn`, provides a nice example to `pacstrap` up a Arch Linux chroot.
   
   The hint here is to ensure you have base, base-devel, an editor, ie. vim, nano, emacs etc., git installed. `pacstrap -h` is also your good friend.

    ```terminal
    # pacstrap -C /tmp/pacman.conf -cKM /var/lib/machines/misskey base base-devel git vim
    ---
    -C: use alternative pacman.conf file, I choose to fetch the fresh conf from Arch's official git and edit the Include lines to Server = closest server, enable Parallel to 10
    -c: use cache in system, reduce bandwidth
    -K: use empty pacman keyring
    -M: do not copy host mirrorlist to guest, kinda reassure isolation
    ```

2. turn on the system, now you shold be in root of the container.

    ```
    # machinectl start misskey
    # machinectl shell misskey
    ```

3. set up network. Npsawn default to use virtual ethernet from systemd-networkd, and it relies on resolved as DNS resolver.

    `[host] systemctl start systemd-networkd`

    `[guest] systemctl enable systemd-networkd && systemctl start systemd-networkd`

    `[guest] systemctl enable systemd-resolved && systemctl start systemd-resolved`

4. the system is ready

# Misskey Setup and Installation Guide

### Now that let's follow the steps of the official guide, with modifications

1. `adduser` is Debian helper wrapped around, we only have `useradd` on Arch.

    > #RTFM
    > 
    > `useradd -h` is your good friend.
    >
    > check `useradd -D` is also good.

    `useradd -m misskey` should be enough

2. dependencies:
    - Nodejs: `nodejs-lts-gallium`
    - PostgreSQL: Arch doesn't provide old version, and the latest (14.5-4) works anyway, so `postgresql`
    - Redis: `redis`
    - Yarn: `yarn`
    - FFMpeg: `ffmpeg`
    - **NPM**: noted that yarn and nodejs do not pull in `npm`, thus you need to specify it.
    - **Node GYP**: `node-gyp` is used during yarn install/build process

    ```
    # pacman -S nodejs-lts-gallium postgresql redis yarn ffmpeg npm node-gyp
    ---
    Don't ask me why the scripts in package.json doesn't choose agnostic commands but specify npm. 
    ```

3. follow the steps

    - The alternative `git clone` command I use:

    ```
    git clone --depth=1 --single-branch --branch=12.119.0 https://github.com/misskey-dev/misskey.git
    ---
    It does reduce some bandwidth.
    12.119.0 is the latest release tag
    ```

    - You'll need to sync submodules, if not using `--recursive` option as official suggests.

    ```
    git submodule init
    git submodule update
    ```

    wait till `yarn install` drop errors, and fix them -- or done without errors, congratulations.

4. follow the steps

    hints on what to edit:
    
    - The host domain
    - The user name
    - The db name and password
    - ...read through the YAML and see what suits your need.

5. there shouldn't be errors during this part.

6. Some notes

   >#RTFM
   >
   >check on [Arch Wiki](https://wiki.archlinux.org/title/PostgreSQL) for initializing PostgreSQL

   - we don't have sudo, thus use `# su -l postgres` instead.
   - `[postgres]$ initdb --locale=C.UTF-8 --encoding=UTF8 -D /var/lib/postgres/data --data-checksums`
   - start/enable the service.
   - bypass other paragraph of Arch Wiki, as they will be done in Misskey manual.
   - continue with what Misskey guide says.

7. side notes
    - notes before test run: check that you've started redis service.
    - addition to recommended systemd service
    ```
    [Unit]
    ...
    After=postgresql.service redis.service
    ...
    ```
