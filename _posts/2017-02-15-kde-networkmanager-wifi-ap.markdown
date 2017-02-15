---
layout: post
title:  "Share Wifi Over NetworkManager Under Plasma"
date:   2017-02-15 14:30:00 +0800
---

### Preface

I found this quite annoying when there were no wifi AP around but I′d like to get my phone online through the laptop some time ago.

![failed to start]({{ site.url }}assets/wifi-1.png)

Later I realized that on a KDE/Plasma distro without specific preset for NetworkManager (such as Arch), one would find the following result when trying to start a wifi access point.

Thus, this tutorial.

**完整中文文章可以參考[這裡](https://chakra-zh.blogspot.tw/2015/11/kde-networkmanager-wifi.html)**

### Preparation

- ~~You need a wireless network card at hand~~

- Check that your wireless adapter supports AP mode

    _(package required: iw)_

```bash
$ iw list
---
     Supported interface modes:

    ...

    * AP

    ...
```

### Configuration

- Setup dnsmasq

    _(package required: dnsmasq)_

> Quoted the description from Arch
```
Lightweight, easy to configure DNS forwarder and DHCP server
```

```bash
/etc/dnsmasq.conf
---
listen-address=127.0.0.1
```

- Setup NetworkManager to use dnsmasq

```bash
/etc/NetworkManager/NetworkManager.conf 
---
[main]
dns=dnsmasq
```

- Restart NetworkManager.service

```bash
systemctl restart NetworkManager.service
```

### And, the rest is history (GUI part)

The GUI part is quite straightforward, if you still get trouble, please refer to the [Chinese version](https://chakra-zh.blogspot.tw/2015/11/kde-networkmanager-wifi.html) of this article for graphical step-by-step tutorial.

If it still bothers, don′t hesitate to leave me a message. I would try to complete this article with more recent snapshots.

### Reference

- ArchWiki

    - [Software access point](https://wiki.archlinux.org/index.php/software_access_point)

    - [DNSMasq and NetworkManager](https://wiki.archlinux.org/index.php/Dnsmasq#NetworkManager)
