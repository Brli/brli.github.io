---
layout: post
title:  "使用 SystemD Nspawn 建立完整的桌面環境 - 以 Arch 為例"
date:   2016-05-11 15:19:00 +0800
categories: systemd container
---

前言
====
硬體虛擬化已經行之有年，我們這帶的初心者大概通常都是由 Oracle VirtualBox 入坑
稍微有人帶路的或許是由 VMware Station 入手

然後到了最近 Docker(DevOps) 的興盛，以及各種「容器」技術的發展（概念上應該都源自於古早的 chroot）
有了 Xen、KVM、LXC 等
現在連惡名昭彰的 SystemD 也來軋一角，推出了內建的 `systemd-nsapwn` 服務

系統需求
=======

一個 systemd(ver>183) 系統
一個支援 bootstrap 的套件管理員（debootstrap, pacstarp, dnf, etc.）
假如你要跨系統，就得確認你能成功用主機系統的 toolchain 編出你要的軟體(例如在 Debian 系中編譯 pacman)

安裝過程
=======

1. 首先取得 Arch Linux 的 pacman.conf
[最新版](https://git.archlinux.org/svntogit/packages.git/plain/trunk/pacman.conf.x86_64?h=packages/pacman)

    wget 'https://git.archlinux.org/svntogit/packages.git/plain/trunk/pacman.conf.x86_64?h=packages/pacman' -O pacman.conf

2. 假設主機系統不是 Arch Linux，那表示我們並沒有對 pacman 作簽章，也就表示 pacman 會在驗證下載的安裝檔時出錯，所以我們得把 SigLevel 調低

    sed 's/Required DatabaseOptional/Never/g' -i pacman.conf
    
 2-1 你也可以調整一下你要用的鏡像站：
   把 `Include = /etc/pacman.d/mirrorlist` 改成 `Server = url://$repo/os/$arch`
   最新的鏡像站列表在[此](https://git.archlinux.org/svntogit/packages.git/plain/trunk/mirrorlist?h=packages/pacman-mirrorlist)

3. 安裝 Arch Linux 系統

    pacstarp -GM -C /path/to/the/pacman.conf -d /path/to/the/root/of/the/vm base base-devel
    
4. 進入那個子系統

    sudo systemd-nspawn -D /rootpath
    
5. 作一些基本的 Arch Linux 調整吧

 * 調整時區
 * 調整語系
 * 調整 pacman
 
    # pacman-key --init
    # pacman-key --populate archlinux
    # pacman -Syu
  
 * 新增使用者
 * 安裝你要用的圖形界面

6. 開啟一個分離的 X 環境

    Xephyr -screen 1280x720 -glamor +xinerama -noreset :1
    
7. 使用新增的使用者登入你的 container

    sudo systemd-nspawn -D /rootpath --user=test --setenv=DISPLAY=:1

8. 使用正確的方式開啟 X 環境

    $ startx

要注意的是，各個桌面環境不盡相同，請自行參閱 ArchWiki 相關的篇章

9. 
