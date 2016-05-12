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

1. 首先取得 Arch Linux 的 pacman.conf [備註1][1]

	$ wget 'https://git.archlinux.org/svntogit/packages.git/plain/trunk/pacman.conf.x86_64?h=packages/pacman' -O pacman.conf

2. 假設主機系統不是 Arch Linux，那表示我們並沒有對 pacman 作簽章，也就表示 pacman 會在驗證下載的安裝檔時出錯，所以我們得把 SigLevel 調低

	$ sed 's/Required DatabaseOptional/Never/g' -i pacman.conf

3. (選擇性)你也可以調整一下你要用的鏡像站 [備註2][2]

   把 `Include = /etc/pacman.d/mirrorlist` 

   改成 `Server = url://$repo/os/$arch`

4. 安裝 Arch Linux 系統

	$ pacstarp -GM -C /path/to/the/pacman.conf -d /path/to/the/root/of/the/vm base base-devel
    
5. 進入那個子系統

	$ sudo systemd-nspawn -D /rootpath
    
6. 作一些基本的 Arch Linux 調整吧

   * 調整時區
   * 調整語系 
   * 調整 pacman

		# pacman-key --init
		
		# pacman-key --populate archlinux
		
		# pacman -Syu

   * 新增使用者
   * 安裝你要用的圖形界面

		# pacman -S mate

傳遞影音
=======

截至目前為止，我們都還是只有終端機界面

1. 在主系統上開啟一個分離的 X 環境

	$ Xephyr -screen 1280x720 -glamor +xinerama -noreset :1
    
2. 使用新增的使用者登入你的容器

	$ sudo systemd-nspawn -D /rootpath --user=test --setenv=DISPLAY=:1
    
   **這裡的`:1`務必要對應 6. 最後面啟動的位置**

3. 在容器中使用正確的方式開啟 X 環境

	$ startx

   **各個桌面環境不盡相同，請自行參閱 ArchWiki 相關的篇章**

4. 賦予此系統發聲的能力（以 PulseAudio 為例）

    在主系統上以一般使用者執行

	$ pactl load-module module-native-protocol-unix socket=/path/to/socket
    
    這會讓主系統上的 PulseAudio 有一個新的 socket litsener
 
5. 使用正確的環境變數登入你的容器（當然，容器內也得安裝 PulseAudio ）

	$ sudo systemd-nspawn -D /rootpath --user=test --setenv=DISPLAY=:1 --bind=/socketpath --setenv=PULSE_SERVER=/socketpath
    
6. 這時候你就可以盡情的、無痕的，使用這個虛擬機ギリギリ愛了！

  ![示意圖](https://raw.githubusercontent.com/Brli/brli.github.io/master/_images/systemd-nspawn-example.jpg)

[1]: https://git.archlinux.org/svntogit/packages.git/plain/trunk/pacman.conf.x86_64?h=packages/pacman "上游的原始檔"
[2]: https://git.archlinux.org/svntogit/packages.git/plain/trunk/mirrorlist?h=packages/pacman-mirrorlist "上游的原始檔"
