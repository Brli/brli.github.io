---
layout: post
title:  "安裝 Chakra Goedel"
date:   2018-05-04 17:00:00 +0800
tag: chakra
---

## 前情提要

原本的電腦是 ASUS Zenbook

…不小心斷頭

![lappy](https://imgur.com/bNBSWFx.jpg)

因為剛好前輩在[經營客製化筆電](https://www.facebook.com/groups/1579037632209469/)，我剛好被打到

於是跟喜傑獅訂了一台新電腦（價位跟那台 ASUS 差不多，但是規格飆升許多）

且因為軟體方面的作業有點重，希望能穩定跑新軟體而不想自己幫社群當測試羊，所以轉移到 Arch

那…舊電腦雖然頭部很危險，但是折起來外接螢幕還是能跑（而且新筆電故意不配獨顯）

那就拿來安裝 Chakra 吧！順便底看看有什麼罷格

這次的計畫如下

- 安裝過程應該是直接掛上原本的 `/home` (btrfs)

- 然後 `/` 格式化 (ext4) 並重新安裝 Chakra

- `/dev/sda1` (fat32) 掛成 `/boot/efi`

其他的就看著辦囉

## 安裝過程以及小便利

![img]({{ site.url }}/assets/gGYPtg6.png)

Calameres 很聰明的幫你找出可能的家目錄，可以直接用滑鼠搞定分區跟掛載問題

![img]({{ site.url }}/assets/7C3fZUf.png)

![img]({{ site.url }}/assets/xitcPrV.png)

頓號好像怪怪的…有空來修

## 錯誤

![img]({{ site.url }}/assets/7iLYQZn.png)

主要是一些 `akonadi_resource` 的檔案，不曉得是不是 btrfs 壞檔？

反正先 `btrfs check` 一下囉

過後重新安裝，就沒問題了

## 重新啟動

__現在就算是 EFI 也預設是 GRUB 開機了呢！感謝團隊 讚嘆團隊__

## 更新

![img]({{ site.url }}/assets/MywTWz7.png)

怕.jpg

——附帶一提，這次安裝完後去看 mirrorlist，是已經正確最佳化的本國鏡像站了呢，完全不用擔心跨海下載慢吞吞

## 轉換到 Nvidia 閉源驅動

參考 Arch Wiki 的 [Optimus](https://wiki.archlinux.org/index.php/NVIDIA_Optimus) 頁面

可以直接安裝 `nvidia` 驅動程式，不需要動東動西

`# pacman -S nvidia`


按照說明依次修改

`/etc/X11/xorg.conf`

`/usr/share/sddm/scripts/Xsetup`

就好囉


__!!!然後請直接重新開機!!!__

__!!!不然畫面會變得很奇怪!!!__

## 安裝 Steam 平台

`# pacman -S steam`

## 移除不需要的語言包、字型檔

__可以服用 [font-null 包](https://gitlab.com/BrLi/brli-aur/-/tree/ttf-fonts-null)__

## 安裝 Firefox

`# pacman -S firefox-kde`


## 在地化

主要還是因為預設安裝了 `fcitx` 卻沒有 `export` 一些環境變數

所以安裝一下 `fcitx-chewing` 後

直接從 [gist](https://gist.github.com/Brli/ea1809e411fc0ee77168) 上抓下來存成 `~/.xprofile` 重新登入後就可以囉

## 後記——總時數

從下載 ISO、燒錄到隨身碟，到安裝完成大約一個半小時

跟 Windows 10 相比，不需要再安裝各種工具軟體以及 Office

跟其他 Linux 相比，不用再調整各個程式的設定以及桌面（當然不失自由度）
