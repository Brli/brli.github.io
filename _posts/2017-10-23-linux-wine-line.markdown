---
layout: post
title:  "Linux 下使用 Wine 安裝 NAVER Line"
date:   2017-10-23 08:30:00 +0800
categories: line wine
---

TL;DR
  遇到 i18n 問題， `LC_ALL=en_US.UTF8` 下去就對了！

Brief Look
![wine-line.png](https://raw.githubusercontent.com/Brli/brli.github.io/master/_images/wine-line.png)

# 安裝環境

- OS: Arch Linux
- Wine: 2.19
- LINE: 5.3.3.1519

# 安裝流程

- 安裝 Wine and friends

```
pacman -S wine wine_gecko wine_mono
```

- 初始化 Wine

```
LC_ALL=en_US.UTF8 WINEARCH=win32 WINEPREFIX=/path/to/wine winecfg
```

- 準備你的安裝檔

LineInst.exe

- 安裝

```
LC_ALL=en_US.UTF8 wine LineInst.exe
```

> 可以在安裝過程發現，現在的 Line 是 Qt5 寫的應用程式

- 順利執行

- 修改 LINE.desktop

> 這裡用的是 Windows 7 容器
> 位置 /path/to/wine/drive_c/users/username/Desktop/LINE.desktop

  - 用編輯器打開
  - 在 `Exec=env` 後面加上 `LC_ALL=en_US.UTF8`
  - 儲存後關閉

# 客製化

1. winecfg 裡，可以設定成 120dpi 左右
2. 在 regedit 載入[中文化的 reg](https://gist.github.com/Brli/355f275a5967e82fa044470f6a85d2a0)
> 網路上有其他類似的東西

# 小 bug

![wine-line-bug.png](https://raw.githubusercontent.com/Brli/brli.github.io/master/_images/wine-line-bug.png)

Bypass: 在 Winecfg 中「顯示(Graphics)」的部份，選擇「模擬一個桌面」

# 回報上游

[WineHQ](https://appdb.winehq.org/objectManager.php?sClass=version&iId=35664)
