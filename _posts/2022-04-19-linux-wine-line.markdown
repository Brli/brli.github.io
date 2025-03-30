---
layout: post
title:  "Linux 下使用 Wine 安裝 NAVER Line"
date:   2022-04-20 08:30:00 +0800
tag: line wine
---

TL;DR

  遇到 i18n 問題， `LC_ALL=en_US.UTF8` 下去就對了！

> 2022-08-10 不需要了，正常語系皆可

Brief Look
![wine-line.png](/assets/wine-line.png)

# 安裝環境

- OS: Arch Linux
- Wine: 2.19
- LINE: 5.3.3.1519

> 2020-09-30 update
```
  Wine: wine-staging 5.18
  LINE: 6.3.2.2338
```

> 2025-03-30
```
  Wine: wine 10.4-1
  LINE: 9.7.0.3556
```

> 2022-04-19 update
```
  Wine: wine-staging 7.6-1
  LINE: 6.7.4.2508
```

# 安裝流程

- 安裝 Wine and friends

> 2025-03-30 update package name
```
  pacman -S wine wine-gecko wine-mono winetricks
```

- 初始化 Wine

> 2025-03-30 update
```
  1. export WINEARCH=win64 # 使用 64 bit wine 環境
  2. `winecfg` 使用 Windows 11 環境 (此為預設值)
```

- 準備你的安裝檔

[LineInst.exe](https://desktop.line-scdn.net/win/new/LineInst.exe)

- 安裝

```
wine /path/to/downloaded/LineInst.exe
```

- 順利執行

# 客製化

1. winecfg 裡，可以設定成 120dpi 左右
2. 在 regedit 載入[中文化的 reg](https://gist.github.com/Brli/355f275a5967e82fa044470f6a85d2a0)
> 網路上有其他類似的東西
3. 如果有要使用桌面整合的話，要記得去 ~/Desktop 裡面改掉 LINE.desktop 的 Exec=
4. 或者，取消所有的桌面整合（安全性up？）
5. 刪掉其他的 Wine integration：

```
~/.local/share/applications
~/.local/share/mime
裡面應該會有蠻多 x-wine 或之類的檔案
那些都是 wine 初始化時建立的，假如不想要讓系統變得奇怪，可以選擇移除
```

# 小 bug

![wine-line-bug.png](/assets/wine-line-bug.png)

Bypass: 在 Winecfg 中「顯示(Graphics)」的部份，選擇「模擬一個桌面」

> 我通常都會選擇用 1280x720

# 回報上游

[WineHQ](https://appdb.winehq.org/objectManager.php?sClass=version&iId=35664)

# (10/29 Update) 後記

寫完才發現，這樣就沒藉口跟老闆說晚上無法 On-call 惹 QQ

真是一則以喜，一則以憂（？

另外一方面，也可能可以開始考慮從 Chrome App 過度來用原生（誤）的軟體

然後想到這篇文章：[Google 打算停止支援 Chrome App](https://www.theverge.com/2016/8/19/12555052/google-shutting-down-chrome-apps)

不過現在都2017下旬了，我的 Chrome 版還是活跳跳der，所以其實也很難講啦 ^^"

# (2020-09-30 Update)

- 修正環境
- 修正安裝流程
- 新增障礙排除

# (2022-04-19 Update)

- 更新版號
- 注意：不要使用 Windows 10 環境，不然會被更新至 7.x 版，有反破解

# (2022-08-10 Update)

- 修正環境：不用再使用特定語系
- 修正環境：使用 Windows 10 x64 系統
- 關於反破解：參考[這篇](/2022/08/25/linux-wine-line.html)

# (2025-03-30 Update)

- 修正環境：可以使用預設 Windows 11 x64 環境
