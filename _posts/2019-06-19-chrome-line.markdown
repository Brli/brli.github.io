---
layout: post
title:  "把 Chrome 的 LINE 擴充抓出來玩"
date:   2019-06-19 11:30:00 +0800
---


繼 [Linux 下使用 Wine 安裝 NAVER Line]() 後許久，我後來還是回到原生的解法——使用 Chrome 商店的 LINE 擴充

但是要使用 LINE 的流程變成：

`開啟 Chromium→ 點選工具列上的 LINE 圖示→輸入密碼→使用 LINE`

有沒有辦法從桌面甚至是終端機上直接開啟 LINE 呀？

## 一切都是從一個小小的發現開始的

![Finshed download]({{ site.url }}/assets/Screenshot_20190619_114823.png)

原來現在的 LINE 擴充根本只是一個有限制視窗大小的網頁呀！

問題就變成了，要怎麼直接呼叫這個網頁呢？

畢竟，我們的瀏覽器都有

```bash
$ chromium [URL]
```

這樣子直接進入某網頁的功能呢！

## 萬用的 F12

![Devtool]({{ site.url }}/assets/Screenshot_20190619_114743.png)

好的，我們已經得到他在電腦上的位址惹

接下來就是盡情的~~調戲時間~~

### 終端機篇

只附上簡易的範例

```
~/.bashrc
---
alias line='chromium chrome-extension://ophjlpahpchlmihnnnihgmmeilfjmjjc/index.html'
```

### 桌面篇

1. `$ cp /usr/share/applications/chromium.desktop ~/.local/share/applications/line.desktop`
    修改 `Name`
    把`Exec=`那行改成`chromium chrome-extension://ophjlpahpchlmihnnnihgmmeilfjmjjc/index.html`
    把`Icon=`改成 Line 圖示的位置`$PROFILE/Extensions/ophjlpahpchlmihnnnihgmmeilfjmjjc/2.2.2_0/res/img/line_logo_128x128_on.png`
2. `$ cp /usr/share/applications/chromium.desktop ~/.local/share/applications/chromium.desktop`

    根據[規格書](https://standards.freedesktop.org/desktop-entry-spec/desktop-entry-spec-latest.html#extra-actions)
    
    先在`Actions=...;`最後面加上`new-line;`
    這裡可以用自己帥帥的名稱，跟下面的`[]`內名稱一致就好
    
   檔案最後面加上
     
     ```
  [Desktop Action new-line]
  Name=Open LINE Extension
  Name[zh_TW]=開啟 LINE 擴充功能
  Exec=/usr/bin/chromium chrome-extension://ophjlpahpchlmihnnnihgmmeilfjmjjc/index.html
  ```
  就能在 Chromium 圖示的右鍵選單裡發現直接開啟 Line 的選項囉