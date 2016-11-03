---
layout: post
title:  "Kate Syntax Highlight and Frameworks"
date:   2016-09-18 20:02:00 +0800
categories: kate kf5 frameworks
---
    At the time when KDE is proting to its version 5, applications are split and separated into libs and main binaries, kate is no exception.
    
    Originally (At KDE SC time), the source tree of kate contains main part, kate itself, and "katepart" as system/desktop integration (aka. being called when there is a demand to embeded text editor, eg. preview directly in Ark. display codes in KDevelop etc.)
    
    Now that things are different. Within the consideration of framework guidelines, kate project is being split into fundamental library `ktexteditor` as part of kf5(short of 'kde framework 5'), and the application `kate`. So are the functions and configurations available.
    
    One of the basic feature of a text editor is the syntax highlight, and of course that plugin cannot include all the file/text extension/format on Earth. One of is lacking is the support for PKGBUILD.
    
    This feature request is originated (not surprisingly) in Arch and a patch is submitted to kate at KDE SC and being adapt by (at least) KaOSx and Chakra, thus port over to modern kf5-plasma-application model as well.
    
    
    
In the testing period of [Chakra Project][0], we found that our [patch for PKGBUILD][1] highlight in Kate(pkgver=16.08.1-1) no longer work.

Within a small research, it seems to be something wrong with our ktexteditor(pkgver=5.26.0-2) package.

However, there is only **README.md** in the directory

```
$ ls -Al /usr/share/katepart5/syntax/
---
-rw-r--r-- 1 root root 0 18.09.2016 19:03 README.md
```
Where are all the highlight files lie in?

It turns out that they are all bundled in the library

`/usr/lib/libKF5TextEditor.so.5.26.0`

Which , by design, we can't modify it directly. (In theory we can, but not a good idea)

It should end here, since I know what I have to do is to get the source tarball, and check the CMakeLists.txt and do works in the directories.

[0]: https://chakralinux.org
[1]: https://chakralinux.org/code/core.git/commit/ktexteditor?id=b2efe36ca8a4944243f27fb59e57f2504faadb77
