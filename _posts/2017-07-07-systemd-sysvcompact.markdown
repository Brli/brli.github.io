---
layout: post
title:  "SystemD SysV Compact"
date:   2017-07-07 11:00:00 +0800
---

This is a side note of my reboot failure today.


### The Package Layout

```bash
$ pacman -Ql systemd-sysvcompat
---
systemd-sysvcompat /usr/
systemd-sysvcompat /usr/bin/
systemd-sysvcompat /usr/bin/halt
systemd-sysvcompat /usr/bin/init
systemd-sysvcompat /usr/bin/poweroff
systemd-sysvcompat /usr/bin/reboot
systemd-sysvcompat /usr/bin/runlevel
systemd-sysvcompat /usr/bin/shutdown
systemd-sysvcompat /usr/bin/telinit
systemd-sysvcompat /usr/share/
systemd-sysvcompat /usr/share/man/
systemd-sysvcompat /usr/share/man/man8/
systemd-sysvcompat /usr/share/man/man8/halt.8.gz
systemd-sysvcompat /usr/share/man/man8/poweroff.8.gz
systemd-sysvcompat /usr/share/man/man8/reboot.8.gz
systemd-sysvcompat /usr/share/man/man8/runlevel.8.gz
systemd-sysvcompat /usr/share/man/man8/shutdown.8.gz
systemd-sysvcompat /usr/share/man/man8/telinit.8.gz
```

### The Concept

Those under /usr/bin are symlink to ../lib/systemd/systemd, and are to comply with SysV initial system (and for compatibility to bootloader like GRUB).

### The Discovery

Upon upgrade today, I found this package, and `pacman -Sii` shows that it wasn't any hard dependency of other packages. So, I uninstalled it.

Then, after reboot...dah!!! I was dropped to busybox rescue shell...

### The Rescue Steps

- Append `init=/sbin/bash` to GRUB and drop myself back to bash, and pacman -U from cache.
- Checked that /sbin/init is symlink to ../lib/systemd/systemd the real binary.
- Since the package is reinstalled, the system boots fine.
- Install the cache with `--force` again due to the prior reinstall wasn't finished with normal pacman process.
- Uninstall again the package.
- Edit /etc/default/grub and append init=/lib/systemd/systemd to LINUX_CMDLINE
- Generate new grub config
- Reboot to check life is fine again.

### Notes

After uninstall this package, one cannot use `poweroff` or `reboot` to control the power anymore.

We may need `systemctl poweroff`, `systemctl reboot` instead.
