---
layout: post
title:  "Debug Naver LINE 在 Wine 下的問題"
date:   2022-08-25 22:10:00 +0800
tag: wine line

---

Recently, running windows applications without running it for a while left me with error as follows: (noted down for better reference for search engine.)

![wine_line_error](/assets/wine-line-error.png)

Window title: Themida

Message:

```
A debugger has been found running in your system.
Please, unload it from memory and restart your program.
```

The root cause is the kernel parameter `kernel.yama.ptrace_scope`.

Users may want to harden their system and set this value to higher than default `1`. Thus cause the trigger.

Read [Arch Wiki/Security/ptrace_scope](https://wiki.archlinux.org/title/Security#ptrace_scope) for more details.

!!Use `1` for wine!!